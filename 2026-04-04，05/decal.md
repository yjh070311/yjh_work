gui用户交互界面
shell用于与计算机交互的命令行界面 (CLI)。
bash,zsh,fish
fish友好
## shell commands
flag命令的变体
man帮助命令用法
cd change dir
mkdir 创建目录
cat ，concatemate链接所有并输出
head，read前十行
less ，read larger files
mv
cp-copy
rm-delete
ls -a隐藏文件 
pwd(print working directory)显示当前工作目录的绝对路径
/（slash）
less**分页查看文件内容**
/单词，寻找单词的位置，n迭代，q退出
grep 单词 文件，寻找
sed 替换
curl 查看
## vim
./，bash，执行脚本
./需要脚本有可执行权限（chmod +x）
 会根据脚本**第一行**的 shebang（`#!`）指定的解释器来执行
  例如：#!/bin/bash 会用 bash 执行，#!/usr/bin/env python3 会用 Python 执行
  bash功能丰富，强制执行
  echo输出文本，-e转义字符，-n不换行输出
  