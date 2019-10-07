---
title: Build A Chromium App
tags:
  - Chromium
url: 1079.html
id: 1079
categories:
  - technology
date: 2019-03-02 12:45:22
---

### prepare

1.  Shadowsocks家族vpn软件，亲测RocketVPN不可用。因为命令行连接谷歌域名必须要建立https连接，并且会失败。解决方案是命令行翻墙。至少git和curl需要翻墙。
    *   git config --global https.proxy localhost:1087
    *   在~/.bashrc或者~/.zshrc中设置curl全局代理。即尾行键入export https_proxy=http://localhost:1087
2.  充足的时间。因为chromium在Google Git上有12G+，clone非常慢，fetch --tags比较慢，另外sync非常慢。
3.  足够的HD空间。因为需要Xcode（解压后12G+），Chromium源码（12G+）。
4.  需要保证（不知道怎么保证但就是要）。如果macOS版本和Xcode版本存在兼容性问题，会卡在gn gen out/Default（第8步）。
5.  Install Xcode, launch it, accept the license agreement, and run `sudo xcode-select -s /path/to/Xcode.app` to continue.

### steps

> 官方教程不全，应该是编译最新版本且环境比较理想的情况下。因此整理一条在mac下编译M68版本且能跑通的操作序列。

\*\*\* 请务必确认终端已经科学上网。

1.  mkdir chromium && cd chromium
2.  fetch --nohooks chromium（后续再执行hooks）
3.  git fetch --tags
4.  git checkout -b release/68 68.0.3440.92
5.  cd ..
6.  gclient sync --with\_branch\_heads --jobs 16
7.  gclient runhooks （如果第6步提示Running hooks: 100% (64/64), done.则可省略此步骤）
8.  gn gen out/Default
9.  autoninja -C out/Default chrome

> 须确保每一条命令无Error。Warning可忽略。

[Chromium Build教程传送门](https://chromium.googlesource.com/chromium/src/+/master/docs/mac_build_instructions.md) [前人踩坑，后人乘凉](https://testerhome.com/topics/16226)

我卡在macOS SDK（10.14.3）和Xcode（10.2 Beta3）版本不兼容，夭折。
================================================