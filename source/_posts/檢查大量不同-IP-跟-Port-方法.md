---
title: 檢查大量不同 IP 跟 Port 方法
date: 2020-03-07 01:12:49
tags: [linux,shell]
categories: Linux
---

最近要測試大量主機 Port ，看裡面東西有沒有開通
使用大量 `telnet ip port` 非常花很多時間
最近在想有沒有好用的工具方便我們檢查

<!--more-->

## 建立多台 vagrant 連線

```ruby
Vagrant.configure("2") do |config|
  config.vm.provision "shell", inline: "echo Hello"
  config.vm.define "web" do |web|
    web.vm.box = "ubuntu/xenial64"
    web.vm.network "private_network", ip: "192.168.50.2"
  end

  config.vm.define "db" do |db|
    db.vm.box = "ubuntu/xenial64"
    db.vm.network "private_network", ip: "192.168.50.3"
  end
end
```


## nc 檢查法

```
$ timeout 2 bash -c "</dev/tcp/canyouseeme.org/80"; echo $?
0
$ timeout 2 bash -c "</dev/tcp/canyouseeme.org/81"; echo $?
124
$ timeout --preserve-status 2 bash -c "</dev/tcp/canyouseeme.org/81"; echo $?
143
$ nc -w 2 -v canyouseeme.org 80 </dev/null; echo $?
Connection to canyouseeme.org 80 port [tcp/http] succeeded!
0
$ nc -w 2 -v canyouseeme.org 81 </dev/null; echo $?
nc: connect to canyouseeme.org port 81 (tcp) timed out: Operation now in progress
1
```
[Test if remote TCP port is open from a shell script - Stack Overflow](https://stackoverflow.com/questions/4922943/test-if-remote-tcp-port-is-open-from-a-shell-script/14701003#14701003)

[Bash/nc: netcat as a simple port scanner](https://gist.github.com/gerjantd/9787606)
[Firxiao/check_service_ports: A tool (based nc) check service's tcp or udp is opened or closed](https://github.com/Firxiao/check_service_ports)


```bash
for i in {10.21.xxx.yyy,10.21.xxx.yyy,10.23.xxx.yyy};
do
        for j in {5501,5502,5503,5504,7701,7702,7703,7704,5551,5552,5553,7771,7772,7773};
        do
                (echo > /dev/tcp/${i}/${j}) > /dev/null 2>&1 && echo "${i}:${j} :: it's getting connected" || echo "${i}:${j} :: it's not connecting"
        done
done
```

[bash - Shell Script - telnet multiple hosts:ports - Stack Overflow](https://stackoverflow.com/questions/29392568/shell-script-telnet-multiple-hostsports)
發現這個範例還滿適合我現在的需求
用這個 shell 小改，順便加個顏色[Bash Shell 顯示 顏色的語法 | Tsung's Blog](https://blog.longwin.com.tw/2015/02/bash-shell-color-display-2015/)

```bash
#!/bin/bash
COLOR_REST='\e[0m';
COLOR_GREEN='\e[0;32m';
COLOR_RED='\e[0;31m';


for i in tw.yahoo.com google.com.tw;
do
        for j in 80 233;
        do
                (nc -zv -w 5 ${i} ${j}) > /dev/null 2>&1 && echo -e "${COLOR_GREEN}${i}:${j} :: it's getting connected${COLOR_REST}" || echo -e "${COLOR_RED}${i}:${j} :: it's not connecting${COLOR_REST}"
        done
done
```

顏色 echo 需要加 `-e`
第一次看到 shell 可以用 `{}`包迴圈
不過只有單筆會有問題樣子
所以我這邊有簡單調整另外一種寫法

例外還有多種運用
[第6期：使用nc批量做网络连通性测试 - 知乎](https://zhuanlan.zhihu.com/p/79244844)
```bash
#!/bin/bash
# checking network connectivity

# IPs and ports to check
# Ignore blank lines and treat hash sign as comments
# comments will be kept as a comment of result
IP_PORT="
# local host 
127.0.0.1 22
127.0.0.1 21
# well known sites
www.google.com 80
www.baidu.com 80
"

# checking
echo "$IP_PORT" | grep -Ev "^$" |
while read line;do
  # simply print comment line
  echo "$line" | grep -qE "^#"
  if [ $? -eq 0 ];then
      echo "$line"
      continue
  fi

  # normal line with ip and port
  connectFlag="DOWN"
  nc -z -w 1 $line
  if [ $? -eq 0 ];then
      connectFlag="UP"
  fi

  printf "%-20s %5s %5s\n" $line $connectFlag
done
```

以下方案失敗 QQ
先留著
## 使用 ansible 方法

[Ansible Playbook for network port checking from host level — OpenShift as example](https://medium.com/@chanhiuyeung/ansible-playbook-for-network-port-checking-from-host-level-openshift-as-example-fb1960090fdf)

有看到有人用 ansible 做出符合我的需求工具
這邊就順便驗證是否可以正常執行

### ansible 安裝

window 不能安裝 ansible
[Installing Ansible — Ansible Documentation](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html)
更多系統可以參考上面連結

```bash
sudo apt update
sudo apt install software-properties-common
sudo apt-add-repository --yes --update ppa:ansible/ansible
sudo apt install ansible
```

### ansible

FAILED => Using a SSH password instead of a key is not possible because Host Key checking is enabled and sshpass does not support this.  Please add this host's fingerprint to your known_hosts file to manage this host.
[ansible小结（四）ansible.cfg与默认配置 - 运维之路](http://www.361way.com/ansible-cfg/4401.html)

host_key_checking = False  



## 其他類似程式

[Check Host - 小巧實用網路監控軟體 ~ 不自量力 の Weithenn](http://www.weithenn.org/2011/11/check-host.html)


[Ansible Playbook for network port checking from host level — OpenShift as example](https://medium.com/@chanhiuyeung/ansible-playbook-for-network-port-checking-from-host-level-openshift-as-example-fb1960090fdf)
[Connectivity check WITH Ansible - DEV Community 👩‍💻👨‍💻](https://dev.to/koh_sh/connectivity-check-with-ansible-1po)
[testing - Ansible: Other option available for telnet check of open ports? - DevOps Stack Exchange](https://devops.stackexchange.com/questions/1658/ansible-other-option-available-for-telnet-check-of-open-ports)
[Test if a server is reachable from host and has port open with Ansible - Stack Overflow](https://stackoverflow.com/questions/39800368/test-if-a-server-is-reachable-from-host-and-has-port-open-with-ansible)
[ansible-testing/test_tcp at master · willthames/ansible-testing](https://github.com/willthames/ansible-testing/blob/master/test_tcp)
[guerzon/ansible-netcat: Ansible proof-of-concept - netcat systemd service](https://github.com/guerzon/ansible-netcat)
