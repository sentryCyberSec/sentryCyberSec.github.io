# SENTRYLAB WWW 主页

![Jekyll Build & Deploy](https://github.com/tuna/tuna.moe/workflows/Jekyll%20Build%20&%20Deploy/badge.svg)

Source code for [www-sentrylab-web](https://www.sentrylab.cn/).


## 运行 Demo


### 直接编译

本站使用 Jekyll 编写，并使用 babel 编译 ECMAScript6，因此必须安装 ruby >= 2.0 和 nodejs.

### For Centos
1.安装 nodejs
```
yum install nodejs
```
2.安装 ruby 2.2.4 and rubygems

Step 1: Install Required Packages
```
yum install gcc-c++ patch readline readline-devel zlib zlib-devel
yum install libyaml-devel libffi-devel openssl-devel make
yum install bzip2 autoconf automake libtool bison iconv-devel sqlite-devel
```
Step 2: Compile ruby 2.2.4 source code
```
wget -c https://cache.ruby-lang.org/pub/ruby/2.2/ruby-2.2.4.tar.gz
```
Step 3: Install rubygems
```
wget -c https://rubygems.org/rubygems/rubygems-2.4.8.tgz
ruby setup.rb
```
3. 安装 bundle 和 build
```
gem install bundle
gem install build
```
4. Fork mirrors source code

```
bundle install
jekyll build
```
其余LINUX发行版和MACOS差不多大同小异，参考链接：

[「笔记」：Jekyll for linux.服务器部署历程](https://www.sentrylab.cn/blog/2019/jekyll/in/linux/)

本人在MacOS BigSur v11.2下测试无任何问题。

之后在博客文件夹根目录下`jekyll serve -P 80` 或`bundle exec jekyll server -P 80`即可运行 demo.


### 食用方法

参考：

[# SentryLab「markdown」语法介绍&批注](https://about.sentrylab.cn/news/sentry-lab-markdown-usage/)
仅此一篇即可编写出漂亮的一篇博客

---
以上。
