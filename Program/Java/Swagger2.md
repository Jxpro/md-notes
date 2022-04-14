# Swagger 无法访问

## 问题描述

出现问题的**项目地址**：[邻家小铺](https://github.com/microapp-store/linjiashop)

原`swagger`版本：`2.2.2`

![image-20220414112937151](https://raw.githubusercontent.com/Jxpro/PicBed/master/md/new/2022-04-14-112938.png)

升级`swagger`版本到`2.9.2`

![image-20220414113029493](https://raw.githubusercontent.com/Jxpro/PicBed/master/md/new/2022-04-14-113030.png)

出现问题：Unable to infer base url. This is common when using dynamic servlet registration or **when the API is behind an API Gateway**. The base url is the root of where all the swagger resources are served. For e.g. if the api is available at http://example.org/api/v2/api-docs then the base url is http://example.org/api/.

![image-20220414113340228](https://raw.githubusercontent.com/Jxpro/PicBed/master/md/new/2022-04-14-113341.png)

## 求助网友

首先百度：**swagger Unable to infer base url**，找到一些文章，但对我没有作用，下面放着仅供参考：

https://www.freesion.com/article/26061412866/

https://www.jianshu.com/p/0c92ec5bb257

https://blog.csdn.net/qq_41345773/article/details/105693212

然后百度：**swagger 2.9.2 无法访问**，找到下面两篇文章，让有所启发：

https://blog.csdn.net/weixin_58276266/article/details/122544534

https://www.cnblogs.com/ahclub/p/15805083.html

最后回顾之前搜到的回答，发现关键问题所在，继续百度：**swagger 2.9.2 被拦截**，找到问题所在：

https://www.cnblogs.com/boboxing/p/15432281.html

https://www.freesion.com/article/2388755310/

https://blog.csdn.net/yang13676084606/article/details/110391214

## 分析问题

经过观察上述的这些回答，其实最关键的问题就是`swagger-ui`所需的**静态资源被拦截**了，只是网友们被拦截的方式不太一样，而这个项目的`swagger-ui`就是被`shiro`拦截了。

观察`2.2.2`版本的`swagger`请求的`ui`，路径具体如下：

![image-20220414114959966](https://raw.githubusercontent.com/Jxpro/PicBed/master/md/new/2022-04-14-115002.png)

再观察`2.9.2`版本的`swagger`请求的`ui`，发现**路径发生了变化**，具体如下：

![image-20220414115143661](https://raw.githubusercontent.com/Jxpro/PicBed/master/md/new/2022-04-14-115145.png)

最后查看其`shiro`配置，发现`swagger-resource/**`果然**不在拦截白名单内**：

![image-20220414115256938](https://raw.githubusercontent.com/Jxpro/PicBed/master/md/new/2022-04-14-115258.png)

同时可以看到`2.9.2`版本的错误响应码为`401`，表示**权限不足**，进一步说明资源被被拦截了。

## 解决问题

经过上述分析可知，`swagger`升级以后，其请求的资源路径也发生了变化，**我们需要修改相关过滤器的配置**，将`swagger-ui`所需的资源过滤，这里是使用的是`shiro`，在原基础上添加如下配置即可：

```java
filterRuleMap.put("/swagger/**", "anon");
filterRuleMap.put("/swagger-resources/**", "anon");
filterRuleMap.put("/v2/**", "anon");
filterRuleMap.put("/configuration/**", "anon");
filterRuleMap.put("/doc.html","anon");
```

