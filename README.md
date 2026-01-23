# Blog: 基于hugo构建的博客

使用 [hugo](https://github.com/gohugoio/hugo) 作为静态网站生成器, 选用 [DoIt](https://hugodoit.pages.dev) 主题, Github Actions + Github Pages 部署和托管.

# 相关信息

Blog 地址: https://golixp.github.io/

Github Pages 仓库: https://github.com/golixp/golixp.github.io

# 使用方式

**注意**: hugo 构建仓库和 GitHub Pages 托管仓库是两个独立的仓库. 以下操作都指 hugo 构建仓库中的操作.

## 本地预览网站

在 [hugo官网](https://github.com/gohugoio/hugo) 安装 hugo 程序.

在仓库目录运行命令行:

```bash
hugo server
```

更新命令:

```bash
# 清空本地缓存
hugo mod clean --all

# 尝试更新所有依赖
hugo mod get -u

# 整理配置文件
hugo mod tidy
```

默认路径为 `http://localhost:1313/`

## 部署

根据 Github Actions 配置, 推送代码到 master 分支时自动向目标存库指定分支部署, 参考配置如下:

```yaml
name: GitHub Pages Push

on:
  push:
    branches:
      - master

jobs:
  deploy:
    runs-on: ubuntu-22.04
    concurrency:
      group: ${{ github.workflow }}-${{ github.ref }}
      cancel-in-progress: true

    steps:
      - name: Checkout
        uses: actions/checkout@v4
        with:
          submodules: true
          fetch-depth: 0

      - name: Setup Hugo
        uses: peaceiris/actions-hugo@v3
        with:
          hugo-version: "latest"
          extended: true

      - name: Build
        run: hugo --minify --gc

      - name: Deploy
        uses: peaceiris/actions-gh-pages@v4
        if: ${{ github.ref == 'refs/heads/master' }}
        with:
          deploy_key: ${{ secrets.GH_PAGES_DEPLOY_KEY }}
          external_repository: golixp/golixp.github.io
          publish_branch: master
          publish_dir: ./public
```

**注意**: 要预先生成一对公私钥在 Github Pages 仓库配置 DEPLOY_KEY 放入 Public Key , 并在 Github Actions 执行仓库 Actions Secrets 中配置 GH_PAGES_DEPLOY_KEY 键值, 放入 Private Key .
