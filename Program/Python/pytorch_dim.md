# 对pytorch中维度的理解

参考：

1.   https://zhuanlan.zhihu.com/p/354201988
2.   https://blog.csdn.net/qq_41375609/article/details/106078474

## 理解

以3-d tensor为例

```text
>> b = torch.tensor([
    [
      [1, 2, 3],
      [4, 5, 6]
    ],
    [
      [1, 2, 3],
      [4, 5, 6]
    ],
    [
      [1, 2, 3],
      [4, 5, 6]
    ],
])
>> print(b.shape)
torch.Size([3, 2, 3])
```

**dim = 0，**通过合并b[0] [0] [0], b[1] [0] [0], ..., b[x-1] [0] [0]得到第一个正确结果

```text
# dim 0
>> print(b[0])
tensor([[1, 2, 3],
        [4, 5, 6]])

>> print(b[1])
tensor([[1, 2, 3],
        [4, 5, 6]])

>> print(torch.sum(b, dim=0))
tensor([[ 3,  6,  9],
        [12, 15, 18]])
```

![动图](https://raw.githubusercontent.com/Jxpro/PicBed/master/md/2024/01-04-105747.webp)

**dim = 1，**通过合并b[0] [0] [0], b[0] [1] [0], ..., b[0] [y-1] [0]得到第一个正确结果

```text
# dim 1
>> print(b[0][0])
tensor([1, 2, 3])

>> print(torch.sum(b, dim=1))
tensor([[5, 7, 9],
        [5, 7, 9],
        [5, 7, 9]])
```

![动图](https://raw.githubusercontent.com/Jxpro/PicBed/master/md/2024/01-04-105800.webp)

**dim = 2，**通过合并b[0] [0] [0], b[0] [0] [1], ..., b[0] [0] [z-1]得到第一个正确结果

```text
# dim 2
>> print(b[0][0][0])
tensor(1)

>> print(torch.sum(b, dim=2))
tensor([[ 6, 15],
        [ 6, 15],
        [ 6, 15]])
```

![动图](https://raw.githubusercontent.com/Jxpro/PicBed/master/md/2024/01-04-105810.webp)
