# Ansible Roles
Roles บน Ansible มันคือ เปรียบเทียบง่ายๆ ในชีวิตประจำวันเรา ตัวอย่างเช่น

เราต้องการสร้างคนหนึ่ง ให้มีอาชีพ (Roles) เป็น หมอ หรือ วิศวกร เราจะต้องทำอะไรกับคนๆ นั้นบ้างเพื่อให้สามารถ มีอาชีพ (Role) ที่เราต้องการได้

Docter
```
- Go to medical school
- Earn medical degree
- Complete Residency Program
- Obtain License
```

Engineer
```
- Go to engineering school
- Earn bachelor's degree
- Gain field experience
- Gain postgraduate degree
```

แล้วถ้าเป็นมุมมองของ System หรือ infra ต่างๆ บ้างล่ะ เช่น

Mysql Server
```
- Installing Pre-requisites
- Installing msysql packages
- Configuring mysql service
- Configuring database and users
```

Nginx Server
```
- Installing Pre-requisites
- Installing nginx packages
- Configuring nginx service
- Configuring custom web pages
```

แล้วถ้าเป็น ansible playbook ล่ะ

```
- 
    name: Install and Configure MySQL
    hosts: db-server
    tasks: 
        - 
            name: Install Pre-Requisites
            yum:
                name: pre-req-packages
                state: present
        - 
            name: Install MySQL Packages
            yum:
                name: mysql
                state: present
        - 
            name: Configure Database
            mysql: 
                name: db1
                state: present
```

### Role 
ในมุมของการเขียน Ansible Role กันบ้าง

```
- 
    name: Install and Configure MySQL
    hosts: db-server01..... db-server10
    roles: 
        - mysql
```

MySQL Role
```
    tasks: 
        - 
            name: Install Pre-Requisites
            yum:
                name: pre-req-packages
                state: present
        - 
            name: Install MySQL Packages
            yum:
                name: mysql
                state: present
        - 
            name: Configure Database
            mysql: 
                name: db1
                state: present
```

พอมันเป็นโครงสร้างลักษณะ Role แล้ว เราสามารถนำ Role ต่างๆ ที่เขียนขึ้น กลับมาใช้งานได้ (Re-user) หรือการแชร์​ในองค์กร (Organize) หรือแชร์ Public ให้คนทั่วโลกใช้ได้ Ansible Galaxy ในโครงสร้างของ Role ไม่ได้มีแค่ Tasks เท่านั้นยังมี

MySQL Role
- tasks
- vars
- defaults
- handlers
- templates

เราสามารถศึกษา หรือนำ Ansible Role มาใช้งานได้ โดยไปดูที่ [Ansible Galaxy](https://galaxy.ansible.com)

```
$ ansible-galaxy init mysql
```

โดยก็จะมีโครงการตัวอย่างเช่น
```
mysql 
    - README.md
    - templates
    - tasks
    - handlers
    - vars
    - defaults
    - meta
```

จากนั้นเราก็ทำการสร้าง ansible playbook เพื่อนำ ansible role มาใช้งานได้เลย
my-playbook/playbook.yml
```
-
    name: Install and Configure MySQL
    hosts: db-server
    roles:
        - mysql
```

โดยปกติโฟเดอร์ของ role จะอยู่ที่ /etc/ansible/role หรือสามารถกำหนดใน ansible.cfg ได้โดย
```
roles_path=/etc/ansible/roles
```

### Find Ansible Roles 
เราสามารถเช้าไปที่ website ansible galaxy แล้วค้นหา ได้เลย หรือใช้คำสั่ง command ก็ได้ 
```
ansible-galaxy search mysql
```

### Use Role 
ในการใช้งาน ansible role เราใช้คำสั่งติดตั้ง role บน local โดย
```
ansible-galaxy install geerlingguy.mysql
```

playbook.yml
```
- 
    name: Install and Configure MySQL
    hosts: db-server
    roles:
        - geerlingguy.mysql
```
หรือ

```
- 
    name: Install and Configure MySQL
    hosts: db-server
    roles:
        - role: geerlingguy.mysql
           become: yes
           vars:
                mysql_user_name: db-user
```

playbook-all-in-one.yml
```
- 
    name: Install and Configure MySQL
    hosts: db-and-webserver
    roles:
        - geerlingguy.mysql
        - nginx
```

playbook-distributed.yml
```
- 
    name: Install and Configure MySQL
    hosts: db-server
    roles:
        - geerlingguy.mysql
    
    name: Install and Configure Web Server
    hosts: web-server
    roles:
        - nginx
```

### List Role
```
$ ansible-galaxy list
- geerlingguy.mysql
- kodeklound1.mysql
```

```
$ ansible-config dump | grep ROLE
```

```
$ ansible-galaxy install geerlingguy.mysql -p ./roles
```