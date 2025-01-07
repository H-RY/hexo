---
title: Linux的使用技巧
date: 2025-01-07 15:26:41
tags:
---

## Linux的使用技巧

### 查文件具体内容在哪

grep -rn 

-r 是递归的找 ，-n是打印那一行



### 将该目录下所有文件用户改为tina

sudo chown -R tina:tina * 



### ssh技巧

linux 下有个包叫 sshpass（不用输密码）
密钥改变了 IP 没变时，ssh-keygen -R 你要连的IP，然后重新 ssh
