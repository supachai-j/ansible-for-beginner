# Ansible Loops
เรื่องของลูปบน ansible เหมือนกันกับการเขียนโปรแกรมทั่วๆ ไปคือ การทำซ้ำ จนกว่าจะมีเงื่อนไข มาทำให้จบ
ตัวอย่างเช่น ถ้าเราต้องการทำอะไรสักอย่างซ้ำๆ โดยใช้ข้อมูลเป็นรายการอยู่ชุดหนึ่ง จะเป็นอย่างไร 
```
- 
    name: Create users
    hosts: localhost
    tasks: 
        - user: name=joe state=present
        - user: name=ravi state=present
        - user: name=mani state=present
        - user: name=kiran state=present
        - user: name=izana state=present
        - user: name=mike state=present
        - user: name=liza state=present
```
ถ้าใช้งาน loop จะง่ายขึ้น

```
- 
    name: Create users
    hosts: localhost
    tasks: 
        - user: name=joe state=present
           loop: 
            - joe
            - ravi
            - mani
            - kiran
            - izana
            - mike
            - liza
```

ถ้าเขียนให้เห็นภาพก็คือ loop จะทำให้เกิดแบบนี้

```
- 
    name: Create users
    hosts: localhost
    tasks: 

    -  var: item=joe
        user: name= "{{ item }}" state=present
    -  var: item=ravi
        user: name= "{{ item }}" state=present
    -  var: item=mani
        user: name= "{{ item }}" state=present
    -  var: item=kiran
        user: name= "{{ item }}" state=present
    -  var: item=izana
        user: name= "{{ item }}" state=present
    -  var: item=mike
        user: name= "{{ item }}" state=present
    -  var: item=liza
        user: name= "{{ item }}" state=present

```

### Loops Visualize
แล้วถ้ากรณีที่เราต้องการเพิ่ม ตัวแปร หรือค่าใน array ในการสร้าง loop จะเป็นอย่างไร เช่น
```
- 
    name: Create users
    hosts: localhost
    tasks: 
        - user: name=joe state=present  uid=1010
           loop: 
            - joe 1010
            - ravi 1011
            - mani 1012
            - kiran 1013
            - izana 1014
            - mike 1015
            - liza 1016
```
เราจะเปลี่ยนเป็น 
```
- 
    name: Create users
    hosts: localhost
    tasks: 
        - user: name= "{{ item.name }}" state=present uid="{{ item.uid }}"
           loop: 
            - name: joe 
               uid: 1010
            - name: ravi 
               uid: 1011
            - name: mani 
               uid: 1012
            - name: kiran 
               uid: 1013
            - name: izana 
               uid: 1014
            - name: mike 
               uid: 1015
            - name: liza 
               uid: 1016
```

จากข้างต้นจะเขียนอธิบายได้เป็นแบบนี้ 
```
- 
    name: Create users
    hosts: localhost
    tasks: 

    -  var: 
            item:
                name: joe
                uid: 1010
        user: name= "{{ item.name }}" state=present uid="{{ item.uid }}"
    -  var: item=ravi
        user: name= "{{ item.name }}" state=present uid="{{ item.uid }}"
    -  var: item=mani
        user: name= "{{ item.name }}" state=present uid="{{ item.uid }}"
    -  var: item=kiran
        user: name= "{{ item.name }}" state=present uid="{{ item.uid }}"
    -  var: item=izana
        user: name= "{{ item.name }}" state=present uid="{{ item.uid }}"
    -  var: item=mike
        user: name= "{{ item.name }}" state=present uid="{{ item.uid }}"
    -  var: item=liza
        user: name= "{{ item.name }}" state=present uid="{{ item.uid }}"
```

### With_*

```
- 
    name: Create users
    hosts: localhost
    tasks:
        - 
            user: name= "{{ item }} state=present
            loop:
                - joe
                - george
                - ravi
                - mani
```

```
    name: Create users
    hosts: localhost
    tasks:
        - 
            user: name= "{{ item }} state=present
            with_items:
                - joe
                - george
                - ravi
                - mani
```
ซึ่ง with_* ถูกกำหนดใช้งานหลากหลายเช่น
```
    name: View Config Files
    hosts: localhost
    tasks:
        - 
           debug: var=item
            with_file:
                - "/etc/hosts"
                - "/etc/resolv.conf"
                - "/etc/ntp.conf"
```

```
    name: Get from multiple URLs
    hosts: localhost
    tasks:
        - 
           debug: var=item
            with_url:
                - "https://site1.com/get-servers"
                - "https://site2.com/get-servers"
                - "https://site3.com/get-servers"
```

```
    name: Check multiple mongodb
    hosts: localhost
    tasks:
        - 
           debug: msg="DB={{ item.database }} PID= {{ item.pid }}"
            with_mongodb:
                - database: dev
                   connection_string: "mongodb://dev.mongo/"
                - database: prod
                   connection_string: "mongodb://prod.mongo/"
```

โดยในส่วนของ with_* ก็จะมีเยอะอยู่พอสมควร

with_items
with_file
with_url
with_mongodb
with_dict
with_etcd
with_env
with_filetree
with_ini
with_inventory_hostnames
with_k8s
with_manifold
with_nested
with_nios
with_openshfit
with_password
with_pipe
with_redis
with_sequence
with_skydive
with_subelements
with_template
with_together
with_varnames


### Ansible Loop Pratices Testing
Ansible Loops #1
The playbook currently runs an echo command to print a fruit name. 
Apply a loop directive (with_items) to the task to print all fruits defined in the fruits variable.

```
-
    name: 'Print list of fruits'
    hosts: localhost
    vars:
        fruits:
            - Apple
            - Banana
            - Grapes
            - Orange
    tasks:
        -
            command: 'echo "{{ item }}"'
            with_items: '{{ fruits }}'
```

Ansible Loops #2
To a more realistic use case. We are attempting to install multiple packages using yum module.
The current playbook installs only a single package.

```
-
    name: 'Install required packages'
    hosts: localhost
    vars:
        packages:
            - httpd
            - binutils
            - glibc
            - ksh
            - libaio
            - libXext
            - gcc
            - make
            - sysstat
            - unixODBC
            - mongodb
            - nodejs
            - grunt
    tasks:
        -
            yum:
                name: "{{ items }}" 
                state: present
            with_items: '{{ packages }}'
```