# Ansible Modules

## Modules
Ansible Modules คือ ความสามารถที่ทำให้ ansible สามารถทำงานได้ตามสิ่งที่เราต้องการอยากให้ทำ 
ในส่วนของ modules จะถูกพัฒนาจากหลายๆ Vendor/Products ตัวอย่างเช่น ในส่วนของ Operating System (OS) หรือกลุ่มของฝั่ง Network และ Infra ก็เช่นกัน

### System 
- User
- Group
- Hostname
- Iptables
- Lvg
- Lvol
- Make
- Mount
- Ping
- Timezone
- Systemd
- Service
### Commands
- Command
- Expect
- Raw
- Script
- Shell
### Files
- Acl
- Archive
- Copy
- File
- Find
- Lineinfile
- Replace
- Stat
- Template
- Unarchive
### Database
- Mongodb
- Mssql
- Mysql
- Postgresql
- Proxysql
- vertica
### Cloud
- Amazon
- Atomic
- Azure
- Centrylink
- Cloudscale
- Cloudstack
- Digital Ocean
- Docker
- Google
- Linode
- Openstack
- Rackspace
- Smartos
- Softlayer
- VMware
### Windows
- Win_copy
- Win_command
- Win_domain
- Win_file
- Win_iis_website
- Win_msg
- Win_msi
- Win_package
- Win_ping
- Win_path
- Win_robocopy
- Win_regedit
- Win_shell
- Win_service
- Win_user
- And more

ช้อมูลเพิ่มเติม [Ansible All Modules ](https://docs.ansible.com/ansible/2.9/modules/list_of_all_modules.html)

## Command module
เป็นการสั่ง execute  คำสั่งบนเครื่องปลายทาง (target host)

ตัวอย่างการใช้งาน
```
- 
    name: Play 1
    hosts: localhost
    tasks:  
        -
            name: 'Execute command "date" '
            command: date
        -
            name: Display resolv.conf contents
            command: cat /etc/resolv.conf
        - 
            name: Display resolv.conf contents
            command: cat resolv.conf chdir=/etc
        -
            name: Create folder directory
            command: mkdir /folder creates=/folder
```
chdir คือ การใช้คำสั่ง cd ไปยัง directory ก่อนการ execute command
ข้อมูลเพิ่มเติม [command-module](https://docs.ansible.com/ansible/2.9/modules/command_module.html#command-module)

## Script module
Runs a local script on a remote node after transferring it
1. Copy script to remote systems
2. Execute script on remote systems

playbook.yml
```
- 
  name: Play 1
  hosts: localhost
  tasks:
    - 
        name: Run a script on remote server
        script: /some/local/script.sh -arg1 -arg2

```

## Service module
Manage Services - Start, Stop, Restart

playbook-1.yml
```
- 
    name: Start Services in order
    hosts: localhost
    tasks:
        -
            name: Start the database service
            service: name=postgresql state=started
```
playbook-2.yml
```
- 
    name: Start Services in order
    hosts: localhost
    tasks:
        -
            name: Start the database service
            service: 
                name: postgresql 
                state: started
```
ทั้งสองไฟล์​ให้ผลลัพธ์ที่เหมือนกัน

### Idempotency
ทำไมต้องใช้คำว่า "started" แทนการใช้คำว่า "start" ?
```
"Start" the service httpd
"Started" the service httpd
```
มันหมายถึงความมั่นใจว่า httpd อยู่ในสถานะ started ไปแล้ว
ถ้า httpd ไม่ได้เป็นสถานะ started อยู่ ก็ต้องทำการ start มันขึ้นมา
แต่ถ้า httpd อยู่ในสถานะ started อยู่แล้ว ก็ไม่ต้องทำอะไร (do nothing)


## lineinfile module
Search for a line in a file and replace it or add it if it doesn't exist.

/etc/resolv.conf
```
nameserver 10.1.250.1
nameserver 10.1.250.2
```
playbook.yml
```
- 
    name: Add DNS server to resolv.conf
    hosts: localhost
    tasks:
        -
            lineinfile:
                path: /etc/resolv.conf
                line: 'nameserver 10.1.250.10'
```

/etc/resolv.conf
```
nameserver 10.1.250.1
nameserver 10.1.250.2
nameserver 10.1.250.10
```

จะคล้ายๆ กับไฟล์​ shellscript ที่เขียนแบบนี้
script.sh
```
# Simple script

echo "nameserver 10.1.250.10" >> /etc/resolv.conf
```

แต่ความพิเศษของ Ansible ที่แตกต่างจาก shellscript คือเมื่อเราทำการ run หรือ execute ซ้ำๆๆ 
Ansible จะไม่ทำการเพิ่มหรือเขียนซ้ำๆ บรรทัดลงไปเหมือนกับ shellscript แต่จะคอยดูว่าเป็นไปตามที่กำหนดหรือไม่
ถ้าไม่ จะดำเนินการ แต่ถ้ามีอยู่หรือกำหนดไว้แล้ว มันก็จะไม่ทำและดำเนินการคำสั่งต่อไปเลย

/etc/resolv.conf  กรณีที่เรา execute shellscript หลายๆ ครั้ง
````
nameserver 10.1.250.1
nameserver 10.1.250.2
nameserver 10.1.250.10
nameserver 10.1.250.10
nameserver 10.1.250.10
````

## Ansible Module Practice Test
```
-
    name: 'Execute a script on all web server nodes and start httpd service'
    hosts: web_nodes
    tasks:
        -
            name: 'Update entry into /etc/resolv.conf'
            lineinfile:
                path: /etc/resolv.conf
                line: 'nameserver 10.1.250.10'
        -
            name: 'Add new user'
            user:
                name: web_user
                uid: 1040
                group: developers
        -
            name: 'Execute a script'
            script: /tmp/install_script.sh
        -
            name: 'Start httpd service'
            service:
                name: httpd
                state: present
```