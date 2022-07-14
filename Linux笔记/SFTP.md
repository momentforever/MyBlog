<!--
 * @Author: moment-forever lzt1141@126.com
 * @Date: 2022-07-08 20:38:30
 * @LastEditors: moment-forever lzt1141@126.com
 * @LastEditTime: 2022-07-11 16:24:24
 * @FilePath: /workspace/Studynote/Linux笔记/SFTP.md
 * @Description: 这是默认设置,请设置`customMade`, 打开koroFileHeader查看配置 进行设置: https://github.com/OBKoro1/koro1FileHeader/wiki/%E9%85%8D%E7%BD%AE
-->
# SFTP

## 基础说明
通过命令行方式传输文件。

Linux和Win下sftp命令相同。

## 基础命令

```shell
# 登录
sftp [user]@[ip]

# 打印远程当前目录
lpwd

# 打印本地当前目录
pwd

# 上传
# -P -p 维护适当的权限和访问时间
# -r 传输文件夹(递归)
put [-Ppr] [本地文件] [远程文件] 

# 下载
get [-Ppr] [远程文件] [本地文件]

# 退出
exit
```

## 其他

### 删除证书

```shell
# IP不变的情况下，重装系统，需清除证书
ssh-keygen -f "~/.ssh/known_hosts" -R [IP]
```