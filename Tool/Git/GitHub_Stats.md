# 在自己的Vercel服务器部署GitHub Stats

## 一、Fork 项目仓库

首先`fork`项目[github-readme-stats](https://github.com/anuraghazra/github-readme-stats)到自己的GitHub下，然后修改`/vercel.json`文件才能部署成功，不然会提示部署错误，需要付费升级Pro计划

```json
// 修改maxDuration为1~10，推荐修改为10
// redirects下的destination用于Vercel默认域名的重定向url
// 可修改为自己的项目地址，也可保留不改
{
  "functions": {
    "api/*.js": {
      "memory": 128,
      "maxDuration": 30
    }
  },
  "redirects": [
    {
      "source": "/",
      "destination": "https://github.com/anuraghazra/github-readme-stats"
    }
  ]
}
```

## 二、注册Vercel账号

进入[Vercel官网](https://vercel.com/)，使用GitHub进行注册

![image-20211110210002084](https://gitee.com/jxprog/PicBed/raw/master/md/2021/11/10-210003.png)

## 三、导入项目

找到`Import Git Repository`准备导入仓库

![image-20211110211010068](https://gitee.com/jxprog/PicBed/raw/master/md/2021/11/10-211432.png)

点击`Add GitHub Org or Account`，选择`Install`授权访问仓库

![image-20211110211211926](https://gitee.com/jxprog/PicBed/raw/master/md/2021/11/10-211444.png)

点击`Import`导入项目

![image-20211110211348767](https://gitee.com/jxprog/PicBed/raw/master/md/2021/11/10-211349.png)

## 四、部署项目

在GitHub里[生成](https://github.com/settings/tokens)的`token`令牌，生成令牌时`Select scopes`可全部留空

![image-20211110212440495](https://gitee.com/jxprog/PicBed/raw/master/md/2021/11/10-212441.png)

回到`Vercel`部署项目，选择不创建团队，点击跳过

![image-20211110211647546](https://gitee.com/jxprog/PicBed/raw/master/md/2021/11/10-211648.png)

项目名称可自定义，其他内容保持不变

![image-20211110212251577](https://gitee.com/jxprog/PicBed/raw/master/md/2021/11/10-212252.png)

添加名为`PAT_1`的环境变量，`Value`为刚才生成的`Token`

![image-20211110212537611](https://gitee.com/jxprog/PicBed/raw/master/md/2021/11/10-212539.png)

点击`deploy`，等待约一分钟即可完成

![image-20211110213352494](https://gitee.com/jxprog/PicBed/raw/master/md/2021/11/10-213354.png)

## 五、调用API

```markdown
[![Jxpro's GitHub stats](https://{domain}/api?username=jxpro&show_icons=true&theme=blueberry&include_all_commits=true)](https://github.com/Jxpro/github-readme-stats)
```

-   详细参数说明见 [readme.md](https://github.com/Jxpro/github-readme-stats/blob/master/docs/readme_cn.md)

## 六、自定义装X小技巧

-   修改`/src/calculateRank.js`下`Line 89`代码：

    ```javascript
    return { level, score: normalizedScore };
    // 修改此处可改变stats-card显示的评级(level)和空余部分(score)
    return { level:"S++", score: 4.6 };
    ```

-   修改`/src/cards/stats-card.js`下`totalIssues`，`totalPRs`

    ```javascript
     const STATS = {
        stars: {
          icon: icons.star,
          label: i18n.t("statcard.totalstars"),
          value: totalStars,
          id: "stars",
        },
        commits: {
          icon: icons.commits,
          label: `${i18n.t("statcard.commits")}${
            include_all_commits ? "" : ` (${new Date().getFullYear()})`
          }`,
          value: totalCommits,
          id: "commits",
        },
        prs: {
          icon: icons.prs,
          label: i18n.t("statcard.prs"),
    //    value: totalPRs,
          value: 3,
          id: "prs",
        },
        issues: {
          ...
        },
        contribs: {
          ...
        },
      };
    ```

-   修改`/src/common/utils.js`下`cache_second`代码：

    ```javascript
    const CONSTANTS = {
      THIRTY_MINUTES: 1800,
      TWO_HOURS: 3600,
      FOUR_HOURS: 3600,
      ONE_DAY: 86400,
    };
    ```

    可用于快速展示修改后的效果，但是修改满意后建议改回原来较大的数值
    
    不然每次访问都需要重新查询，构造图片，会影响效果
