# **利用BPB Panel 部署Cloudflare Workers 或 Pages 的代理面板项目，可以轻松建立免费 VPN，因CF对代理项目审查较严格，所以需要对源码进行自定义加密混淆，生成不同的混淆代码，绕过CF的限制。**

## 如何使用？ [视频教程](https://www.youtube.com/watch?v=iORZlu1a5Is)

## 准备工作
Github帐号；cloudflare账号；域名（收费或免费域名）

## Github部署
1. 新建github仓库:把`BPB panel`项目代码同步到仓库。

2. 配置github Actions: 在仓库目录下创建`.github/workflows`文件夹，并创建`Obfuscate.yml`文件。
   
```
name: Build Obfuscate BPB Panel

on:
  push:
    branches:
      - main
  schedule:
    - cron: "0 1 * * *"

permissions:
  contents: write

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - name: Check out the code
        uses: actions/checkout@v4

      - name: Set up Node.js
        uses: actions/setup-node@v4
        with:
          node-version: "latest"

      - name: Install dependencies
        run: |
          npm install -g javascript-obfuscator

      - name: Clone BPB workjs
        run: |
          wget -O origin.js https://raw.githubusercontent.com/bia-pain-bache/BPB-Worker-Panel/refs/heads/main/build/unobfuscated-worker.js

      - name: Obfuscate BPB worker js
        run: |
          javascript-obfuscator origin.js --output _worker.js \
          --compact true \
          --identifier-names-generator hexadecimal \
          --rename-globals true \
          --string-array true \
          --string-array-encoding 'base64' \
          --string-array-threshold 0.75 \
          --transform-object-keys true \
          --self-defending false \
          --simplify true

      - name: Commit changes
        uses: stefanzweifel/git-auto-commit-action@v5
        with:
          branch: main
          commit_message: ':arrow_up: update latest bpb panel'
          commit_author: 'github-actions[bot] <github-actions[bot]@users.noreply.github.com>'
          push_options: '--set-upstream'
```

## **Github仓库通过`Obfuscate.yml`自动下载BPB源代码，并执行混淆。**

## Cloudflare 部署
•创建`pages`：点击`workers`和`pages`，选择`pages`部署。连接`github`仓库，选择新建的项目仓库，然后点击部署。

•绑定自定义域名：以防止`pages`分配的域名被屏蔽。

•设置变量：`UUID`，`TR_PASS`，`PROXYIP`

### 变量说明

| 变量名 | 必填 | 备注 | 
|--|--|--|
| UUID |✔️| VLESS UUID |
| TR_PASS |✔️| Trojan Password |
| PROXYIP |✔️| Proxy IP 或 域名 (VLESS, Trojan) |
| SUB_PATH |❌| 订阅的' URI |
| FALLBACK |❌| 备用域名 (VLESS, Trojan) |
| DOH_URL |❌| Core DOH |

•绑定KV命名空间：名称随便但不能含有bpb等敏感词。

•重试部署pages

## BPB面板设置
•部署成功后，打开浏览器输入:`https://[自定义域名]或者你的项目地址,后面加上/panel检查是否能正常访问BPB面板`。

•修改BPB面板密码

•配置BPB面板参数

## 常用IP获取方式
cleanIP/优选IP：[地址1](https://www.wetest.vip/page/cloudflare/address_v4.html)    [地址2](https://ipdb.030101.xyz/bestcf/)   [地址3](https://mrxn.net/BESTCFDOMAIN)

PROXYIP：[点击进入一](https://ipdb.030101.xyz/bestproxy/)   [点击进入二](https://www.nslookup.io/domains/bpb.yousef.isegaro.com/dns-records/)

# 感谢

## 博 客：www.kekehub.com
## Github: https://github.com/kelekekou8

![演示](https://media.giphy.com/media/3o7abldj0b3rxrZUxW/giphy.gif)

![Build Status](https://img.shields.io/badge/build-passing-brightgreen)

## Stargazers over time
[![Stargazers over time](https://starchart.cc/bia-pain-bache/BPB-Worker-Panel.svg?variant=adaptive)](https://starchart.cc/bia-pain-bache/BPB-Worker-Panel)
