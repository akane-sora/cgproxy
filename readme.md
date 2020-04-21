# Transparent Proxy with cgroup v2

## Introduction

cgproxy will **transparent** proxy anything running in specific cgroup. It resembles with *proxychains* and *tsock*, but without their disadvantages.

## Prerequest

- cgroup2

  For now, linux default using cgroup v1 for compatibility, this project need cgroup v2, you need disable cgroup v1 and enable cgroup v2 in your system. 

- TPROXY

  A process listening on port (e.g.  12345)  to accept iptables TPROXY, for example v2ray's dokodemo-door  in tproxy mode.

## How to install

```bash
mkdir build && cd build && cmake .. && make && make install
```

It is alreay in archlinux AUR.

## How to use

- First enable service

  ```bash
  sudo systemctl enable --now cgproxy.service
  sudo systemctl status cgproxy.service
  ```

- Then prefix with cgproxy with you command, just like proxychains

  ```
  cgproxy <CMD>
  ```

- For example, test proxy

  ```bash
  cgproxy curl -vIs https://www.google.com
  ```

More config in `/etc/cgproxy.conf`:

```bash
## any process in this cgroup will be proxied
## must start with slash '/'
proxy_cgroup="/proxy.slice"

## listening port of another proxy process, for example v2ray 
port=12345

## if you set to false, it's traffic won't go through proxy, but still can go direct to internet
enable_tcp=true
enable_udp=true
enable_ipv4=true
enable_ipv6=true

## v2ray outbound mark
## only useful if v2ray process is also in proxy_cgroup, for example, you want to proxy whole userspace,
## and v2ray is also running in userspace
## otherwise ignore this
v2ray_so_mark=255

## do not modify this if you don't known what you are doing
mark=2333
table=100
```



## NOTES

- `cgattach` attach pid to specific cgroup, and has *suid* bit set by default, be careful to use on multi-user server for securiry. To avoid this situation,  you can remove the *suid* bit , then it will fallback to use *sudo*, with *visudo* you can restrict permission or set NOPASSWD for youself.
- TPROXY need root or cap_net_admin capability whatever process is listening on port,
  v2ray as example: sudo setcap cap_net_admin+ep /usr/lib/v2ray/v2ray

## TIPS

- `systemd-cgls` to see the cgroup hierarchical tree.

## Licences

cgproxy is licenced under [![License: GPL v3](https://img.shields.io/badge/License-GPL%20v2-blue.svg)](https://www.gnu.org/licenses/gpl-2.0) 
