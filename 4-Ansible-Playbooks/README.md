# Ansible Playbooks
Ansible Playbooks มันคือการกำหนดรูปแบบการสั่งงานหรือภาษาในรูปแบบ Orchestrator ว่าจะให้ Ansible ทำอะไรหรืออย่างไร?
ตัวอย่างเช่น ในกรณีที่เราต้องสั่งให้ target ทำงานด้วยคำสั่ง ansible
```
# Simple Ansible Playbook

- Run command1 on server1
- Run command2 on server2
- Run command3 on server3
- Run command4 on server4
- Run command5 on server5
- Run command6 on server6
- Run command7 on server7
- Run command8 on server8
- Run command9 on server9
- Restarting server1
- Restarting server2
- Restarting server3
- Restarting server4
- Restarting server5
- Restarting server6
- Restarting server7
- Restarting server8
- Restarting server9
```
และถ้าเริ่มซับซ้อนมากขึ้นล่ะ ตัวอย่างเช่น
```
# Complex Ansible Playbook

- Deploy 50 VMs on Public Cloud
- Deploy 50 VMs on Private Cloud
- Provision Storage to all VMs
- Setup Network Configuration on Private VMs
- Setup Cluster Configuration
- Configure Web server on 20 Public VMs
- Configure DB server on 20 Private VMs
- Setup Loadbalancing between web server VMs
- Setup Monitoring components
- Install and Configure backup clients on VMs
- Update CMDB database with new VM Information
```
ขนาดพิมพ์เอง ยังเหนื่อยเลยครับ ฮ่าๆๆๆ 

### Playbook
Playbook คือ YAML ไฟล์​เดียว ที่ถูกเขียนกำหนดการทำงานต่างๆ ไว้ เช่น playbook.yml (YAML format)

- Playbook is a single YAML file.
    - Play is defines a set of activities (Tasks) to be run on hosts
        - Task is an action to be performed on the host
            - Execute a command
            - Run a script
            - Install a package
            - Shutdown/Restart
 
 playbook.yml
 ```
 - 
    name: Play 1
    hosts: localhost
    tasks:
        - name: Execute command 'date'
          command: date

        - name: Execute script on server
           script: test_script.sh
        
        - name: Install httpd service
          yum:
             name: httpd
             state: present
        
        - name: Start web server
           service:
             name: httpd
             state: started
 ```

 ### Playbook Format
 playbook.yml
 ```
  - 
    name: Play 1
    hosts: localhost
    tasks:
        - name: Execute command 'date'
          command: date

        - name: Execute script on server
           script: test_script.sh
-
    name: Play 2
    hosts: localhost
    tasks:
        - name: Install httpd service
          yum:
             name: httpd
             state: present
        
        - name: Start web server
           service:
             name: httpd
             state: started
 ```

ถ้าสังเกตดูจะเห็นว่า - บนไฟล์​เป็นรูปแบบของ List และส่วนของ name, hosts เป็นลักษณะของ Dictionary ครับ

### Hosts / Inventory
playbook.yml
 ```
 - 
    name: Play 1
    hosts: localhost  <------------ เป็นการเลือก target ที่อ้างอิงจากไฟล์​ inventory เป็น host หรือ group 
    tasks:
        - name: Execute command 'date'
          command: date

        - name: Execute script on server
           script: test_script.sh
        
        - name: Install httpd service
          yum:
             name: httpd
             state: present
        
        - name: Start web server
           service:
             name: httpd
             state: started
 ```
 Inventory ไฟล์
 ```
localhost

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

### Module บน Ansible Playbook ไฟล์
playbook.yml
 ```
 - 
    name: Play 1
    hosts: localhost  
    tasks:
        - name: Execute command 'date'
          command: date   <--------------- ansible module ชื่อ command 

        - name: Execute script on server
           script: test_script.sh  <--------------- ansible module ชื่อ script 
        
        - name: Install httpd service
          yum:  <--------------- ansible module ชื่อ yum 
             name: httpd
             state: present
        
        - name: Start web server
           service:  <--------------- ansible module ชื่อ service 
             name: httpd
             state: started
 ```
คำสั่งเพื่อดู ansible docuement
```
 ansible-doc -l
 ansible-doc yum
```

### Run
ในการสั่งให้ ansible playbook ทำงาน คือ ansible-playbook ตามด้วยไฟล์​ playbook yml
- Execute Ansible Playbook
- Syntax: ansible-playbook <playbook-file-name>
ตัวอย่างเช่น
```
ansible-playbook playbook.yml
ansible-playbook --help
```

ลองดูความแตกต่างระหว่าง ansible และ ansible-playbook 
#### ansible
```
ansible <hosts> -a <command>
ansible all -a "/sbin/reboot"
ansible <hosts> -m <module>
ansible target1 -m ping
```
#### ansible-playbook
```
ansible-playbook <playbook name>
ansible-playbook playbook-webserver.yml
```

## Ansible Playbook Demo
ทำการ ssh เข้าไปที่ master
```
$vagrant ssh master

[vagrant@master test-project]$ ansible all -m ping -i inventory.txt
target1 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python"
    }, 
    "changed": false, 
    "ping": "pong"
}
target2 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python"
    }, 
    "changed": false, 
    "ping": "pong"
}
[vagrant@master test-project]
```
ทำการสร้างไฟล์​ playbook-pingtest.yml
```
-
  name: Test connectivity to target servers
  hosts: all
  tasks:
    - name: Ping test
       ping: 
```
ลอง ansible-playbook run
```
[vagrant@master test-project]$ cat playbook-pingtest.yml 
-
  name: Test connectivity to target servers
  hosts: all
  tasks:
   - name: Ping Test
     ping:
[vagrant@master test-project]$
[vagrant@master test-project]$ ansible-playbook playbook-pingtest.yml -i inventory.txt 

PLAY [Test connectivity to target servers] ***********************************************************************************************************************************************************

TASK [Gathering Facts] *******************************************************************************************************************************************************************************
ok: [target2]
ok: [target1]

TASK [Ping Test] *************************************************************************************************************************************************************************************
ok: [target2]
ok: [target1]

PLAY RECAP *******************************************************************************************************************************************************************************************
target1                    : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
target2                    : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   

[vagrant@master test-project]$
```

## Tips and Tricks for Ansible Playbook Develop

YAML Lint คือ เว็บไซต์ที่ใช้ตรวจ Syntax ของ YAML ไฟล์ โดยเข้าไปที่ [YAML LINT](http://www.yamllint.com/)
```
- 
  name: Test Connectivity 
  hosts: all
  tasks: 
    - name: "Ping Test"
    ping: 
```

ถ้าใช้ Atom IDE ในการพัฒนา ก็จะมี Package ดังนี้
- [linter-js-yaml](https://atom.io/packages/linter-js-yaml)
```
apm install linter-js-yaml
```
- [remote-sync](https://atom.io/packages/remote-sync)
```
apm install remote-sync
```

ลองทำการใช้ ansible-playbook ทำการ copy file จาก master ไปยัง target ทุกตัว
```
- 
  name: Copy file to target servers
  hosts: all
  tasks:
    - name: Copy file
       copy: 
            src: /tmp/test-file.txt
            dest: /etc/foo.txt
```
สร้างไฟล์​ไว้รอที่ master 
/tmp/test-file.txt
```
Hello! This is a sample content in test file
```
ลองทำการ ansible-playbook
```
[vagrant@master test-project]$ ansible-playbook playbook-copy-file.yml -i inventory.txt 

PLAY [Copy file to target servers] *******************************************************************************************************************************************************************

TASK [Gathering Facts] *******************************************************************************************************************************************************************************
ok: [target1]
ok: [target2]

TASK [Copy file] *************************************************************************************************************************************************************************************
changed: [target1]
changed: [target2]

PLAY RECAP *******************************************************************************************************************************************************************************************
target1                    : ok=2    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
target2                    : ok=2    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   

[vagrant@master test-project]$

[vagrant@target1 ~]$ cat /etc/foo.txt 
Hello! This is a sample content in test file
[vagrant@target1 ~]$ 

[vagrant@target2 ~]$ cat /etc/foo.txt 
Hello! This is a sample content in test file
[vagrant@target2 ~]$
[vagrant@target2 ~]$ sudo rm -f /etc/foo.txt 
[vagrant@target2 ~]$

[vagrant@master test-project]$ ansible-playbook playbook-copy-file.yml -i inventory.txt 

PLAY [Copy file to target servers] *******************************************************************************************************************************************************************

TASK [Gathering Facts] *******************************************************************************************************************************************************************************
ok: [target2]
ok: [target1]

TASK [Copy file] *************************************************************************************************************************************************************************************
ok: [target1]
changed: [target2]

PLAY RECAP *******************************************************************************************************************************************************************************************
target1                    : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
target2                    : ok=2    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   

[vagrant@master test-project]$ 
```

### Ansible Practice Tast
```
-
    name: 'Stop the web services on web server nodes'
    hosts: web_nodes
    tasks:
        -
            name: 'Stop the web services on web server nodes'
            command: 'service httpd stop'
-
    name: 'Shutdown the mysql services on database nodes'
    hosts: db_nodes
    tasks:
        - 
            name: 'Shutdown the mysql services'
            command: 'service mysql stop'
-
    name: 'Restart all servers'
    hosts: all_nodes
    tasks:
        -
            name: 'Restart all servers'
            command: '/sbin/shutdown -r'
-
    name: 'Start the mysql service on database nodes'
    hosts: db_nodes
    tasks:
        - 
            name: 'Start the mysql services'
            command: 'service mysql start'
-
    name: 'Start the web service on web server nodes'
    hosts: web_nodes
    tasks:
        - 
            name: 'Start the web services on web server nodes'
            command: 'service httpd start'
```