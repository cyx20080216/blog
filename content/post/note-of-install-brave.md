---
author: "cyx20080216"
title: "Ubuntu安装Brave踩坑"
date: 2022-07-07T15:59:00+08:00
lastmod: 2022-07-07T15:59:00+08:00
tags: ["笔记"]
categories: ["笔记"]
---
今天我想在我的Ubuntu上装Brave，于是找到[官方文档](https://brave.com/linux/#debian-ubuntu-mint)，按照要求执行如下命令

```bash
sudo apt install apt-transport-https curl

sudo curl -fsSLo /usr/share/keyrings/brave-browser-archive-keyring.gpg https://brave-browser-apt-release.s3.brave.com/brave-browser-archive-keyring.gpg

echo "deb [signed-by=/usr/share/keyrings/brave-browser-archive-keyring.gpg arch=amd64] https://brave-browser-apt-release.s3.brave.com/ stable main"|sudo tee /etc/apt/sources.list.d/brave-browser-release.list

sudo apt update

sudo apt install brave-browser
```

由于依赖问题，我装不上`curl`，但是可以用`wget`下载公钥，即换成

```bash
sudo wget -O /usr/share/keyrings/brave-browser-archive-keyring.gpg https://brave-browser-apt-release.s3.brave.com/brave-browser-archive-keyring.gpg
```

但在此之前，由于DNS污染，需要在`/etc/hosts`中加入
```
54.192.18.111 brave-browser-apt-release.s3.brave.com
```

一切就绪，执行`sudo apt update`时却显示`NO_PUBKEY A8580BDC82D3DC6C`

当时，我以为`apt`没有识别到之前下载的公钥，便找了各种办法，花了很长时间

最后才想到在[keys.openpgp.org](keys.openpgp.org)上按照ID找公钥，下载下来才发现之前的公钥根本就是错的

用上新的公钥，马上就成功了
