# Nuxt 3 Minimal Starter

Look at the [Nuxt 3 documentation](https://nuxt.com/docs/getting-started/introduction) to learn more.

## Setup

Make sure to install the dependencies:

```bash
# npm
npm install

# pnpm
pnpm install

# yarn
yarn install

# bun
bun install
```

## Development Server

Start the development server on `http://localhost:3000`:

```bash
# npm
npm run dev

# pnpm
pnpm run dev

# yarn
yarn dev

# bun
bun run dev
```

## Production

Build the application for production:

```bash
# npm
npm run build

# pnpm
pnpm run build

# yarn
yarn build

# bun
bun run build
```

Locally preview production build:

```bash
# npm
npm run preview

# pnpm
pnpm run preview

# yarn
yarn preview

# bun
bun run preview
```

Check out the [deployment documentation](https://nuxt.com/docs/getting-started/deployment) for more information.

```yml
# 工作流的名称，如果省略，则使用当前文件名
name: GitHub Actions Build Docs

# 从工作流生成的工作流运行的名称，如果省略，则使用提交时的commit信息
run-name: Deploy by @${{ github.actor }}

# 触发部署的条件
on:
  # 每当 push 到 main 分支时触发部署
  push:
    branches:
      - main

# 当前流程要执行的任务，可以是多个。[my_first_job]就是一个任务
jobs:
  build-and-deploy:
    # 运行所需要的虚拟机环境
    runs-on: ubuntu-latest

    # 每个任务下的运行步骤，短横杠 - 表示一个步骤，从上至下依次执行。
    steps:
      # clone 该仓库的源码到工作流中
      - name: Checkout
        uses: actions/checkout@v3
        with:
          # "最近更新时间"等 git 日志相关信息，需要拉取全部提交记录
          fetch-depth: 0

      # 安装 Node 环境
      - name: Setup Node.js
        uses: actions/setup-node@v3
        with:
          # 选择要使用的 node 版本
          node-version: "20"

      # # 如果你的依赖是使用 pnpm 的，用这种
      # # 缓存 pnpm node_modules
      # - name: Cache dependencies
      #   uses: pnpm/action-setup@v2
      #   with:
      #     version: ^7.0

      # # 安装依赖 pnpm
      # - name: Install dependencies
      #   run: pnpm install

      # 如果你的依赖是使用 npm 的，用这种
      # 缓存 npm node_modules
      - name: Cache dependencies
        uses: actions/cache@v3
        with:
          path: ~/.npm
          key: ${{ runner.os }}-npm-cache-${{ hashFiles('**/package-lock.json') }}
          restore-keys: |
            ${{ runner.os }}-npm-cache-

      # 安装依赖 npm
      - name: Install dependencies
        # 如果没有命中缓存才执行 npm install
        if: steps.cache-deps.outputs.cache-hit != 'true'
        run: npm install

      # 如果你的依赖是使用yarn的，用这种
      # 缓存 yarn node_modules
      # - name: Cache dependencies
      #   uses: actions/cache@v3
      #   id: yarn-cache
      #   with:
      #     path: |
      #       **/node_modules
      #     key: ${{ runner.os }}-yarn-${{ hashFiles('**/yarn.lock') }}
      #     restore-keys: |
      #       ${{ runner.os }}-yarn-

      # 安装依赖 yarn
      # - name: Install dependencies
      #   # 如果没有命中缓存才执行 npm install
      #   if: steps.npm-cache.outputs.cache-hit != 'true'
      #   run: yarn --frozen-lockfile

      # 运行构建脚本
      - name: Run Build Script
        run: npm run generate

      # 部署到 GitHub Pages
      - name: Deploy to GitHub Pages
        # 此actions的官方文档 https://github.com/JamesIves/github-pages-deploy-action
        uses: JamesIves/github-pages-deploy-action@v4
        with:
          # 要部署的文件夹，必填，build 构建后的打包文件夹
          FOLDER: dist
          # 希望部署的分支，默认gh-pages
          BRANCH: gh-pages
          # 默认情况是不需要填的，如果您需要更多权限，例如部署到另一个存储库才需要填写
          # TOKEN_NAME 对应 GitHub Secrets 中设置的字段【配置详细步骤在文章下面内容中】，不要照搬
          # 仓库范围的访问令牌，可以将个人令牌的值存储在 GitHub Secrets 中
          TOKEN: ${{ secrets.TOKEN_NAME }}
          # 部署到GitHub的不同仓库 <用户名>/<仓库名>
          # 此选项必须配置了TOKEN才能正常执行
          # REPOSITORY-NAME: niezicheng/projectName

```
