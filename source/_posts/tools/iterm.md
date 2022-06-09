---
title: mac终端美化-iTerm
cover: https://images.xianhu.wang/22060906.jpg
date:  2022-06-08 13:20:10
updated: 2022-06-08 13:20:10
tags:
  - mac终端美化
categories:
  - 工欲善其事、必先利其器
---
# mac终端美化-iTerm配置
{% note red 'fas fa-fan' disabled %} ## 前言{% endnote %}

{% note red  'fas -fa-fan' flat%} 
拥有一款高颜值的终端对于我们来说是很重要的，甚至可以增加我们对工具的热爱。因此，今天给大家推荐一款mac下高颜值终端工具-iTerm
{% endnote %}

{% note red 'fas fa-fan' disabled %} ## 效果{% endnote %}
{% image https://images.xianhu.wang/22060901.png %}

{% image https://images.xianhu.wang/22060902.png %}

{% note red 'fas fa-fan' disabled %} ## 安装iTerm{% endnote %}

```sh
https://iterm2.com/
```
{% note red 'fas fa-fan' disabled %} ## 配置iTerm{% endnote %}

{% image https://images.xianhu.wang/20220608143802.png %}

### 调整Status Bar
{% note red  'fas -fa-fan' flat%} 
将 iTerm2 自带 theme 修改为 Minimal 以达到顶栏沉浸式的效果.在Preference页面中点击Appearance选项卡，可以设置Status bar的位置，修改 Status bar location。
{% image https://images.xianhu.wang/22060903.png %}

可以在Profiles选项卡，Session页面最底部看到开启选项。Status bar enabled 选项，勾选上即可打开。点击右边的 Configure Status Bar 按钮可设置显示的内容。可以看到能显示的内容非常多，把上方要显示的内容拖动到下方 Active Components 区域即添加。
{% image https://images.xianhu.wang/22060904.png %}

{% image https://images.xianhu.wang/22060905.png %}

{% endnote %}
### 更改配色
{% note red  'fas -fa-fan' flat%} 
可以修改背景配置，推荐一款：https://draculatheme.com/iterm/
解压后更换 Preferences->Profiles->Color-Color Presets->Import 即可
{% endnote %}

### zsh主题配置
{% note red  'fas -fa-fan' flat%}
```sh
# 配置主题 默认robbyrussell
vim .zshrc
# 支持的主题
https://github.com/ohmyzsh/ohmyzsh/wiki/Themes
```
{% endnote %}

### 安装Oh-My-Zsh
{% note red  'fas -fa-fan' flat%}
切换shell为zsh
```sh
# 查看系统支持的 Shell 类型
cat /etc/shells
# 查看当前 Shell 类型
echo $SHELL
# 切换 Shell 类型
chsh -s /bin/zsh
```

下载地址（国内地址）：sh -c "$(curl -fsSL https://gitee.com/shmhlsy/oh-my-zsh-install.sh/raw/master/install.sh)" 

下载插件自动补全插件下载
```sh
 cd .oh-my-zsh下面的/custom/plugins目录
git clone https://github.com/zsh-users/zsh-autosuggestions.git

```
下载插件高亮插件下载
```sh
 cd .oh-my-zsh下面的/custom/plugins目录
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git

```
修改 ~/.zshrc配置
```sh
# 修改 ~/.zshrc
vim ~/.zshrc
# 设置ohmyz权限
ZSH_DISABLE_COMPFIX=true
# 修改 plugins=(git)
plugins=(git zsh-autosuggestions zsh-syntax-highlighting)

# 添加
source $ZSH/custom/plugins/zsh-autosuggestions/zsh-autosuggestions.zsh
source $ZSH/custom/plugins/zsh-syntax-highlighting/zsh-syntax-highlighting.zsh
# 使配置生效，执行
source ~/.zshrc
```
{% endnote %}
