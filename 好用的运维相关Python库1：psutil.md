由于Bash实在太恶心，很多运维工程师开始使用Python代替Bash写各种脚本。

虽然理论上可以用Python调用Bash指令实现各种功能，但是给各种命令写Bash wrapper还要解析输出实在太麻烦。所以我们还是希望用原生Python库来实现各种功能。

今天我推荐一个好用的Python库`psutil`。

`psutil`一个能够跨平台使用的库。可以用来获取系统信息（CPU、内存、磁盘、网络）和进程信息。

它可以被用来替代这些UNIX的系统命令：`ps, top, lsof, netstat, ifconfig, who, df, kill, free, nice, ionice, iostat, iotop, uptime, pidof, tty, taskset, pmap`

下面是一些具体例子：

```python
>>> import psutil # 加载库
```

# CPU信息

CPU占用率

```python
>>> psutil.cpu_percent(interval=5)
12.9
```

CPU频率

```python
>>> psutil.cpu_freq()
scpufreq(current=2300, min=2300, max=2300)
```

CPU load

```python
>>> psutil.getloadavg()
(2.21044921875, 2.4814453125, 2.74609375)
```

# 内存信息

内存使用状况

```python
>>> psutil.virtual_memory()
svmem(total=8589934592, available=1664913408, percent=80.6, used=5807058944, free=15069184, active=1651421184, inactive=1649065984, wired=4155637760)
```

# 磁盘

磁盘分区信息

```python
>>> psutil.disk_partitions()
[sdiskpart(device='/dev/disk1s5', mountpoint='/', fstype='apfs', opts='ro,local,rootfs,dovolfs,journaled,multilabel', maxfile=255, maxpath=1024), ...]
```

磁盘占用率

```python
>>> psutil.disk_usage('/')
sdiskusage(total=250685575168, used=11233398784, free=14531440640, percent=43.6)
```

磁盘IO

```python
>>> psutil.disk_io_counters()
sdiskio(read_count=190236826, write_count=110716593, read_bytes=11349786423296, write_bytes=9505852936192, read_time=165443373, write_time=100234117)
```

# 网络

网络连接 (`netstat`)

```python
>>> import psutil
>>> psutil.net_connections()
[pconn(fd=115, family=<AddressFamily.AF_INET: 2>, type=<SocketType.SOCK_STREAM: 1>, laddr=addr(ip='10.0.0.1', port=48776), raddr=addr(ip='93.186.135.91', port=80), status='ESTABLISHED', pid=1254),
 pconn(fd=117, family=<AddressFamily.AF_INET: 2>, type=<SocketType.SOCK_STREAM: 1>, laddr=addr(ip='10.0.0.1', port=43761), raddr=addr(ip='72.14.234.100', port=80), status='CLOSING', pid=2987),
 pconn(fd=-1, family=<AddressFamily.AF_INET: 2>, type=<SocketType.SOCK_STREAM: 1>, laddr=addr(ip='10.0.0.1', port=60759), raddr=addr(ip='72.14.234.104', port=80), status='ESTABLISHED', pid=None),
 pconn(fd=-1, family=<AddressFamily.AF_INET: 2>, type=<SocketType.SOCK_STREAM: 1>, laddr=addr(ip='10.0.0.1', port=51314), raddr=addr(ip='72.14.234.83', port=443), status='SYN_SENT', pid=None)
 ...]
```

网络接口（`ifconfig`)

```python
>>> psutil.net_if_addrs()
{'lo': [snicaddr(family=<AddressFamily.AF_INET: 2>, address='127.0.0.1', netmask='255.0.0.0', broadcast='127.0.0.1', ptp=None),
        snicaddr(family=<AddressFamily.AF_INET6: 10>, address='::1', netmask='ffff:ffff:ffff:ffff:ffff:ffff:ffff:ffff', broadcast=None, ptp=None),
        snicaddr(family=<AddressFamily.AF_LINK: 17>, address='00:00:00:00:00:00', netmask=None, broadcast='00:00:00:00:00:00', ptp=None)],
 'wlan0': [snicaddr(family=<AddressFamily.AF_INET: 2>, address='192.168.1.3', netmask='255.255.255.0', broadcast='192.168.1.255', ptp=None),
           snicaddr(family=<AddressFamily.AF_INET6: 10>, address='fe80::c685:8ff:fe45:641%wlan0', netmask='ffff:ffff:ffff:ffff::', broadcast=None, ptp=None),
           snicaddr(family=<AddressFamily.AF_LINK: 17>, address='c4:85:08:45:06:41', netmask=None, broadcast='ff:ff:ff:ff:ff:ff', ptp=None)]}
```


# 其它系统信息

电池

```python
>>> psutil.sensors_battery()
sbattery(percent=93, secsleft=15420, power_plugged=False)
```

当前用户

```python
>>> psutil.users()
[suser(name='myname', terminal='console', host=None, started=1604809088.0, pid=180), suser(name='yuliji', terminal='ttys012', host=None, started=1615375616.0, pid=13894)]
```

# 进程信息

PID

```python
>>> psutil.pids()
[1, 2, 3, 5, 7, 8, 9, 10, 11, 12, 13, 14, 15, 17, 18, 19, ..., 32498]
```

所有进程

```python
>>> for proc in psutil.process_iter(['pid', 'name', 'username']):
...     print(proc.info)
...
{'name': 'systemd', 'pid': 1, 'username': 'root'}
{'name': 'kthreadd', 'pid': 2, 'username': 'root'}
{'name': 'ksoftirqd/0', 'pid': 3, 'username': 'root'}
```


详细文档，请参考： https://psutil.readthedocs.io/en/latest/