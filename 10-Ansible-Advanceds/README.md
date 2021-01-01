# Ansible Advanced Topics
- Preparing Windows  Server
- Ansible-Galaxy
- Patterns
- Dynamic Inventory
- Developing Custom Modules

## Preparing Windows
- Ansible Control Machine can only be linux and windows
- Windows Machine can be targets of Ansible and thus be part of automation
- Ansible connect to a windows machine using winrm.
- Requirement:
    - pywinrm module installed on the ansible control machine - pip install "pywinrm >= 0.2.2"
    - Setup WinRM - example/scripts/ConfigureRemotingForAnsible.ps1
    - Different modes to authentication:
        - Basic/Cerficate/Kerberos/CredSSP

[ansible-for-windows-target](https://docs.ansible.com/ansible/latest/user_guide/windows.html)

## Ansible Galaxy 
เป็นศูยน์ร่วมของ ansible role ที่ถูกแชร์กันทั่วโลก เราสามารถติดตั้ง ansible role ต่างๆ มาใช้งานได้

[ansible-galaxy](https://galaxy.ansible.com/)

## Patterns

```
# Simple Ansible Playbook1.yml
- 
    name: Play 1
    hosts: localhost
    tasks:
        - 
            name: Execute command 'data'
            command: data
        - 
            name: Excute script on server
            script: test_script.sh
        -
            name: Install httpd service
            yum:
                name: httpd
                state: present
        -
            name: Start web server
            service:
                name: httpd
                state: started
```
ในการเลือก target hosts โดยสามารถใช้วิธีการเลือกได้หลายแบบ 
- Host1, Host2, Host3
- Group1, Host1
- Host*
- *.company.com

อ้างอิงเพิ่มเติม [ansible-patterns](https://docs.ansible.com/ansible/latest/user_guide/intro_patterns.html#using-patterns)

## Dynamic Inventory 

โดยก่อนหน้านี้เราจะเขียนไฟล์​ inventory แบบ static ตัวอย่างเช่น
```
# Simple Inventory File

server1.company.com
server2.company.com

[mail]
server3.company.com
server4.company.com

[db]
server5.company.com
server6.company.com

[web]
server7.company.com
server8.company.com
```

ในการเรียกใช้งาน inventory แบบ static
```
ansible-playbook -i inventory.txt playbook.yml
```

แต่ ansible สามารถเรียกใช้งาน inventory แบบ dynamic ได้โดยอ้างอิงไปยัง script แทนการเรียกไฟล์​ 
```
ansible-playbook -i inventory.py playbook.yml
```
โดยการเรียกใช้ script อาจจะไปเรียก inventory ของ aws, azure, openstack และอื่นๆ นำมาใช้เป็น inventory ให้กับ ansible ได้

## Custom Modules
ใน ansible เราสามารถพัฒนา module สำหรับใช้งานได้ 

ข้อมูลเพิ่มเติม [ansible-developing-module](https://docs.ansible.com/ansible/latest/dev_guide/developing_modules_general.html)


## Demo Project Deploy with Ansible

ตัวอย่างการใช้ Ansible ทำการ Deploy Project หนึ่งๆ เป็น Web E-Comm โดยใช้งานบน LAMP 
- Linux
- Apache
- MariaDB
- Php

ขั้นตอนมีดังนี้
1. Install Firewall
2. Install httpd, Configure httpd, Configure Firewall, Start httpd
3. Install MariaDB, Configure MariaDB, start MariaDB, Configure Firewall, Configure Database, Load Data
4. Install Cofigure Code

เปลี่ยนให้เหมาะสมในรูปแบบของ ansible

- Install Firewall 
```
$ sudo yum install firewalld
$ sudo service firewalld start
$ sudo systemctl enable firewalld
```
- Install MariaDB
```
$ sudo yum install mariadb-server
```
- Configure MariaDB
```
$ sudo vi /etc/my.cnf   # configure the file with the right port
```
- Start Maria DB
```
$ sudo service mariadb start
$ sudo systemctl enable mariadb
```
- Configure Firewall
```
$ sudo firewall-cmd --permanent --zone=public --add-port=3306/tcp
$ sudo firewall-cmd --reload
```
- Configure Database
```
$ mysql
MariaDB > CREATE DATABASE ecomdb;
MariaDB > CREATE USER 'ecomuser'@'localhost' IDENTIFIED BY 'ecompassword';
MariaDB > GRANT ALL PRIVILEGES ON *.* TO 'ecomuser'@'localhost';
MariaDB > FLUSH PRIVIEGES;
```
- Load Data
```
$ mysql < db-load-script.sql
```
- Install httpd 
- Install php
```
$ sudo yum install -y httpd php php-mysql
```
- Configure Firewall
```
$ sudo firewall-cmd --permanent --zone=public --add-port=80/tcp
$ sudo firewall-cmd --reload
```
- Configure httpd
```
$ sudo vi /etc/httpd/conf/httpd.conf #
   # configure DirectoryIndex of use index.php instead of index.html
```
- Start httpd
```
$ sudo service httpd start
$ sudo systemctl enable httpd
```
- Download Code
```
$ sudo yum install -y git
$ git clone https://github.com/<application>.git /var/www/html/
```
- Test
```
$ curl http://localhost
```

## Deployment Model 
1. Single Node
2. Multi Nodes
    - Web Server 1 node
    - DB 1 node

ถ้าในการอ้างอิง ip address ของ web server ไปยัง db จะเป็นอย่างไร

