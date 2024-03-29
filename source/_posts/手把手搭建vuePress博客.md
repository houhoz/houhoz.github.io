---
title: 手把手搭建vuePress博客
date: 2021-12-22 09:37:26
tags:
  - Vue
categories:
  - Note
cover:
feature: false
---

## 本地安装

[vuePress 官方文档](https://v1.vuepress.vuejs.org/zh)
首先使用官方的脚手架生成基本的项目结构

```bash
pnpm create vuepress blog
#下面选择blog 之后一直回车
```

项目结构

```md
├── README.md
├── blog
│   └── \_posts
│   ├── 2018-11-7-frontmatter-in-vuepress-2.md
│   ├── 2018-11-7-frontmatter-in-vuepress-3.md
│   ├── 2018-11-7-frontmatter-in-vuepress.md
│   ├── 2019-2-26-markdown-slot-2.md
│   ├── 2019-2-26-markdown-slot-3.md
│   ├── 2019-2-26-markdown-slot-4.md
│   ├── 2019-2-26-markdown-slot.md
│   ├── 2019-5-6-writing-a-vuepress-theme-2.md
│   ├── 2019-5-6-writing-a-vuepress-theme-3.md
│   ├── 2019-5-6-writing-a-vuepress-theme-4.md
│   └── 2019-5-6-writing-a-vuepress-theme.md
└── package.json
```

其中\_post 文件夹下面就是我们的博客，官方推荐我们使用日期作为文件名称

升级一下 package.json 版本

```bash
pnpm up

# package.json
"devDependencies": {
  "@vuepress/theme-blog": "^2.3.3",
  "vuepress": "^1.9.1"
}
```

## 部署

新建 deploy.sh

```bash
#!/usr/bin/env sh

# 确保脚本抛出遇到的错误
set -e

# 生成静态文件
npm run build

# 进入生成的文件夹
cd blog/.vuepress/dist

# 如果是发布到自定义域名
# echo 'www.example.com' > CNAME

git init
git add -A
git commit -m 'deploy'

# 如果发布到 https://<USERNAME>.github.io
git push -f git@github.com:<USERNAME>/<USERNAME>.github.io.git master:gh-pages

# 如果发布到 https://<USERNAME>.github.io/<REPO>
# git push -f git@github.com:<USERNAME>/<REPO>.git master:gh-pages

cd -

```

package.json 增加部署命令

```json
"scripts": {
  "dev": "vuepress dev blog",
  "build": "vuepress build blog",
  "deploy": "bash deploy.sh"
}
```

## github 静态 pages

新建仓库名称为 USERNAME.github.io 仓库

在仓库 Settings ---> pages

![img](/img/141406.png)
然后就可以访问 USERNAME.github.io
