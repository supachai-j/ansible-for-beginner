# Ansible Conditionals
ในการเขียนโปรแกรมหรือการเขียน playbook ของ ansible เราสามารถเขียนเงื่อนไขตามที่เรากำหนดได้ โดยตัวอย่างเช่น
เรามีต้องการติดตั้ง nginx บน เครื่องเป้าหมาย target แต่มีเงื่อนไขว่า ถ้าเป็น debian OS ให้ใช้ module apt แล้วสำหรับ Redhat OS ให้ใช้ module yum เป็นตัว 
nginx-debian.yml
```
---
-
    name: Install NGINX
    hosts: debian_hosts
    tasks:
        - 
            name: Install NGINX on Debian
            apt:
                name: nginx
                state: present
```

nginx-redhat.yml
```
---
-
    name: Install NGINX
    hosts: redhat_hosts
    tasks:
        - 
            name: Install NGINX on Redhat
            yum:
                name: nginx
                state: present
```

ปัญหาที่เจอคือ เราจะมีไฟล์ yml มากมายแต่ OS แยกกันถูกต้องไหมถ้า แต่ถ้าเราใช้ เงื่อนไข (condition) ได้มันดีกว่าไหม และร่วมเป็นไฟล์เดียวกันได้เลย

## Conditional - when
อันแรก เงื่อนไข แบบ When

nginx-os.yml
```
---
-
    name: Install NGINX
    hosts: all
    tasks:
        - 
            name: Install NGINX on Debian
            apt:
                name: nginx
                state: present
            when: ansible_os_family == "debian" and
                        ansible_distribution_version == "16.04"
        - 
            name: Install NGINX on Redhat
            yum:
                name: nginx
                state: present 
            when:  ansible_os_family == "RedHat" or 
                        ansible_os_family == "SUSE"     
```

## Conditional in Loops
เงื่อนในการทำ Loops

```
---
- 
    name: Install Software
    hosts: all
    vars:
        packages:
            - name: nginx
               required: True
            - name: mysql
               required: True
            - name: apache
               required: False
    tasks:
        - 
            name: Install "{{ item.name }}" on Debian
            apt:
                name: "{{ item.name }}"
                state: present
            when: item.required == True     <------------ สามารถใช้เงื่อนไข when ในการเลือกได้เช่นกัน
            loop: "{{ packages }}" 
```

จะเหมือนกับว่า เราเขียนแต่ตัวแยกกันดังนี้
```
        - 
            name: Install "{{ item.name }}" on Debian
            vars:
                item:
                    name: nginx
                    required: True
            apt:
                name: "{{ item.name }}"
                state: present
            when: item.required == True
```
```
        - 
            name: Install "{{ item.name }}" on Debian
            vars:
                item:
                    name: mysql
                    required: True
            apt:
                name: "{{ item.name }}"
                state: present
            when: item.required == True
```
```
        - 
            name: Install "{{ item.name }}" on Debian
            vars:
                item:
                    name: apache
                    required: False
            apt:
                name: "{{ item.name }}"
                state: present
            when: item.required == True
```

## Conditional & Register

```
---
- 
    name: Check status of a service and email if its down
    hosts: localhost
    tasks: 
        - command: service httpd status
           register: result
        
        - mail:
            to: admin@company.com
            subject: Service Alert
            body: Httpd Service is down

            when: result.stdout.find('down') != -1
```


## Ansible Conditionals Practice Test

### Ansible Variables #1
The given playbook attempts to start mysql service on all_servers. 
Use the when condition to run this task if the host (ansible_host) is the database server.

Refer to the inventory file to identify the name of the database server.

```
# Database Servers
db1 ansible_host=server4.company.com ansible_connection=winrm ansible_user=administrator ansible_ssh_pass=Password123!

```

```
-
    name: 'Execute a script on all web server nodes'
    hosts: all_servers
    tasks:
        -
            service: 'name=mysql state=started'
            when: 'ansible_host=="server4.company.com"'
```

### Ansible Variables #2
The playbook has a variable defined - age. The two tasks attempt to print if I am a child or an Adult.
Use the when conditional to print if I am a child or an Adult based on weather my age is < 18 (child) or >= 18 (Adult).

```
-
    name: 'Am I an Adult or a Child?'
    hosts: localhost
    vars:
        age: 25
    tasks:
        -
            command: 'echo "I am a Child"'
            when: 'age < 18'
        -
            command: 'echo "I am an Adult"'
            when: 'age >= 18 '

```

### Ansible Variables #3
The given playbook attempts to add an entry into the /etc/resolv.conf file for nameserver.


First, we run a command using the shell module to get the contents of /etc/resolv.conf file and then we add a new line containing the name server data into the file. However, when this playbook is run multiple times, it keeps adding new entries of same line into the resolv.conf file.

1. Add a register directive to store the output of the first command to variable command_output

2. Then add a conditional to the second command to check if the output contains the name server (10.0.250.10) already. Use command_output.stdout.find(<IP>) == -1

Note: A better way to do this would be to use the lineinfile module. This is just for practice.

Note: shell and command modules are similar in that they are used to execute a command on the system. However shell executes the command inside a shell giving us access to environment variables and redirection using >>

```
-
    name: 'Add name server entry if not already entered'
    hosts: localhost
    tasks:
        -
            shell: 'cat /etc/resolv.conf'
            register: command_output
        -
            shell: 'echo "nameserver 10.0.250.10" >> /etc/resolv.conf'
            when: 'command_output.stdout.find("10.0.250.10") == -1'
```