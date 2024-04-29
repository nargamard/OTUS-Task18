# Задание: разворачиваем сетевую лабораторию
Vagrantfile с начальным построением сети

inetRouter
centralRouter
centralServer

тестировалось на virtualbox

Планируемая архитектура

построить следующую архитектуру

Сеть office1

192.168.2.0/26 - dev
192.168.2.64/26 - test servers
192.168.2.128/26 - managers
192.168.2.192/26 - office hardware

Сеть office2
192.168.1.0/25 - dev
192.168.1.128/26 - test servers
192.168.1.192/26 - office hardware

Сеть central
192.168.0.0/28 - directors
192.168.0.32/28 - office hardware
192.168.0.64/26 - wifi
```
Office1 ---\
----> Central --IRouter --> internet
Office2----/
```

Итого должны получится следующие сервера
inetRouter
centralRouter
office1Router
office2Router
centralServer
office1Server
office2Server

Теоретическая часть

Найти свободные подсети
Посчитать сколько узлов в каждой подсети, включая свободные
Указать broadcast адрес для каждой подсети
проверить нет ли ошибок при разбиении

Практическая часть

Соединить офисы в сеть согласно схеме и настроить роутинг
Все сервера и роутеры должны ходить в инет черз inetRouter
Все сервера должны видеть друг друга
у всех новых серверов отключить дефолт на нат (eth0), который вагрант поднимает для связи
при нехватке сетевых интервейсов добавить по несколько адресов на интерфейс

---
## Решение:


При помощи калькулятора получаем параметры подсетей:
|                Назначение |               Сеть |            Маска | Число узлов |    Первый адрес | Последний адрес | Broadcast адрес |
|---------------------------|--------------------|------------------|-------------|-----------------|-----------------|-----------------|
|       central - directors |     192.168.0.0/28 |  255.255.255.240 |          14 |     192.168.0.1 |    192.168.0.14 |    192.168.0.15 |
|        central - СВОБОДНО |    192.168.0.16/28 |  255.255.255.240 |          14 |    192.168.0.17 |    192.168.0.30 |    192.168.0.31 |
| central - office hardware |    192.168.0.32/28 |  255.255.255.240 |          14 |    192.168.0.33 |    192.168.0.46 |    192.168.0.47 |
|        central - СВОБОДНО |    192.168.0.48/28 |  255.255.255.240 |          14 |    192.168.0.49 |    192.168.0.62 |    192.168.0.63 |
|            central - Wifi |    192.168.0.64/26 |  255.255.255.192 |          62 |    192.168.0.65 |   192.168.0.126 |   192.168.0.127 |
|        central - СВОБОДНО |   192.168.0.128/25 |  255.255.255.128 |         126 |   192.168.0.129 |   192.168.0.254 |   192.168.0.255 |
|                           |                    |                  |             |                 |                 |                 |
|             Office1 - Dev |     192.168.2.0/26 |  255.255.255.192 |          62 |     192.168.2.1 |    192.168.2.62 |    192.168.2.63 |
|            Office1 - Test |    192.168.2.64/26 |  255.255.255.192 |          62 |    192.168.2.65 |   192.168.2.126 |   192.168.2.127 |
|        Office1 - Managers |   192.168.2.128/62 |  255.255.255.192 |          62 |   192.168.2.129 |   192.168.2.190 |   192.168.2.191 |
| Office1 - Office hardware |   192.168.2.192/26 |  255.255.255.192 |          62 |   192.168.2.193 |   192.168.2.254 |   192.168.2.255 |
|             СВОБОДНЫХ НЕТ |                    |                  |             |                 |                 |                 |
|                           |                    |                  |             |                 |                 |                 |
|             Office2 - Dev |     192.168.1.0/25 |  255.255.255.128 |         126 |     192.168.1.1 |   192.168.1.126 |   192.168.1.127 |
|            Office2 - Test |   192.168.1.128/26 |  255.255.255.192 |          62 |   192.168.1.129 |   192.168.1.190 |   192.168.1.191 |
|          Office2 - Office |   192.168.1.192/26 |  255.255.255.192 |          62 |   192.168.1.193 |   192.168.1.254 |   192.168.1.255 |
|             СВОБОДНЫХ НЕТ |                    |                  |             |                 |                 |                 |
|                           |                    |                  |             |                 |                 |                 |
|            Inet — central |   192.168.255.0/30 |  255.255.255.252 |           2 |   192.168.255.1 |   192.168.255.2 |   192.168.255.3 |
| Inet — central - СВОБОДНО |   192.168.255.4/30 |  255.255.255.252 |           2 |   192.168.255.5 |   192.168.255.6 |   192.168.255.7 |
| Inet — central - СВОБОДНО |   192.168.255.8/29 |  255.255.255.248 |           6 |   192.168.255.9 |  192.168.255.14 |  192.168.255.15 |
| Inet — central - СВОБОДНО |  192.168.255.16/28 | 	255.255.255.240 |          14 |  192.168.255.17 |  192.168.255.30 |  192.168.255.31 |
| Inet — central - СВОБОДНО |     192.168.255.32 | 	255.255.255.224 |          30 |  192.168.255.33 |  192.168.255.62 |  192.168.255.63 |
| Inet — central - СВОБОДНО |  192.168.255.64/26 |  255.255.255.192 |          62 |  192.168.255.65 | 192.168.255.126 | 192.168.255.127 |
| Inet — central - СВОБОДНО | 192.168.255.128/25 |  255.255.255.128 |         126 | 192.168.255.129 | 192.168.255.254 | 192.168.255.255 |

## Практика


Виртуальные машины создаются с помощью Vagrant.

<details><summary>Вывод `vagrant up`</summary>

```
[andrej@home-srv OTUS-Task18]$ vagrant up
Bringing machine 'inetRouter' up with 'virtualbox' provider...
Bringing machine 'centralRouter' up with 'virtualbox' provider...
Bringing machine 'centralServer' up with 'virtualbox' provider...
Bringing machine 'office1Router' up with 'virtualbox' provider...
Bringing machine 'office1Server' up with 'virtualbox' provider...
Bringing machine 'office2Router' up with 'virtualbox' provider...
Bringing machine 'office2Server' up with 'virtualbox' provider...
==> inetRouter: You assigned a static IP ending in ".1" to this machine.
==> inetRouter: This is very often used by the router and can cause the
==> inetRouter: network to not work properly. If the network doesn't work
==> inetRouter: properly, try changing this IP.
==> inetRouter: Importing base box 'ubuntu/jammy64'...
==> inetRouter: Matching MAC address for NAT networking...
==> inetRouter: You assigned a static IP ending in ".1" to this machine.
==> inetRouter: This is very often used by the router and can cause the
==> inetRouter: network to not work properly. If the network doesn't work
==> inetRouter: properly, try changing this IP.
==> inetRouter: Checking if box 'ubuntu/jammy64' version '1.0.0' is up to date...
==> inetRouter: Setting the name of the VM: OTUS-Task18_inetRouter_1714159559245_35261
==> inetRouter: Clearing any previously set network interfaces...
==> inetRouter: Preparing network interfaces based on configuration...
    inetRouter: Adapter 1: nat
    inetRouter: Adapter 2: intnet
    inetRouter: Adapter 8: hostonly
==> inetRouter: Forwarding ports...
    inetRouter: 22 (guest) => 2222 (host) (adapter 1)
==> inetRouter: Running 'pre-boot' VM customizations...
==> inetRouter: Booting VM...
==> inetRouter: Waiting for machine to boot. This may take a few minutes...
    inetRouter: SSH address: 127.0.0.1:2222
    inetRouter: SSH username: vagrant
    inetRouter: SSH auth method: private key
    inetRouter: Warning: Connection reset. Retrying...
    inetRouter: Warning: Remote connection disconnect. Retrying...
    inetRouter: 
    inetRouter: Vagrant insecure key detected. Vagrant will automatically replace
    inetRouter: this with a newly generated keypair for better security.
    inetRouter: 
    inetRouter: Inserting generated public key within guest...
    inetRouter: Removing insecure key from the guest if it's present...
    inetRouter: Key inserted! Disconnecting and reconnecting using new SSH key...
==> inetRouter: Machine booted and ready!
==> inetRouter: Checking for guest additions in VM...
    inetRouter: The guest additions on this VM do not match the installed version of
    inetRouter: VirtualBox! In most cases this is fine, but in rare cases it can
    inetRouter: prevent things such as shared folders from working properly. If you see
    inetRouter: shared folder errors, please make sure the guest additions within the
    inetRouter: virtual machine match the version of VirtualBox you have installed on
    inetRouter: your host and reload your VM.
    inetRouter: 
    inetRouter: Guest Additions Version: 6.0.0 r127566
    inetRouter: VirtualBox Version: 6.1
==> inetRouter: Setting hostname...
==> inetRouter: Configuring and enabling network interfaces...
==> inetRouter: Mounting shared folders...
    inetRouter: /vagrant => /home/andrej/Nextcloud/Документы/OTUS-HOMETASKS/OTUS-Task18
==> inetRouter: Running provisioner: shell...
    inetRouter: Running: inline script
==> centralRouter: You assigned a static IP ending in ".1" to this machine.
==> centralRouter: This is very often used by the router and can cause the
==> centralRouter: network to not work properly. If the network doesn't work
==> centralRouter: properly, try changing this IP.
==> centralRouter: Importing base box 'ubuntu/jammy64'...
==> centralRouter: Matching MAC address for NAT networking...
==> centralRouter: You assigned a static IP ending in ".1" to this machine.
==> centralRouter: This is very often used by the router and can cause the
==> centralRouter: network to not work properly. If the network doesn't work
==> centralRouter: properly, try changing this IP.
==> centralRouter: Checking if box 'ubuntu/jammy64' version '1.0.0' is up to date...
==> centralRouter: Setting the name of the VM: OTUS-Task18_centralRouter_1714159625437_16097
==> centralRouter: Fixed port collision for 22 => 2222. Now on port 2200.
==> centralRouter: Clearing any previously set network interfaces...
==> centralRouter: Preparing network interfaces based on configuration...
    centralRouter: Adapter 1: nat
    centralRouter: Adapter 2: intnet
    centralRouter: Adapter 3: intnet
    centralRouter: Adapter 4: intnet
    centralRouter: Adapter 5: intnet
    centralRouter: Adapter 6: intnet
    centralRouter: Adapter 7: intnet
    centralRouter: Adapter 8: hostonly
==> centralRouter: Forwarding ports...
    centralRouter: 22 (guest) => 2200 (host) (adapter 1)
==> centralRouter: Running 'pre-boot' VM customizations...
==> centralRouter: Booting VM...
==> centralRouter: Waiting for machine to boot. This may take a few minutes...
    centralRouter: SSH address: 127.0.0.1:2200
    centralRouter: SSH username: vagrant
    centralRouter: SSH auth method: private key
    centralRouter: Warning: Connection reset. Retrying...
    centralRouter: Warning: Remote connection disconnect. Retrying...
    centralRouter: Warning: Connection reset. Retrying...
    centralRouter: Warning: Remote connection disconnect. Retrying...
    centralRouter: 
    centralRouter: Vagrant insecure key detected. Vagrant will automatically replace
    centralRouter: this with a newly generated keypair for better security.
    centralRouter: 
    centralRouter: Inserting generated public key within guest...
    centralRouter: Removing insecure key from the guest if it's present...
    centralRouter: Key inserted! Disconnecting and reconnecting using new SSH key...
==> centralRouter: Machine booted and ready!
==> centralRouter: Checking for guest additions in VM...
    centralRouter: The guest additions on this VM do not match the installed version of
    centralRouter: VirtualBox! In most cases this is fine, but in rare cases it can
    centralRouter: prevent things such as shared folders from working properly. If you see
    centralRouter: shared folder errors, please make sure the guest additions within the
    centralRouter: virtual machine match the version of VirtualBox you have installed on
    centralRouter: your host and reload your VM.
    centralRouter: 
    centralRouter: Guest Additions Version: 6.0.0 r127566
    centralRouter: VirtualBox Version: 6.1
==> centralRouter: Setting hostname...
==> centralRouter: Configuring and enabling network interfaces...
==> centralRouter: Mounting shared folders...
    centralRouter: /vagrant => /home/andrej/Nextcloud/Документы/OTUS-HOMETASKS/OTUS-Task18
==> centralRouter: Running provisioner: shell...
    centralRouter: Running: inline script
==> centralServer: Importing base box 'ubuntu/jammy64'...
==> centralServer: Matching MAC address for NAT networking...
==> centralServer: Checking if box 'ubuntu/jammy64' version '1.0.0' is up to date...
==> centralServer: Setting the name of the VM: OTUS-Task18_centralServer_1714159705357_30122
==> centralServer: Fixed port collision for 22 => 2222. Now on port 2201.
==> centralServer: Clearing any previously set network interfaces...
==> centralServer: Preparing network interfaces based on configuration...
    centralServer: Adapter 1: nat
    centralServer: Adapter 2: intnet
    centralServer: Adapter 8: hostonly
==> centralServer: Forwarding ports...
    centralServer: 22 (guest) => 2201 (host) (adapter 1)
==> centralServer: Running 'pre-boot' VM customizations...
==> centralServer: Booting VM...
==> centralServer: Waiting for machine to boot. This may take a few minutes...
    centralServer: SSH address: 127.0.0.1:2201
    centralServer: SSH username: vagrant
    centralServer: SSH auth method: private key
    centralServer: Warning: Connection reset. Retrying...
    centralServer: Warning: Remote connection disconnect. Retrying...
    centralServer: Warning: Connection reset. Retrying...
    centralServer: Warning: Remote connection disconnect. Retrying...
    centralServer: 
    centralServer: Vagrant insecure key detected. Vagrant will automatically replace
    centralServer: this with a newly generated keypair for better security.
    centralServer: 
    centralServer: Inserting generated public key within guest...
    centralServer: Removing insecure key from the guest if it's present...
    centralServer: Key inserted! Disconnecting and reconnecting using new SSH key...
==> centralServer: Machine booted and ready!
==> centralServer: Checking for guest additions in VM...
    centralServer: The guest additions on this VM do not match the installed version of
    centralServer: VirtualBox! In most cases this is fine, but in rare cases it can
    centralServer: prevent things such as shared folders from working properly. If you see
    centralServer: shared folder errors, please make sure the guest additions within the
    centralServer: virtual machine match the version of VirtualBox you have installed on
    centralServer: your host and reload your VM.
    centralServer: 
    centralServer: Guest Additions Version: 6.0.0 r127566
    centralServer: VirtualBox Version: 6.1
==> centralServer: Setting hostname...
==> centralServer: Configuring and enabling network interfaces...
==> centralServer: Mounting shared folders...
    centralServer: /vagrant => /home/andrej/Nextcloud/Документы/OTUS-HOMETASKS/OTUS-Task18
==> centralServer: Running provisioner: shell...
    centralServer: Running: inline script
==> office1Router: You assigned a static IP ending in ".1" to this machine.
==> office1Router: This is very often used by the router and can cause the
==> office1Router: network to not work properly. If the network doesn't work
==> office1Router: properly, try changing this IP.
==> office1Router: Importing base box 'ubuntu/jammy64'...
==> office1Router: Matching MAC address for NAT networking...
==> office1Router: You assigned a static IP ending in ".1" to this machine.
==> office1Router: This is very often used by the router and can cause the
==> office1Router: network to not work properly. If the network doesn't work
==> office1Router: properly, try changing this IP.
==> office1Router: Checking if box 'ubuntu/jammy64' version '1.0.0' is up to date...
==> office1Router: Setting the name of the VM: OTUS-Task18_office1Router_1714159795690_79889
==> office1Router: Fixed port collision for 22 => 2222. Now on port 2202.
==> office1Router: Clearing any previously set network interfaces...
==> office1Router: Preparing network interfaces based on configuration...
    office1Router: Adapter 1: nat
    office1Router: Adapter 2: intnet
    office1Router: Adapter 3: intnet
    office1Router: Adapter 4: intnet
    office1Router: Adapter 5: intnet
    office1Router: Adapter 6: intnet
    office1Router: Adapter 8: hostonly
==> office1Router: Forwarding ports...
    office1Router: 22 (guest) => 2202 (host) (adapter 1)
==> office1Router: Running 'pre-boot' VM customizations...
==> office1Router: Booting VM...
==> office1Router: Waiting for machine to boot. This may take a few minutes...
    office1Router: SSH address: 127.0.0.1:2202
    office1Router: SSH username: vagrant
    office1Router: SSH auth method: private key
    office1Router: Warning: Connection reset. Retrying...
    office1Router: Warning: Remote connection disconnect. Retrying...
    office1Router: 
    office1Router: Vagrant insecure key detected. Vagrant will automatically replace
    office1Router: this with a newly generated keypair for better security.
    office1Router: 
    office1Router: Inserting generated public key within guest...
    office1Router: Removing insecure key from the guest if it's present...
    office1Router: Key inserted! Disconnecting and reconnecting using new SSH key...
==> office1Router: Machine booted and ready!
==> office1Router: Checking for guest additions in VM...
    office1Router: The guest additions on this VM do not match the installed version of
    office1Router: VirtualBox! In most cases this is fine, but in rare cases it can
    office1Router: prevent things such as shared folders from working properly. If you see
    office1Router: shared folder errors, please make sure the guest additions within the
    office1Router: virtual machine match the version of VirtualBox you have installed on
    office1Router: your host and reload your VM.
    office1Router: 
    office1Router: Guest Additions Version: 6.0.0 r127566
    office1Router: VirtualBox Version: 6.1
==> office1Router: Setting hostname...
==> office1Router: Configuring and enabling network interfaces...
==> office1Router: Mounting shared folders...
    office1Router: /vagrant => /home/andrej/Nextcloud/Документы/OTUS-HOMETASKS/OTUS-Task18
==> office1Router: Running provisioner: shell...
    office1Router: Running: inline script
==> office1Server: Importing base box 'ubuntu/jammy64'...
==> office1Server: Matching MAC address for NAT networking...
==> office1Server: Checking if box 'ubuntu/jammy64' version '1.0.0' is up to date...
==> office1Server: Setting the name of the VM: OTUS-Task18_office1Server_1714159869635_61753
==> office1Server: Fixed port collision for 22 => 2222. Now on port 2203.
==> office1Server: Clearing any previously set network interfaces...
==> office1Server: Preparing network interfaces based on configuration...
    office1Server: Adapter 1: nat
    office1Server: Adapter 2: intnet
    office1Server: Adapter 8: hostonly
==> office1Server: Forwarding ports...
    office1Server: 22 (guest) => 2203 (host) (adapter 1)
==> office1Server: Running 'pre-boot' VM customizations...
==> office1Server: Booting VM...
==> office1Server: Waiting for machine to boot. This may take a few minutes...
    office1Server: SSH address: 127.0.0.1:2203
    office1Server: SSH username: vagrant
    office1Server: SSH auth method: private key
    office1Server: Warning: Connection reset. Retrying...
    office1Server: Warning: Remote connection disconnect. Retrying...
    office1Server: 
    office1Server: Vagrant insecure key detected. Vagrant will automatically replace
    office1Server: this with a newly generated keypair for better security.
    office1Server: 
    office1Server: Inserting generated public key within guest...
    office1Server: Removing insecure key from the guest if it's present...
    office1Server: Key inserted! Disconnecting and reconnecting using new SSH key...
==> office1Server: Machine booted and ready!
==> office1Server: Checking for guest additions in VM...
    office1Server: The guest additions on this VM do not match the installed version of
    office1Server: VirtualBox! In most cases this is fine, but in rare cases it can
    office1Server: prevent things such as shared folders from working properly. If you see
    office1Server: shared folder errors, please make sure the guest additions within the
    office1Server: virtual machine match the version of VirtualBox you have installed on
    office1Server: your host and reload your VM.
    office1Server: 
    office1Server: Guest Additions Version: 6.0.0 r127566
    office1Server: VirtualBox Version: 6.1
==> office1Server: Setting hostname...
==> office1Server: Configuring and enabling network interfaces...
==> office1Server: Mounting shared folders...
    office1Server: /vagrant => /home/andrej/Nextcloud/Документы/OTUS-HOMETASKS/OTUS-Task18
==> office1Server: Running provisioner: shell...
    office1Server: Running: inline script
==> office2Router: You assigned a static IP ending in ".1" to this machine.
==> office2Router: This is very often used by the router and can cause the
==> office2Router: network to not work properly. If the network doesn't work
==> office2Router: properly, try changing this IP.
==> office2Router: Importing base box 'ubuntu/jammy64'...
==> office2Router: Matching MAC address for NAT networking...
==> office2Router: You assigned a static IP ending in ".1" to this machine.
==> office2Router: This is very often used by the router and can cause the
==> office2Router: network to not work properly. If the network doesn't work
==> office2Router: properly, try changing this IP.
==> office2Router: Checking if box 'ubuntu/jammy64' version '1.0.0' is up to date...
==> office2Router: Setting the name of the VM: OTUS-Task18_office2Router_1714159948452_95429
==> office2Router: Fixed port collision for 22 => 2222. Now on port 2204.
==> office2Router: Clearing any previously set network interfaces...
==> office2Router: Preparing network interfaces based on configuration...
    office2Router: Adapter 1: nat
    office2Router: Adapter 2: intnet
    office2Router: Adapter 3: intnet
    office2Router: Adapter 4: intnet
    office2Router: Adapter 5: intnet
    office2Router: Adapter 8: hostonly
==> office2Router: Forwarding ports...
    office2Router: 22 (guest) => 2204 (host) (adapter 1)
==> office2Router: Running 'pre-boot' VM customizations...
==> office2Router: Booting VM...
==> office2Router: Waiting for machine to boot. This may take a few minutes...
    office2Router: SSH address: 127.0.0.1:2204
    office2Router: SSH username: vagrant
    office2Router: SSH auth method: private key
    office2Router: Warning: Connection reset. Retrying...
    office2Router: Warning: Remote connection disconnect. Retrying...
    office2Router: Warning: Remote connection disconnect. Retrying...
    office2Router: Warning: Connection reset. Retrying...
    office2Router: 
    office2Router: Vagrant insecure key detected. Vagrant will automatically replace
    office2Router: this with a newly generated keypair for better security.
    office2Router: 
    office2Router: Inserting generated public key within guest...
    office2Router: Removing insecure key from the guest if it's present...
    office2Router: Key inserted! Disconnecting and reconnecting using new SSH key...
==> office2Router: Machine booted and ready!
==> office2Router: Checking for guest additions in VM...
    office2Router: The guest additions on this VM do not match the installed version of
    office2Router: VirtualBox! In most cases this is fine, but in rare cases it can
    office2Router: prevent things such as shared folders from working properly. If you see
    office2Router: shared folder errors, please make sure the guest additions within the
    office2Router: virtual machine match the version of VirtualBox you have installed on
    office2Router: your host and reload your VM.
    office2Router: 
    office2Router: Guest Additions Version: 6.0.0 r127566
    office2Router: VirtualBox Version: 6.1
==> office2Router: Setting hostname...
==> office2Router: Configuring and enabling network interfaces...
==> office2Router: Mounting shared folders...
    office2Router: /vagrant => /home/andrej/Nextcloud/Документы/OTUS-HOMETASKS/OTUS-Task18
==> office2Router: Running provisioner: shell...
    office2Router: Running: inline script
==> office2Server: Importing base box 'ubuntu/jammy64'...
==> office2Server: Matching MAC address for NAT networking...
==> office2Server: Checking if box 'ubuntu/jammy64' version '1.0.0' is up to date...
==> office2Server: Setting the name of the VM: OTUS-Task18_office2Server_1714160027452_94465
==> office2Server: Fixed port collision for 22 => 2222. Now on port 2205.
==> office2Server: Clearing any previously set network interfaces...
==> office2Server: Preparing network interfaces based on configuration...
    office2Server: Adapter 1: nat
    office2Server: Adapter 2: intnet
    office2Server: Adapter 8: hostonly
==> office2Server: Forwarding ports...
    office2Server: 22 (guest) => 2205 (host) (adapter 1)
==> office2Server: Running 'pre-boot' VM customizations...
==> office2Server: Booting VM...
==> office2Server: Waiting for machine to boot. This may take a few minutes...
    office2Server: SSH address: 127.0.0.1:2205
    office2Server: SSH username: vagrant
    office2Server: SSH auth method: private key
    office2Server: Warning: Connection reset. Retrying...
    office2Server: Warning: Remote connection disconnect. Retrying...
    office2Server: 
    office2Server: Vagrant insecure key detected. Vagrant will automatically replace
    office2Server: this with a newly generated keypair for better security.
    office2Server: 
    office2Server: Inserting generated public key within guest...
    office2Server: Removing insecure key from the guest if it's present...
    office2Server: Key inserted! Disconnecting and reconnecting using new SSH key...
==> office2Server: Machine booted and ready!
==> office2Server: Checking for guest additions in VM...
    office2Server: The guest additions on this VM do not match the installed version of
    office2Server: VirtualBox! In most cases this is fine, but in rare cases it can
    office2Server: prevent things such as shared folders from working properly. If you see
    office2Server: shared folder errors, please make sure the guest additions within the
    office2Server: virtual machine match the version of VirtualBox you have installed on
    office2Server: your host and reload your VM.
    office2Server: 
    office2Server: Guest Additions Version: 6.0.0 r127566
    office2Server: VirtualBox Version: 6.1
==> office2Server: Setting hostname...
==> office2Server: Configuring and enabling network interfaces...
==> office2Server: Mounting shared folders...
    office2Server: /vagrant => /home/andrej/Nextcloud/Документы/OTUS-HOMETASKS/OTUS-Task18
==> office2Server: Running provisioner: shell...
    office2Server: Running: inline script
==> office2Server: Running provisioner: ansible...
    office2Server: Running ansible-playbook...
```
</details>

Настройка сети на виртуальных машинах выполнена с помощью Ansible. 

<details><summary>Выполнение Ансибл-плейбука provision.yaml</summary>

```
PLAY [inetRouter] **************************************************************

TASK [Gathering Facts] *********************************************************
ok: [inetRouter]
ok: [office2Router]
ok: [office1Router]
ok: [centralRouter]
ok: [centralServer]
ok: [office2Server]
ok: [office1Server]

TASK [stop & disable ufw] ******************************************************
changed: [inetRouter]
changed: [centralRouter]
changed: [office1Router]
changed: [centralServer]
changed: [office2Router]
changed: [office1Server]
changed: [office2Server]

TASK [install traceroute] ******************************************************
changed: [inetRouter]
changed: [centralRouter]
changed: [office1Router]
changed: [centralServer]
changed: [office2Router]
changed: [office2Server]
changed: [office1Server]

TASK [Set up NAT on inetRouter] ************************************************
skipping: [centralRouter] => (item={'src': 'templates/iptables_rules.ipv4', 'dest': '/etc/iptables_rules.ipv4', 'mode': '0644'}) 
skipping: [centralRouter] => (item={'src': 'templates/iptables_restore', 'dest': '/etc/network/if-pre-up.d/iptables', 'mode': '0755'}) 
skipping: [centralRouter]
skipping: [office1Router] => (item={'src': 'templates/iptables_rules.ipv4', 'dest': '/etc/iptables_rules.ipv4', 'mode': '0644'}) 
skipping: [office1Router] => (item={'src': 'templates/iptables_restore', 'dest': '/etc/network/if-pre-up.d/iptables', 'mode': '0755'}) 
skipping: [office2Router] => (item={'src': 'templates/iptables_rules.ipv4', 'dest': '/etc/iptables_rules.ipv4', 'mode': '0644'}) 
skipping: [office2Router] => (item={'src': 'templates/iptables_restore', 'dest': '/etc/network/if-pre-up.d/iptables', 'mode': '0755'}) 
skipping: [office1Router]
skipping: [office2Router]
skipping: [centralServer] => (item={'src': 'templates/iptables_rules.ipv4', 'dest': '/etc/iptables_rules.ipv4', 'mode': '0644'}) 
skipping: [centralServer] => (item={'src': 'templates/iptables_restore', 'dest': '/etc/network/if-pre-up.d/iptables', 'mode': '0755'}) 
skipping: [centralServer]
skipping: [office1Server] => (item={'src': 'templates/iptables_rules.ipv4', 'dest': '/etc/iptables_rules.ipv4', 'mode': '0644'}) 
skipping: [office1Server] => (item={'src': 'templates/iptables_restore', 'dest': '/etc/network/if-pre-up.d/iptables', 'mode': '0755'}) 
skipping: [office1Server]
skipping: [office2Server] => (item={'src': 'templates/iptables_rules.ipv4', 'dest': '/etc/iptables_rules.ipv4', 'mode': '0644'}) 
skipping: [office2Server] => (item={'src': 'templates/iptables_restore', 'dest': '/etc/network/if-pre-up.d/iptables', 'mode': '0755'}) 
skipping: [office2Server]
changed: [inetRouter] => (item={'src': 'templates/iptables_rules.ipv4', 'dest': '/etc/iptables_rules.ipv4', 'mode': '0644'})
changed: [inetRouter] => (item={'src': 'templates/iptables_restore', 'dest': '/etc/network/if-pre-up.d/iptables', 'mode': '0755'})

TASK [set up forward packages across routers] **********************************
skipping: [centralServer]
skipping: [office1Server]
skipping: [office2Server]
changed: [inetRouter]
changed: [office2Router]
changed: [centralRouter]
changed: [office1Router]

TASK [disable default route] ***************************************************
skipping: [inetRouter]
changed: [centralServer]
changed: [office1Router]
changed: [office2Router]
changed: [centralRouter]
changed: [office1Server]
changed: [office2Server]

TASK [add default gateway for centralRouter] ***********************************
changed: [inetRouter]
changed: [office2Router]
changed: [centralRouter]
changed: [centralServer]
changed: [office1Router]
changed: [office2Server]
changed: [office1Server]

TASK [restart all hosts] *******************************************************
changed: [office2Router]
changed: [office1Router]
changed: [centralRouter]
changed: [office1Server]
changed: [office2Server]
fatal: [centralServer]: FAILED! => {"msg": "Failed to connect to the host via ssh: ssh: connect to host 192.168.56.12 port 22: No route to host"}
fatal: [inetRouter]: FAILED! => {"msg": "Failed to connect to the host via ssh: ssh: connect to host 192.168.56.10 port 22: No route to host"}

PLAY RECAP *********************************************************************
centralRouter              : ok=7    changed=6    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0   
centralServer              : ok=5    changed=4    unreachable=0    failed=1    skipped=2    rescued=0    ignored=0   
inetRouter                 : ok=6    changed=5    unreachable=0    failed=1    skipped=1    rescued=0    ignored=0   
office1Router              : ok=7    changed=6    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0   
office1Server              : ok=6    changed=5    unreachable=0    failed=0    skipped=2    rescued=0    ignored=0   
office2Router              : ok=7    changed=6    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0   
office2Server              : ok=6    changed=5    unreachable=0    failed=0    skipped=2    rescued=0    ignored=0   

Ansible failed to complete successfully. Any error output should be
visible above. Please fix these errors and try again.
```

</details>

Хосты centralServer и inetRouter долго перезагружались, поэтому получено сообщение об ошибке. Но это не повлияло на работу стенда, пакеты идут через узлы, прописанные в файлах `50-vagrant_{{ansible_hostname}}.yaml`:

`Office1Server`:
```
vagrant@office1Server:~$ traceroute 8.8.8.8
traceroute to 8.8.8.8 (8.8.8.8), 30 hops max, 60 byte packets
 1  _gateway (192.168.2.129)  1.134 ms  0.944 ms  1.002 ms
 2  192.168.255.9 (192.168.255.9)  1.823 ms  2.010 ms  1.579 ms
 3  192.168.255.1 (192.168.255.1)  3.138 ms  3.043 ms  5.301 ms
 4  10.0.2.2 (10.0.2.2)  5.188 ms  5.328 ms  5.325 ms
 5  * * *
 6  * * *
 7  * * *
 8  ler-cr01-ae2.1025.pen.mts-internet.net (212.188.11.93)  29.250 ms  3.431 ms  3.247 ms
 9  pstamb-cr02-eth-trunk6.tam.mts-internet.net (212.188.0.26)  6.161 ms  6.216 ms  6.032 ms
10  psnmich-cr02-eth-trunk1.rzn.mts-internet.net (212.188.0.24)  9.762 ms  9.797 ms  9.155 ms
11  a197-cr06-be2.msk.mts-internet.net (212.188.56.144)  15.156 ms  14.689 ms  14.373 ms
12  108.170.226.244 (108.170.226.244)  15.515 ms mag9-cr02-be14.77.msk.mts-internet.net (212.188.42.43)  14.820 ms  14.752 ms
13  108.170.250.130 (108.170.250.130)  16.348 ms 108.170.250.66 (108.170.250.66)  15.549 ms 108.170.250.130 (108.170.250.130)  16.027 ms
14  209.85.255.136 (209.85.255.136)  30.722 ms 142.250.238.214 (142.250.238.214)  33.770 ms 172.253.66.116 (172.253.66.116)  33.781 ms
15  142.250.233.0 (142.250.233.0)  30.223 ms 142.251.238.70 (142.251.238.70)  30.630 ms 172.253.66.108 (172.253.66.108)  28.955 ms
16  172.253.51.241 (172.253.51.241)  30.199 ms 142.250.56.215 (142.250.56.215)  29.404 ms 142.250.208.23 (142.250.208.23)  31.733 ms
17  * * *
18  * * *
19  * * *
20  * * *
21  * * *
22  * * *
23  * * *
24  * * *
25  * * *
26  dns.google (8.8.8.8)  33.301 ms  31.995 ms *
vagrant@office1Server:~$ 
```

`office2Server`:
```
vagrant@office2Server:~$ traceroute 8.8.8.8
traceroute to 8.8.8.8 (8.8.8.8), 30 hops max, 60 byte packets
 1  _gateway (192.168.1.1)  0.923 ms  1.402 ms  0.910 ms
 2  192.168.255.5 (192.168.255.5)  1.788 ms  2.273 ms  2.168 ms
 3  192.168.255.1 (192.168.255.1)  2.362 ms  2.844 ms  2.614 ms
 4  10.0.2.2 (10.0.2.2)  3.911 ms  5.575 ms  5.267 ms
 5  * * *
 6  * * *
 7  * * *
 8  ler-cr01-ae2.1025.pen.mts-internet.net (212.188.11.93)  3.872 ms  3.789 ms  2.497 ms
 9  pstamb-cr02-eth-trunk6.tam.mts-internet.net (212.188.0.26)  6.645 ms  6.631 ms  6.617 ms
10  psnmich-cr02-eth-trunk1.rzn.mts-internet.net (212.188.0.24)  9.857 ms  9.156 ms  9.135 ms
11  a197-cr06-be2.msk.mts-internet.net (212.188.56.144)  14.420 ms  14.399 ms  14.585 ms
12  108.170.250.129 (108.170.250.129)  15.832 ms mag9-cr02-be14.77.msk.mts-internet.net (212.188.42.43)  14.659 ms 108.170.250.33 (108.170.250.33)  16.854 ms
13  108.170.250.130 (108.170.250.130)  17.062 ms  17.153 ms  16.662 ms
14  142.251.237.154 (142.251.237.154)  33.444 ms 142.250.238.214 (142.250.238.214)  33.849 ms  34.955 ms
15  209.85.254.6 (209.85.254.6)  28.564 ms 172.253.66.108 (172.253.66.108)  33.059 ms 72.14.232.86 (72.14.232.86)  28.963 ms
16  172.253.51.237 (172.253.51.237)  33.302 ms 216.239.63.27 (216.239.63.27)  34.338 ms 216.239.58.53 (216.239.58.53)  32.851 ms
17  * * *
18  * * *
19  * * *
20  * * *
21  * * *
22  * * *
23  * * *
24  * * *
25  * * *
26  dns.google (8.8.8.8)  31.863 ms  31.637 ms  31.246 ms
vagrant@office2Server:~$ 
```

`centralServer`:
```
vagrant@centralServer:~$ traceroute -m 60 ya.ru
traceroute to ya.ru (5.255.255.242), 60 hops max, 60 byte packets
 1  _gateway (192.168.0.1)  0.771 ms  0.949 ms  0.413 ms
 2  192.168.255.1 (192.168.255.1)  1.371 ms  1.276 ms  1.527 ms
 3  10.0.2.2 (10.0.2.2)  2.276 ms  3.394 ms  3.324 ms
 4  * * *
 5  * * *
 6  corp-32-125.ptcomm.ru (85.234.32.125)  4.970 ms  2.700 ms  3.002 ms
 7  ler-cr01-ae2.1025.pen.mts-internet.net (212.188.11.93)  3.547 ms  3.844 ms  4.446 ms
 8  pstamb-cr02-eth-trunk6.tam.mts-internet.net (212.188.0.26)  7.600 ms  6.562 ms  6.354 ms
 9  psnmich-cr02-eth-trunk1.rzn.mts-internet.net (212.188.0.24)  9.648 ms  9.524 ms  9.712 ms
10  a197-cr06-be2.msk.mts-internet.net (212.188.56.144)  14.285 ms * *
11  a197-cr01-ae16.77.msk.mts-internet.net (212.188.56.137)  22.444 ms  22.387 ms  23.928 ms
12  a197-cr04-be31.77.msk.mts-internet.net (212.188.56.14)  14.361 ms  14.294 ms  14.090 ms
13  * a197-cr03-ae0.16.msk.mts-internet.net (212.188.55.2)  13.985 ms  14.040 ms
14  195.34.49.178 (195.34.49.178)  13.816 ms  13.968 ms  14.119 ms
15  * * *
16  a197-cr01-ae31.77.msk.mts-internet.net (212.188.56.13)  18.902 ms  18.540 ms  18.811 ms
17  as13238.asbr.router (212.188.33.199)  14.257 ms  14.255 ms  14.321 ms
18  * * vla-32z1-ae3.yndx.net (93.158.160.151)  36.793 ms
19  * * *
```
