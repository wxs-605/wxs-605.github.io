# Vim 主题颜色配置


记录一下 ‘vim’ 更换主题颜色，这里我使用的是 onedark 主题。安装 onedark 之前需要安装'vim'的插件管理器，我用的是 'vim-plug'。

我的主机：云服务器
操作系统：Linux

1. 在用户目录下输入

```shell
curl -fLo ~/.vim/autoload/plug.vim --create-dirs \
    https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim
```

使用命令 `ls -a` 查看是否生成了 ‘.vimrc’ ，若没有，则创建一个  `touch .vimrc`

用  ‘vim’ 打开 ’.vimrc‘，输入下面的指令
```shell
call plug#begin('~/.vim/plugged') "该路径可以自己指定
Plug 'joshdick/onedark-vim' "输入你想安装的插件，这里是 onedark
call plug#end()
```
保存退出。


2. 进入 'vim' 终端，输入`:PlugInstall`，安装插件，若安装失败（出现一个×），则可以自己把 ’onedark‘ 下载到本地主机，

把'onedark.vim-main/autoload/onedar.vim' 移动到 '.vim/autoload'下，

把'onedark.vim-main/colors/onedar.vim' 移动到 '/usr/share/vim/vim82/colors/'下即可。

3. 用  'vim' 打开 用户目录下的 '.vimrc'，在最后添加

```shell
colorscheme onedark
```

这样就能成功运用 'onedark' 的主题配色啦。
