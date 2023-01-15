---
title: 开发备忘录
tags: Cache
date: "2022-12-01 00:00:00"
---

## Windows 下 SSH / Git 配置代理

在 `C:\Users\reqwey\.ssh` 目录下新建 `config` 文件，并写入以下内容：(其中 6666 是代理软件的端口）

```config
#Windows用户，注意替换你的端口号和connect.exe的路径
ProxyCommand "D:\Program Files\Git\mingw64\bin\connect" -S 127.0.0.1:6666 -a none %h %p

#MacOS用户用下方这条命令，注意替换你的端口号
#ProxyCommand nc -v -x 127.0.0.1:6666 %h %p

Host github.com
  User git
  Port 22
  Hostname github.com
  # 注意修改路径为你的路径
  IdentityFile "C:\Users\reqwey\.ssh\id_rsa"
  TCPKeepAlive yes

Host ssh.github.com
  User git
  Port 443
  Hostname ssh.github.com
  # 注意修改路径为你的路径
  IdentityFile "C:\Users\reqwey\.ssh\id_rsa"
  TCPKeepAlive yes
```

## Socket.io server 4.x 遍历已连接的所有 Socket

```js
for (let [id, socket] of io.sockets.sockets) {}
```

其中 `io.sockets.sockets` 为 `Map`。单独取一个需要使用 `.get(socket_id)` 语法

## 国内 Electron 安装失败

* 使用 `yarn install` 而不是 `npm install`
* 修改 `node_modules/electron/install.js` 内容如下

```diff
downloadArtifact({
  version,
  artifactName: 'electron',
  force: process.env.force_no_cache === 'true',
  cacheRoot: process.env.electron_config_cache,
  checksums: process.env.electron_use_remote_checksums ? undefined : require('./checksums.json'),
+ mirrorOptions: {
+   mirror: 'https://cdn.npmmirror.com/binaries/electron/'
+ },
  platform,
  arch
}).then(extractFile).catch(err => {
```
