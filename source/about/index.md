---
layout: page
title: 关于
meta:
  header: []
  footer: []
sidebar: []
valine:
  placeholder: 有什么想对我说的呢？
---

{% btns circle center %}
{% cell Linhk1606, , https://cdn.jsdelivr.net/gh/Linhk1606/blog-cdn@0.0.6.5/img/avatar.webp %}
{% endbtns %}


{% tabs aboutme %}

<!-- tab 本站配置 -->

* 网站源码由 [GitHub](https://github.com) 托管
* 博客主题是 [Volantis(原Material-X)](https://xaoxuu.com/wiki/volantis)
* 使用 [CloudFlare](https://cloudflare.com) DNS 解析
* 域名相关服务由 [Namecheap](https://www.namecheap.com) 支持
* 由 [Hexo](https://hexo.io/) Version 4.2.1 强力驱动
* [jsDelivr](https://www.jsdelivr.com) 提供免费高速的 CDN 服务
* 使用 [yuque-hexo](https://github.com/x-cold/yuque-hexo) 的语雀云端写作功能
* [腾讯云](https://cloud.tencent.com) , [LeanCloud国际版](https://leancloud.app) 提供云函数计算服务
* 使用 [GitHub Actions](https://help.github.com/en/actions) 自动部署

<!-- endtab -->

<!-- tab 开发工具一览 -->

##### Git

* [SourceTree](https://www.sourcetreeapp.com/)
* [GitHub Desktop](https://desktop.github.com/)

##### IDE

* [Qt Creator](https://www.qt.io/development-tools/)
* [VS 2019 Enterprise](https://visualstudio.microsoft.com/zh-hans/vs/)
* [CLion](https://www.jetbrains.com/clion/)

##### Editor

* [VS Code](https://code.visualstudio.com/)
* [Atom](https://atom.io/)
* [Sublime Text 3](https://www.sublimetext.com/)

<!-- endtab -->

<!-- tab 有用的小东西 -->

{% folding blue, Windows Terminal 终端美化 %}

```json settings.json
// To view the default settings, hold "alt" while clicking on the "Settings" button.
// For documentation on these settings, see: https://aka.ms/terminal-documentation
{
    "$schema": "https://aka.ms/terminal-profiles-schema",
    "defaultProfile": "{0caa0dad-35be-5f56-a8ff-afceeeaa6101}",
    "profiles":
    {
        "defaults":
        {
            "useAcrylic": true,
            "colorScheme": "Solarized Light"
        },
        "list":
        [
            {
                // Make changes here to the cmd.exe profile
                "guid": "{0caa0dad-35be-5f56-a8ff-afceeeaa6101}",
                "name": "cmd",
                "commandline": "cmd.exe",
                "hidden": false
            },
            {
                // Make changes here to the powershell.exe profile
                "guid": "{61c54bbd-c2c6-5271-96e7-009a87ff44bf}",
                "name": "Windows PowerShell",
                "commandline": "powershell.exe",
                "hidden": false
            },
            {
                "guid": "{58ad8b0c-3ef8-5f4d-bc6f-13e4c00f2530}",
                "hidden": false,
                "name": "Debian",
                "source": "Windows.Terminal.Wsl"
            },
            {
                "guid": "{b453ae62-4e3d-5e58-b989-0a998ec441b8}",
                "hidden": false,
                "name": "Azure Cloud Shell",
                "source": "Windows.Terminal.Azure"
            },
            {
                "guid": "{b453ae62-4e3d-5e58-b989-0a998ec441b7}",
                "hidden": false,
                "name": "Git Bash",
                "commandline": "E:\\Program Files\\Git\\bin\\bash.exe",
                "startingDirectory": "D:\\linhk\\blog-source",
                "icon": "E:\\Program Files\\Git\\mingw64\\share\\git\\git-for-windows.ico"
            }
        ]
    },
    // Add custom color schemes to this array
    "schemes": [],
    // Add any keybinding overrides to this array.
    // To unbind a default keybinding, set the command to "unbound"
    "keybindings":
    [
        // Copy and paste are bound to Ctrl+Shift+C and Ctrl+Shift+V in your defaults.json.
        // These two lines additionally bind them to Ctrl+C and Ctrl+V.
        // To learn more about selection, visit https://aka.ms/terminal-selection
        {
            "command": {
                "action": "copy",
                "singleLine": false
            },
            "keys": "ctrl+c"
        },
        {
            "command": "paste",
            "keys": "ctrl+v"
        },
        // Press Ctrl+Shift+F to open the search box
        {
            "command": "find",
            "keys": "ctrl+shift+f"
        },
        // Press Alt+Shift+D to open a new pane.
        // - "split": "auto" makes this pane open in the direction that provides the most surface area.
        // - "splitMode": "duplicate" makes the new pane use the focused pane's profile.
        // To learn more about panes, visit https://aka.ms/terminal-panes
        {
            "command": {
                "action": "splitPane",
                "split": "auto",
                "splitMode": "duplicate"
            },
            "keys": "alt+shift+d"
        }
        // {
        //     "command": {
        //         "action": "newTab",
        //         "commandline": "%comspec% /k D:\\vs2019\\Common7\\Tools\\VsDevCmd.bat",
        //         "startingDirectory": "D:\\code"
        //     },
        //     "keys": "ctrl+0"
        // }
        // 设置了 ctrl+0 为快捷键 调用vs2019 脚本 然后再打开VScode 进行编译
        //%comspec% /k "C:\\apps\\vsIDE\\Common7\\Tools\\VsDevCmd.bat"  %comspec% /k "D:\\vs2019\\Common7\\Tools\\VsDevCmd.bat"
        //          { "command": { "action": "splitPane", "split": "auto", "profile": "profile1", "commandline": "foo.exe" }, "keys": "ctrl+a" },
        //          { "command": { "action": "newTab", "profile": "{00000000-0000-0000-0000-000000000000}", "startingDirectory": "C:\\foo" }, "keys": "ctrl+b" },
        //          { "command": { "action": "newTab", "index": 0, "tabTitle": "bar", "startingDirectory": "C:\\foo", "commandline": "foo.exe" }, "keys": "ctrl+c" }
    ]
}
```

{% endfolding %}

{% folding cyan, 本站 GitHub Actions 博客自动部署 %}

{% codeblock lang:yaml https://github.com/Linhk1606/blog-source/blob/master/.github/workflows/main.yml %}
# GitHub Actions Hexo博客自动部署 + Yuque Hexo 云端写作
# @author: Linhk1606 Linhk1606@outlok.com
# workflow name
name: Deploy To Github Pages

# 当有 push 到仓库和外部触发的时候就运行
on: [push, repository_dispatch]

# YUQUE_TOKEN
# Github_SSH_PRIVATE_KEY
# HEXO_ALGOLIA_INDEXING_KEY
jobs:
  deploy: 
    name: Deploy Hexo Public To Pages
    runs-on: ubuntu-latest 
    env:
      TZ: Asia/Shanghai    

    steps:
    # check it to your workflow can access it
    # from: https://github.com/actions/checkout
    - name: Checkout Repository master branch
      uses: actions/checkout@master 

    # from: https://github.com/actions/setup-node  
    - name: Setup Node.js 10.x 
      uses: actions/setup-node@master
      with:
        node-version: "10.x"
    
    
    # from https://github.com/x-cold/yuque-hexo
    - name: Setup Yuque & Hexo Settings
      env:
        YUQUE_TOKEN: ${{ secrets.YUQUE_TOKEN }}
      run: |
        npm install hexo-cli -g
        npm install yuque-hexo -g
        npm install
        npm run yuque

    - name: Setup Algolia Search
      env:
        HEXO_ALGOLIA_INDEXING_KEY: ${{ secrets.HEXO_ALGOLIA_INDEXING_KEY }}
      run: hexo algolia

    - name: Generate
      run: npm run start

    # from https://github.com/peaceiris/actions-gh-pages    
    - name: Deploy
      uses: peaceiris/actions-gh-pages@v3
      with:
        deploy_key: ${{ secrets.Github_SSH_PRIVATE_KEY }}
        external_repository: Linhk1606/Linhk1606.github.io
        publish_branch: master
        publish_dir: ./public
        commit_message: ${{ github.event.head_commit.message }}

{% endcodeblock %}

{% endfolding %}

{% folding green, GitHub 学生福利 %}

{% btns circle %}
{% cell 查看更多, https://education.github.com/pack, fab fa-github %}
{% endbtns %}

{% endfolding %}

<!-- endtab -->

{% endtabs %}

-----
