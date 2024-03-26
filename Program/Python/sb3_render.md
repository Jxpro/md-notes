# Stable Baselines3 渲染问题

[TOC]

## 问题描述

`OpenAI Gym`环境在创建时，通过指定参数`render_mode='human'`，然后在每次动作时`render`函数渲染画面，能够可视化训练或评估过程，示例代码如下

```python
import gym

env = gym.make('CartPole-v1', render_mode='human')
env.reset()
for _ in range(1000):
    # 每次渲染一帧动画
    env.render()
    action = env.action_space.sample()
    env.step(action)

env.close() 
```

`Stable Baselines3`中使用`make_vec_env`函数创建向量化环境，可以加速样本采集过程，但是存在的问题是，在创建环境时没有`render_mode`参数，导致调用`render`函数无法打开动画窗口，示例代码如下：

```python
from stable_baselines3 import PPO
from stable_baselines3.common.env_util import make_vec_env
from stable_baselines3.common.evaluation import evaluate_policy

env_name = "CartPole-v1"
env = make_vec_env(env_name, n_envs=1)

model = PPO("MlpPolicy", env, verbose=1, device=device)
model.learn(total_timesteps=10000)

obs = env.reset()
for _ in range(1000):
    # 没有动画窗口打开
    env.render()
    action, _states = model.predict(obs, deterministic=True)
    obs, rewards, dones, info = env.step(action)
env.close()
```

## 解决办法

>   注意，该方法只适合在`Stable Baselines3`中的向量化环境中使用
>
>   如果在`OpenAI Gym`环境中调用`env.render('human')`会报错：
>
>   TypeError: CartPoleEnv.render() takes 1 positional argument but 2 were given

通过查阅[stackoverflow](https://stackoverflow.com/questions/77532840/render-doesnt-work-for-gym-environment)上的回答，找到一个方法：

`Stable Baselines3`在创建向量化环境时虽然没有`render_mode`参数，但是可以在调用`env.render()`时指定参数`mode='human'`，达到同样的效果，示例代码如下

```python
from stable_baselines3 import PPO
from stable_baselines3.common.env_util import make_vec_env
from stable_baselines3.common.evaluation import evaluate_policy

env_name = "CartPole-v1"
env = make_vec_env(env_name, n_envs=1)

model = PPO("MlpPolicy", env, verbose=1, device=device)
model.learn(total_timesteps=10000)

obs = env.reset()
for _ in range(1000):
    # 唯一改动了下面一行，可以显示动画
    env.render(mode='human')
    action, _states = model.predict(obs, deterministic=True)
    obs, rewards, dones, info = env.step(action)
env.close()
```

## 源码分析

### make_vec_env

首先查看`make_vec_env`函数的代码（位于`stable_baselines3\common\env_util.py`）

>   下面代码摘要省略了部分注释和次要部分

```python
def make_vec_env(
    env_id: Union[str, Callable[..., gym.Env]],
    n_envs: int = 1,
    seed: Optional[int] = None,
    start_index: int = 0,
    monitor_dir: Optional[str] = None,
    wrapper_class: Optional[Callable[[gym.Env], gym.Env]] = None,
    env_kwargs: Optional[Dict[str, Any]] = None,
    vec_env_cls: Optional[Type[Union[DummyVecEnv, SubprocVecEnv]]] = None,
    vec_env_kwargs: Optional[Dict[str, Any]] = None,
    monitor_kwargs: Optional[Dict[str, Any]] = None,
    wrapper_kwargs: Optional[Dict[str, Any]] = None,
) -> VecEnv:    
	# ......(省略注释)
    env_kwargs = env_kwargs or {}
    vec_env_kwargs = vec_env_kwargs or {}
    monitor_kwargs = monitor_kwargs or {}
    wrapper_kwargs = wrapper_kwargs or {}
    assert vec_env_kwargs is not None  # for mypy

    def make_env(rank: int) -> Callable[[], gym.Env]:
        def _init() -> gym.Env:
            # For type checker:
            assert monitor_kwargs is not None
            assert wrapper_kwargs is not None
            assert env_kwargs is not None

            if isinstance(env_id, str):
                # if the render mode was not specified, we set it to `rgb_array` as default.
                kwargs = {"render_mode": "rgb_array"}
                kwargs.update(env_kwargs)
                try:
                    env = gym.make(env_id, **kwargs)  # type: ignore[arg-type]
                except TypeError:
                    env = gym.make(env_id, **env_kwargs)
            else:
                env = env_id(**env_kwargs)
                # Patch to support gym 0.21/0.26 and gymnasium
                env = _patch_env(env)
                
			# ......(省略部分代码)
            
        return _init

    # No custom VecEnv is passed
    if vec_env_cls is None:
        # Default: use a DummyVecEnv
        vec_env_cls = DummyVecEnv

    vec_env = vec_env_cls([make_env(i + start_index) for i in range(n_envs)], **vec_env_kwargs)
    # Prepare the seeds for the first reset
    vec_env.seed(seed)
    return vec_env
```

1.   我们可以在`33`行看到原生`gym`环境的创建过程`env = gym.make(env_id, **kwargs)`，在此之前，`31`行更新了环境参数`kwargs.update(env_kwargs)`，同时`30`行指定了默认的`render_mode`为`rgb_array`。
2.   可以发现，`env_kwargs`是一个字典变量，包含了`K-V`形式的环境参数，因此，虽然我们无法在`make_vec_env`中指定`render_mode`参数，但是可以构建一个环境参数字典`env_kwargs = {"render_mode": "human"}`，然后在创建向量化环境时指定`make_vec_env(env_name, n_envs=1, env_kwargs=env_kwargs)`。

但是这个方法并**不推荐**，因为打开窗口渲染动画会**严重降低训练速度**！！！

### render

接下来查看`render `函数的代码（位于`stable_baselines3\common\vec_env\base_vec_env.py`）

```python
def render(self, mode: Optional[str] = None) -> Optional[np.ndarray]:
    """
    Gym environment rendering

    :param mode: the rendering type
    """

    if mode == "human" and self.render_mode != mode:
        # Special case, if the render_mode="rgb_array"
        # we can still display that image using opencv
        if self.render_mode != "rgb_array":
            warnings.warn(
                f"You tried to render a VecEnv with mode='{mode}' "
                "but the render mode defined when initializing the environment must be "
                f"'human' or 'rgb_array', not '{self.render_mode}'."
            )
            return None

    elif mode and self.render_mode != mode:
        warnings.warn(
            f"""Starting from gymnasium v0.26, render modes are determined during the initialization of the environment.
            We allow to pass a mode argument to maintain a backwards compatible VecEnv API, but the mode ({mode})
            has to be the same as the environment render mode ({self.render_mode}) which is not the case."""
        )
        return None

    mode = mode or self.render_mode

    if mode is None:
        warnings.warn("You tried to call render() but no `render_mode` was passed to the env constructor.")
        return None

    # mode == self.render_mode == "human"
    # In that case, we try to call `self.env.render()` but it might
    # crash for subprocesses
    if self.render_mode == "human":
        self.env_method("render")
        return None

    if mode == "rgb_array" or mode == "human":
        # call the render method of the environments
        images = self.get_images()
        # Create a big image by tiling images from subprocesses
        bigimg = tile_images(images)  # type: ignore[arg-type]

        if mode == "human":
            # Display it using OpenCV
            import cv2

            cv2.imshow("vecenv", bigimg[:, :, ::-1])
            cv2.waitKey(1)
        else:
            return bigimg

    else:
        # Other render modes:
        # In that case, we try to call `self.env.render()` but it might
        # crash for subprocesses
        # and we don't return the values
        self.env_method("render")
    return None
```

1.   通过对`make_vec_env`函数的分析可以知道，如果我们不指定环境参数`env_kwargs `，则创建的`OpenAI Gym`环境的`render_mode`值为`rgb_array`。
2.   因此，如果我们不指定参数，直接调用`env.render()`，则`mode`会在`33`行时被赋值为`self.render_mode`，即默认的`rgb_array`，随后进入`40`行的`if`结构，然后进入`52`行的`else`部分，直接返回`bigimg`。
3.   如果我们在渲染时加上`mode`参数，使用`env.render(mode='human')`，则`mode`会在`33`行保持`human`这个值，随后进入`40`行的`if`结构，然后进入`46`行的`if`部分，由`Stable Baselines3`手动执行渲染。
4.   另外需要注意，虽然`make_vec_env`创建的向量化环境`vec_env`也有`render_mode`字段，但却不会与`OpenAI Gym`环境的`render_mode`字段保持同步，因此即使环境创建后设置`vec_env.render_mode='human'`，也只会导致代码进入`36`行的`if`结构（无论`mode`为何值，因此也会使`env.render(mode='human')`方法失效），然后调用`OpenAI Gym`环境的`render`方法，而其对应的`render_mode`值依旧为`rgb_array`，不会打开窗口渲染动画。

## 官方文档

>   -   to overcome the current Gymnasium limitation (only one render mode allowed per env instance, see [issue #100](https://github.com/Farama-Foundation/Gymnasium/issues/100)), we recommend using `render_mode="rgb_array"` since we can both have the image as a numpy array and display it with OpenCV. if no mode is passed or `mode="rgb_array"` is passed when calling `vec_env.render` then we use the default mode, otherwise, we use the OpenCV display. Note that if `render_mode != "rgb_array"`, you can only call `vec_env.render()` (without argument or with `mode=env.render_mode`).
>   -   methods and attributes of the underlying Gym envs can be accessed, called and set using `vec_env.get_attr("attribute_name")`, `vec_env.env_method("method_name", args1, args2, kwargs1=kwargs1)` and `vec_env.set_attr("attribute_name", new_value)`.

-   为了克服当前`OpenAI Gym`的限制（每个环境实例只允许一种渲染模式，参见[问题#100](https://github.com/Farama-Foundation/Gymnasium/issues/100)），我们建议使用`render_mode="rgb_array"`，因为我们既可以将图像作为`numpy`数组，又可以用`OpenCV`显示它。如果在调用`vec_env.render`时没有传递模式或传递了`mode="rgb_array"`，那么我们使用默认模式，否则，我们使用`OpenCV`显示。请注意，如果`render_mode != "rgb_array"`，你只能调用`vec_env.render()`（不带参数或者带`mode=env.render_mode`）。
-   可以通过`vec_env.get_attr("attribute_name")`、`vec_env.env_method("method_name", args1, args2, kwargs1=kwargs1)`和`vec_env.set_attr("attribute_name", new_value)`来访问、调用和设置底层`OpenAI Gym`环境的方法和属性。

## 总结

本文讨论了如何解决`Stable Baselines3`在使用向量化环境时，无法通过`render_mode='human'`参数来直接渲染动画窗口的问题。通常情况下，在使用`OpenAI Gym`环境进行强化学习训练或评估时，通过指定`render_mode='human'`参数，并在每次执行动作后调用`render`函数，可以可视化地展现出环境的状态变化。但在`Stable Baselines3`的`make_vec_env`函数创建的向量化环境中，由于缺少`render_mode`参数，直接调用`render`函数并不能打开动画窗口。

源码分析揭示了`make_vec_env`函数默认将`render_mode`设置为`rgb_array`，但通过传递一个包含`render_mode: 'human'`的`env_kwargs`字典给`make_vec_env`，可以在创建环境时指定渲染模式，尽管这能解决渲染问题，但是使用时并不推荐，因为开启动画窗口渲染会显著降低训练的速度。

解决方法是，虽然在`make_vec_env`函数创建环境时并不直接支持设置`render_mode`，但可以通过向`env.render`函数传递`mode='human'`参数来达到相似的效果，注意这是`Stable Baselines3`的自定义实现，而不是直接调用`OpenAI Gym`的方法。