## 配置安装Fail2Ban
### 如果你之前安装过任何fail2ban,按照如下卸载
```
sudo service fail2ban stop
sudo cp -a /etc/fail2ban/ /etc/fail2ban_backup/
sudo apt remove --auto-remove fail2ban
sudo apt purge --auto-remove fail2ban
sudo rm -r /etc/fail2ban/
```
### 下载新版本
```
sudo wget https://github.com/fail2ban/fail2ban/archive/0.10.4.tar.gz
sudo tar xzf 0.10.4.tar.gz
sudo cd fail2ban-0.10.4/
sudo python setup.py install
```
### 放置ubuntu的文件
```
sudo cp files/bash-completion /etc/bash_completion.d/fail2ban
sudo cp man/*.5 /usr/share/man/man5
sudo cp man/*.1 /usr/share/man/man1
sudo wget -O /etc/logrotate.d/fail2ban https://raw.githubusercontent.com/fail2ban/fail2ban/debian/debian/fail2ban.logrotate
```
### 如上指令如果无法复制, 请使用如下指令
```
sudo vim /etc/logrotate.d/fail2ban
```
### 并且在 vim内写入
```
/var/log/fail2ban.log {
    weekly
    rotate 4
    compress
    # Do not rotate if empty
    notifempty
    delaycompress
    missingok
    postrotate
	  fail2ban-client flushlogs 1>/dev/null
    endscript
    # If fail2ban runs as non-root it still needs to have write access
    # to logfiles.
    # create 640 fail2ban adm
    create 640 root adm
}
```
### 执行剩余的
```
sudo cp build/fail2ban.service /lib/systemd/system/fail2ban.service
sudo systemctl enable fail2ban
sudo service rsyslog restart
sudo service fail2ban start
sudo service fail2ban status
```
### 复制如下代码更改
`/etc/fail2ban/jail.conf`
```
[sshd]
mode   = normal
enabled = true
port    = ssh
filter = sshd[mode=%(mode)s]
# 如果你上面定义了全局变量, 请修改全局变量
#findtime = 5m 		# 在5分钟之内
#MaxAuthTries = 3  	# 认证失败3次 
		# 动词词语不一致,详见 (https://superuser.com/questions/1180000/fail2ban-has-maxretry-of-3-but-i-see-authentication-failures-repeated-5-times)
#bantime = 7h  		# 封锁7小时
logpath = %(sshd_log)s
backend = %(sshd_backend)s
##sendmail-whois[name=SSH, dest=you@example.com, sender=fail2ban@example.com,sendername="Fail2Ban"] #发邮件
```
### 访问防火墙然后看看是否有封禁记录
```
sudo iptables -nL
```
### 也可以访问上面配置的`logpath`
```
tail -n 10 /var/log/f2b_log.log #我的路径
```
### 别的配置亦是如此

