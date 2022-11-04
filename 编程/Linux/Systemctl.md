# Systemctl

##### 1\. Systemd 和 Systemctl

Systemd是一个系统管理*守护进程、工具和库*的集合，用于取代 System V 初始进程。Systemd 的功能是用于集中管理和配置类 UNIX 系统。Systemd通常是所有其它守护进程的父进程，但并非总是如此。

Systemctl 是一个 systemd工具，主要负责控制 systemd 系统和服务管理器。

##### 2\. 系统中是否安装有 systemd 并确定当前安装的版本

```linux
[root@localhost ~]# systemctl --version
systemd 239
+PAM +AUDIT +SELINUX +IMA -APPARMOR +SMACK +SYSVINIT +UTMP +LIBCRYPTSETUP +GCRYPT +GNUTLS +ACL +XZ +LZ4 +SECCOMP +BLKID +ELFUTILS +KMOD +IDN2 -IDN +PCRE2 default-hierarchy=legacy
```

上面清楚地表明了，我们安装了 329 版本的 systemd

##### 3\. 检查 systemd 和 systemctl 的二进制文件和库文件的安装位置

```linux
# whereis systemd 
systemd: /usr/lib/systemd /etc/systemd /usr/share/systemd /usr/share/man/man1/systemd.1.gz
# whereis systemctl
systemctl: /usr/bin/systemctl /usr/share/man/man1/systemctl.1.gz
```

##### 4\. 检查 systemd 是否运行

```
[root@localhost ~]# ps -eaf | grep [s]ystemd
root           1       0  0 4月19 ?       00:00:02 /usr/lib/systemd/systemd --switched-root --system --deserialize 18
root         739       1  0 4月19 ?       00:00:00 /usr/lib/systemd/systemd-journald
root         773       1  0 4月19 ?       00:00:00 /usr/lib/systemd/systemd-udevd
dbus         932       1  0 4月19 ?       00:00:00 /usr/bin/dbus-daemon --system --address=systemd: --nofork --nopidfile --systemd-activation --syslog-only
root         942       1  0 4月19 ?       00:00:00 /usr/lib/systemd/systemd-machined
root        1051       1  0 4月19 ?       00:00:00 /usr/lib/systemd/systemd-logind
```

**注意**：systemd 是作为父进程（PID=1）运行的。在上面带（-e）参数的ps命令输出中，选择所有进程，（-a）选择除会话前导外的所有进程，并使用（-f）参数输出完整格式列表（即 -eaf）。

##### 5\. 分析 systemd 启动进程

```
[root@localhost ~]# systemd-analyze
Startup finished in 1.150s (kernel) + 3.728s (initrd) + 19.515s (userspace) = 24.395s
graphical.target reached after 19.471s in userspace
```

##### 6\. 分析启动时各个进程花费的时间

```
[root@localhost ~]# systemd-analyze blame
          6.321s kdump.service
          3.150s tuned.service
          2.340s udisks2.service
          2.049s vdo.service
          1.860s firewalld.service
          1.845s sssd.service
          1.832s systemd-udev-settle.service
          1.764s NetworkManager-wait-online.service
          1.667s dracut-initqueue.service
          1.524s systemd-machined.service
          1.252s lvm2-monitor.service
          1.116s libvirtd.service
          1.047s systemd-vconsole-setup.service
		  …
```

7\. 分析启动时的关键链

```
[root@localhost ~]# systemd-analyze critical-chain
The time after the unit is active or started is printed after the "@" character.
The time the unit takes to start is printed after the "+" character.

graphical.target @19.471s
└─multi-user.target @19.471s
  └─tuned.service @6.409s +3.150s
    └─network.target @6.385s
      └─NetworkManager.service @6.253s +128ms
        └─network-pre.target @6.252s
          └─firewalld.service @4.390s +1.860s
            └─polkit.service @3.454s +932ms
              └─basic.target @3.196s
                └─paths.target @3.196s
                  └─cups.path @3.196s
                    └─sysinit.target @3.191s
                      └─systemd-update-utmp.service @3.182s +8ms
                        └─auditd.service @3.080s +101ms
                          └─systemd-tmpfiles-setup.service @3.029s +45ms
                            └─import-state.service @2.989s +39ms
                              └─local-fs.target @2.987s
                                └─boot.mount @2.916s +70ms
                                  └─systemd-fsck@dev-disk-by\x2duuid-d1366dab\x2df97e\x2d4683\x2db17a\x2d1e5f80fa37be.>
                                    └─local-fs-pre.target @2.862s
                                      └─lvm2-monitor.service @672ms +1.252s
                                        └─dm-event.socket @666ms
                                          └─-.mount
                                            └─system.slice
                                              └─-.slice
```

### 管理并控制单元

##### 8\. 列出所有可用单元

```
[root@localhost ~]# systemctl list-unit-files
UNIT FILE                                  STATE    
proc-sys-fs-binfmt_misc.automount          static   
-.mount                                    generated
boot.mount                                 generated
dev-hugepages.mount                        static   
dev-mqueue.mount                           static   
mnt-hgfs.mount                             generated
proc-fs-nfsd.mount                         static   
proc-sys-fs-binfmt_misc.mount              static   
run-vmblock\x2dfuse.mount                  disabled 
…
```

##### 9. 列出所有运行中单元

```
[root@localhost ~]# systemctl list-units
  UNIT                                                               LOAD   ACTIVE SUB       DESCRIPTION              
  proc-sys-fs-binfmt_misc.automount                                  loaded active waiting   Arbitrary Executable File>
  sys-devices-pci0000:00-0000:00:07.1-ata2-host1-target1:0:0-1:0:0:0-block-sr0.device loaded active plugged   VMware_I>
  sys-devices-pci0000:00-0000:00:10.0-host2-target2:0:0-2:0:0:0-block-sda-sda1.device loaded active plugged   VMware_V>
  sys-devices-pci0000:00-0000:00:10.0-host2-target2:0:0-2:0:0:0-block-sda-sda2.device loaded active plugged   VMware_V>
  sys-devices-pci0000:00-0000:00:10.0-host2-target2:0:0-2:0:0:0-block-sda.device loaded active plugged   VMware_Virtua>
  sys-devices-pci0000:00-0000:00:11.0-0000:02:01.0-net-ens33.device  loaded active plugged   82545EM Gigabit Ethernet >
  sys-devices-pci0000:00-0000:00:11.0-0000:02:02.0-sound-card0.device loaded active plugged   ES1371/ES1373 / Creative>
  sys-devices-platform-serial8250-tty-ttyS1.device                   loaded active plugged   /sys/devices/platform/ser>
  sys-devices-platform-serial8250-tty-ttyS2.device                   loaded active plugged   /sys/devices/platform/ser>
  sys-devices-platform-serial8250-tty-ttyS3.device                   loaded active plugged   /sys/devices/platform/ser>
  sys-devices-pnp0-00:05-tty-ttyS0.device                            loaded active plugged   /sys/devices/pnp0/00:05/t>
  sys-devices-virtual-block-dm\x2d0.device                           loaded active plugged   /sys/devices/virtual/bloc>
  sys-devices-virtual-block-dm\x2d1.device                           loaded active plugged   /sys/devices/virtual/bloc>
  sys-devices-virtual-net-virbr0.device                              loaded active plugged   /sys/devices/virtual/net/>
  sys-devices-virtual-net-virbr0\x2dnic.device                       loaded active plugged   /sys/devices/virtual/net/>
  sys-module-configfs.device                                         loaded active plugged   /sys/module/configfs     
  sys-module-fuse.device                                             loaded active plugged   /sys/module/fuse      
```

##### 10\. 列出所有失败的单元

```
[root@localhost ~]# systemctl --failed
  UNIT           LOAD   ACTIVE SUB    DESCRIPTION                           
● mcelog.service loaded failed failed Machine Check Exception Logging Daemon

LOAD   = Reflects whether the unit definition was properly loaded.
ACTIVE = The high-level unit activation state, i.e. generalization of SUB.
SUB    = The low-level unit activation state, values depend on unit type.

1 loaded units listed. Pass --all to see loaded but inactive units, too.
To show all installed unit files use 'systemctl list-unit-files'.
```

##### 11\. 检查某个单元是否启用

```
[root@localhost ~]# systemctl is-enabled crond.service
enabled
```

##### 12. 检查某个单元或服务是否运行

```
[root@localhost ~]# systemctl status firewalld.service
● firewalld.service - firewalld - dynamic firewall daemon
   Loaded: loaded (/usr/lib/systemd/system/firewalld.service; enabled; vendor preset: enabled)
   Active: active (running) since Sun 2021-04-18 11:22:47 CST; 2 days ago
     Docs: man:firewalld(1)
 Main PID: 1036 (firewalld)
    Tasks: 2 (limit: 4712)
   Memory: 11.7M
   CGroup: /system.slice/firewalld.service
           └─1036 /usr/libexec/platform-python -s /usr/sbin/firewalld --nofork --nopid
```

### 控制并管理服务

##### 13. 列出所有服务（包括启用的和禁用的）

```
[root@localhost ~]# systemctl list-unit-files --type=service
UNIT FILE                                  STATE   
accounts-daemon.service                    enabled 
alsa-restore.service                       static  
alsa-state.service                         static  
anaconda-direct.service                    static  
anaconda-nm-config.service                 static  
anaconda-noshell.service                   static  
anaconda-pre.service                       static  
anaconda-shell@.service                    static  
anaconda-sshd.service                      static
…
```

##### 14\. 启动、重启、停止、重载服务以及检查服务（如 httpd.service）状态

```
# systemctl start httpd.service
# systemctl restart httpd.service
# systemctl stop httpd.service
# systemctl reload httpd.service
# systemctl status httpd.service
```

**注意**：当我们使用 systemctl 的 start，restart，stop 和 reload 命令时，我们不会从终端获取到任何输出内容，只有 status 命令可以打印输出。

##### 15\. 激活服务并在启动时启用或禁用服务（即系统启动时自动启动服务

```
# systemctl is-active httpd.service
# systemctl enable httpd.service
# systemctl disable httpd.service
```

##### 16\. 屏蔽（让它不能启动）或显示服务

```
# systemctl mask httpd.service
ln -s '/dev/null' '/etc/systemd/system/httpd.service'
# systemctl unmask httpd.service
rm '/etc/systemd/system/httpd.service'
```

##### 17. 使用systemctl命令杀死服务

```
# systemctl kill httpd
```

### 管理并控制挂载点

##### 18. 列出所有系统挂载点

```
# systemctl list-unit-files --type=mount
```

##### 19\. 挂载、卸载、重新挂载、重启挂载、检查系统中挂载点状态

```
[root@localhost ~]# systemctl start tmp.mount
[root@localhost ~]# systemctl stop tmp.mount
[root@localhost ~]# systemctl restart tmp.mount
[root@localhost ~]# systemctl reload tmp.mount
[root@localhost ~]# systemctl status tmp.mount
● tmp.mount - Temporary Directory (/tmp)
   Loaded: loaded (/usr/lib/systemd/system/tmp.mount; disabled; vendor preset: disabled)
   Active: active (mounted) since Wed 2021-04-21 16:36:44 CST; 16s ago
    Where: /tmp
     What: tmpfs
     Docs: man:hier(7)
           https://www.freedesktop.org/wiki/Software/systemd/APIFileSystems
    Tasks: 0 (limit: 4712)
   Memory: 96.0K
   CGroup: /system.slice/tmp.mount

4月 21 16:36:44 localhost.localdomain systemd[1]: tmp.mount: Directory /tmp to mount over is not empty, mounting anywa>
4月 21 16:36:44 localhost.localdomain systemd[1]: Mounting Temporary Directory (/tmp)...
4月 21 16:36:44 localhost.localdomain systemd[1]: Mounted Temporary Directory (/tmp).
4月 21 16:36:51 localhost.localdomain systemd[1]: Reloading Temporary Directory (/tmp).
4月 21 16:36:51 localhost.localdomain systemd[1]: Reloaded Temporary Directory (/tmp).
```

##### 20\. 在启动时激活、启用或禁用挂载点

```
[root@localhost ~]# systemctl is-active tmp.mount
active
[root@localhost ~]# systemctl enable tmp.mount
Created symlink /etc/systemd/system/local-fs.target.wants/tmp.mount → /usr/lib/systemd/system/tmp.mount.
[root@localhost ~]# systemctl disable tmp.mount
Removed /etc/systemd/system/local-fs.target.wants/tmp.mount.
```

##### 21\. 屏蔽或可见 (让它不可见) 挂载点

```
[root@localhost ~]# systemctl mask tmp.mount
Created symlink /etc/systemd/system/tmp.mount → /dev/null.
[root@localhost ~]# systemctl unmask tmp.mount
Removed /etc/systemd/system/tmp.mount.
```

### 控制并管理套接口

##### 22\. 列出所有可用系统套接口

```
[root@localhost ~]# systemctl mask tmp.mount
Created symlink /etc/systemd/system/tmp.mount → /dev/null.
[root@localhost ~]# systemctl unmask tmp.mount
Removed /etc/systemd/system/tmp.mount.
…
```

##### 23\. 启动、重启、停止、重载套接口并检查其状态

```
# systemctl start cups.socket
# systemctl restart cups.socket
# systemctl stop cups.socket
# systemctl reload cups.socket
# systemctl status cups.socket
```

##### 24. 在启动时激活套接口，并启用或禁用它（系统启动时自启动）

```
# systemctl is-active cups.socket
# systemctl enable cups.socket
# systemctl disable cups.socket
```

##### 25. 屏蔽（使它不能启动）或显示套接口

```
# systemctl mask cups.socket
# systemctl unmask cups.socket
```

### 服务的CPU利用率（分配额）

##### 26. 获取当前某个服务的CPU分配额（如httpd）

```
# systemctl show -p CPUShares httpd.service
CPUShares=1024
```

##### 27. 将某个服务（httpd.service）的CPU分配份额限制为2000 CPUShares

**注意**：各个服务的默认 CPU 分配份额 1024，你可以增加/减少某个进程的 CPU 分配份额。

```
# systemctl set-property httpd.service CPUShares=2000
# systemctl show -p CPUShares httpd.service
CPUShares=2000
```

##### 28. 检查某个服务的所有配置细节

```
[root@localhost ~]# systemctl show httpd
Type=notify
Restart=no
NotifyAccess=main
RestartUSec=100ms
TimeoutStartUSec=1min 30s
TimeoutStopUSec=1min 30s
RuntimeMaxUSec=infinity
WatchdogUSec=0
WatchdogTimestampMonotonic=0
PermissionsStartOnly=no
RootDirectoryStartOnly=no
RemainAfterExit=no
GuessMainPID=yes
…
```

##### 29\. 分析某个服务的关键链

```
[root@localhost ~]# systemd-analyze critical-chain httpd.service
The time after the unit is active or started is printed after the "@" character.
The time the unit takes to start is printed after the "+" character.

└─nss-lookup.target @7.333s
  └─systemd-resolved.service @6.240s +1.092s
    └─systemd-journald.socket
      └─system.slice
        └─-.slice
```

##### 30\. 获取某个服务（httpd）的依赖性列表

```
[root@localhost ~]# systemctl list-dependencies httpd.service
httpd.service
● ├─-.mount
● ├─httpd-init.service
● ├─php-fpm.service
● ├─system.slice
● └─sysinit.target
●   ├─dev-hugepages.mount
●   ├─dev-mqueue.mount
●   ├─dracut-shutdown.service
●   ├─import-state.service
●   ├─iscsi-onboot.service
●   ├─kmod-static-nodes.service
●   ├─ldconfig.service
●   ├─loadmodules.service
●   ├─lvm2-lvmpolld.socket
●   ├─lvm2-monitor.service
…
```

##### 31. 按等级列出控制组

```
[root@localhost ~]# systemd-cgls
Control group /:
-.slice
├─user.slice
│ ├─user-0.slice
│ │ ├─session-3.scope
│ │ │ ├─1839 sshd: root [priv]
│ │ │ ├─1843 sshd: root@pts/0
│ │ │ ├─1844 -bash
│ │ │ ├─2422 systemd-cgls
│ │ │ └─2423 systemd-cgls
│ │ └─user@0.service
│ │   ├─pulseaudio.service
│ │   │ └─1765 /usr/bin/pulseaudio --daemonize=no
│ │   ├─init.scope
│ │   │ ├─1751 /usr/lib/systemd/systemd --user
│ │   │ └─1756 (sd-pam)
│ │   └─dbus.service
│ │     └─1793 /usr/bin/dbus-daemon --session --address=systemd: --nofork --nopidfile --systemd-activation --syslog-on>
│ └─user-42.slice
│   ├─session-c1.scope
│   │ ├─1945 gdm-session-worker [pam/gdm-launch-environment]
│   │ ├─1974 /usr/libexec/gdm-wayland-session gnome-session --autostart /usr/share/gdm/greeter/autostart
│   │ ├─1996 /usr/libexec/gnome-session-binary --autostart /usr/share/gdm/greeter/autostart
│   │ ├─2024 /usr/bin/gnome-shell
│   │ ├─2067 /usr/bin/Xwayland :1024 -rootless -te
…
```

32. 按CPU、内存、输入和输出列出控制组

```
[root@localhost ~]# systemd-cgtop
Control Group                                                                   Tasks   %CPU   Memory  Input/s Output/s
/                                                                                 342      -   718.3M        -        -
/init.scope                                                                         1      -    19.4M        -        -
/system.slice                                                                     106      -   209.2M        -        -
/system.slice/ModemManager.service                                                  3      -     2.8M        -        -
/system.slice/NetworkManager.service                                                3      -     5.5M        -        -
/system.slice/accounts-daemon.service                                               3      -     2.1M        -        -
/system.slice/atd.service                                                           1      -   572.0K        -        -
/system.slice/auditd.service                                                        4      -     1.9M        -        -
/system.slice/avahi-daemon.service                                                  2      -     1.3M        -        -
/system.slice/boot.mount                                                            -      -   208.0K        -        -
/system.slice/chronyd.service                                                       1      -     1.3M        -        -
/system.slice/colord.service                                                        3      -     3.9M        -        -
/system.slice/crond.service                                                         1      -     1.3M        -        -
/system.slice/cups.service                                                          1      -     2.0M        -        -
/system.slice/dbus.service                                                          2      -     2.8M        -        -
/system.slice/dev-hugepages.mount                                                   -      -    64.0K        -        -
/system.slice/dev-mapper-cl\x2dswap.swap                                            -      -   240.0K        -        -
/system.slice/dev-mqueue.mount                                                      -      -    56.0K        -        -
/system.slice/firewalld.service                                                     2      -    23.2M        -        -
/system.slice/gdm.service                                                           3      -     4.5M        -        -
/system.slice/gssproxy.service                                                      6      -     1.0M        -        -
/system.slice/ksmtuned.service                                                      2      -     5.8M        -        -
/system.slice/libstoragemgmt.service                                                1      -   832.0K        -        -
/system.slice/libvirtd.service                                                     19      -    12.1M        -        -
```

### 控制运行等级

① 启动救援模式：`systemctl rescue`

② 进入紧急模式：`systemctl emergency`

③ 列出当前的运行级别：`systemctl get-default`

④ 启用运行级别：

```
systemctl isolate runlevel5.target
systemctl isolate graphical.target
# 使用 start 关键字也可以但是建议使用 isolate
systemctl start runlevel5.target
systemctl start graphical.target
```

⑤ 重启、停止、挂起、休眠系统或使系统进入混合睡眠

```
systemctl reboot
systemctl halt
systemctl suspend
systemctl hibernate
systemctl hybrid-sleep
```

##### 系统的运行等级

| runlevel 模式    |                                | 描述               |
| ---------------- | ------------------------------ | ------------------ |
| runlevel0.target | shutdown.target                | 关闭系统           |
| runlevel1.target | rescue.target/emergency.target | 救援；维护模式     |
| runlevel2.target | multi-user.target              | 多用户，无图形系统 |
| runlevel3.target | multi-user.target              | 多用户，五图形系统 |
| runlevel4.target | nulti-user.target              | 多用户，无图形系统 |
| runlevel5.target | graphical.target               | 多用户，图形化系统 |
| runlevel6.target |                                | 关闭并重启系统     |

