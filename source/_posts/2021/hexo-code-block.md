---
title: 记录 hexo 生成代码块间隙过大的问题解决
date: 2021-07-08
tags: [hexo]
---

`npm` 更新了依赖包之后发现文章生成的代码块的间隙变得很大，排查之后发现是由于 `hexo` 版本导致，原来使用的是 ```4.2.1``` 版本，被更新成了 `4.0.0` 版本，修改根目录下 `package.json` 文件后问题解决，记录备忘。

<!--more-->

```json
{
     "name": "hexo-site",
     "version": "0.0.0",
     "private": true,
     "scripts": {
       "build": "hexo generate",
       "clean": "hexo clean",
       "deploy": "hexo deploy",
       "server": "hexo server"
    },
    "hexo": {
      "version": "4.2.1"
    },
    "dependencies": {
      "hexo": "^4.2.1",
      "hexo-deployer-git": "^2.1.0",
      "hexo-deployer-rsync": "^1.0.0",
      "hexo-filter-mermaid-diagrams": "^1.0.5",
      "hexo-generator-archive": "^1.0.0",
      "hexo-generator-baidu-sitemap": "^0.1.6",
      "hexo-generator-category": "^1.0.0",
      "hexo-generator-feed": "^2.2.0",
      "hexo-generator-index": "^1.0.0",
      "hexo-generator-json-content": "^4.1.6",
      "hexo-generator-sitemap": "^2.0.0",
      "hexo-generator-tag": "^1.0.0",
      "hexo-renderer-ejs": "^1.0.0",
      "hexo-renderer-kramed": "^0.1.4",
      "hexo-renderer-stylus": "^1.1.0",
      "hexo-server": "^1.0.0"
    }
  }
```

