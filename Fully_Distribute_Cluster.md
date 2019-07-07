
# Hadoop 完全分布式部署
## 准备三台设备
三台设备，同一局域网，hostname统一命名，配置JAVA环境

## 三台设备配置SSH环境
### SSH无密码登录
- 在home目录下找到当前用户对应的文件夹（root用户的文件夹在/root下）
- 找到.ssh/目录
- 执行下面命令
```shell
# 生成秘钥对（根据known-host文件中的公钥记录生成秘钥对）
ssh-keygen -t rsa
# 发送公钥到目标服务器
ssh-copy-id host
# 测试是否可以免密登录
ssh host
```

















