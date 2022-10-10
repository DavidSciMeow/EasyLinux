## 创建如下服务

```shell
vim /etc/systemd/system/journal-autoclean.service
```
## 写入如下内容
```shell
[Unit]
Description=Journalctl-AutoCleanJournal
#After=
[Service]
Type=simple
User=root
ExecStart=journalctl --vacuum-size=100M #清理日志,仅剩余100M大小
#ExecStart=journalctl --vacuum-time=2d #清理日志,清除两天前所有日志
[Install]
WantedBy=multi-user.target
```
## 创建后尝试运行
```shell
sudo service journal-autoclean start
```
## 使用status查看运行结果
```
journal-autoclean.service - Journalctl-AutoCleanJournal
     Loaded: loaded (/etc/systemd/system/journal-autoclean.service; disabled; vendor preset: enabled)
     Active: inactive (dead)
TriggeredBy: ● journal-autoclean.timer

Oct 10 23:41:25 electronicute journalctl[193428]: Deleted archived journal /var/log/journal/b88300d8b20045b9b2f2a7c8ee1f0c5f/system@fd2f1af1ee6748cfa72c4653dc6d8df5-00000000001708ff-0005e9a87bf1cdc1.>
Oct 10 23:41:25 electronicute journalctl[193428]: Deleted archived journal /var/log/journal/b88300d8b20045b9b2f2a7c8ee1f0c5f/user-1000@711ac20ef5f04d828f07d9ad77014e95-000000000017090c-0005e9a87bf3dd>
Oct 10 23:41:25 electronicute journalctl[193428]: Deleted archived journal /var/log/journal/b88300d8b20045b9b2f2a7c8ee1f0c5f/system@fd2f1af1ee6748cfa72c4653dc6d8df5-0000000000192c52-0005e9ba7c8c50da.>
Oct 10 23:41:25 electronicute journalctl[193428]: Deleted archived journal /var/log/journal/b88300d8b20045b9b2f2a7c8ee1f0c5f/user-1000@711ac20ef5f04d828f07d9ad77014e95-0000000000192d4b-0005e9bab1c35d>
Oct 10 23:41:25 electronicute journalctl[193428]: Deleted archived journal /var/log/journal/b88300d8b20045b9b2f2a7c8ee1f0c5f/system@fd2f1af1ee6748cfa72c4653dc6d8df5-00000000001a6670-0005e9fda29d0245.>
Oct 10 23:41:25 electronicute journalctl[193428]: Deleted archived journal /var/log/journal/b88300d8b20045b9b2f2a7c8ee1f0c5f/user-1000@37aaefbe0b724f9a97cca0e1fa2dd1df-00000000001a69b1-0005e9fda3a809>
Oct 10 23:41:25 electronicute journalctl[193428]: Deleted archived journal /var/log/journal/b88300d8b20045b9b2f2a7c8ee1f0c5f/system@fd2f1af1ee6748cfa72c4653dc6d8df5-00000000001bcae9-0005ea4c0a3af6a0.>
Oct 10 23:41:25 electronicute journalctl[193428]: Vacuuming done, freed 408.0M of archived journals from /var/log/journal/b88300d8b20045b9b2f2a7c8ee1f0c5f.
Oct 10 23:41:25 electronicute journalctl[193428]: Vacuuming done, freed 0B of archived journals from /var/log/journal.
Oct 10 23:41:25 electronicute systemd[1]: journal-autoclean.service: Deactivated successfully.
```
## 创建定时器
```shell
sudo vim /etc/systemd/system/journal-autoclean.timer
```
## 写入如下内容
```shell
[Unit]
Description=journalctl-autocleantimer

[Timer]
#OnBootSec=10min
#OnUnitActiveSec=1h
OnCalendar=Mon *-*-* 00:00:00 #每周清理一次
# 每小时 → *-*-* *:00:00
# 每天 → *-*-* 00:00:00
# 每个月 → *-*-01 00:00:00
# 每周 → Mon *-*-* 00:00:00
# 每年 → *-01-01 00:00:00
# 每季度 → *-01,04,07,10-01 00:00:00
# 每半年 → *-01,07-01 00:00:00
[Install]
WantedBy=multi-user.target
```
## 重载Systemctl, 启动timer
```shell
sudo systemctl daemon-reload
sudo systemctl enable journal-autoclean.timer
```
## 尝试查看是否含有timer
```shell
systemctl list-units --type=timer
```
```
  UNIT                         LOAD   ACTIVE SUB     DESCRIPTION                                            
...............
  journal-autoclean.timer      loaded active waiting journalctl-autocleantimer
...............
```
## 配置完成

