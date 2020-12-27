# Ansible Variable
variable หรือตัวแปร เป็นวิธีการหนึ่งในการเก็บข้อมูลให้แต่ละเครื่อง target hosts โดยเราสามารถนำตัวแปรที่เก็บค่าต่างๆ ของแต่ละเครื่องมาทำงานให้เป็นไปตามเงื่อนไขที่เรากำหนดได้

ตัวอย่างเช่น
ไฟล์​ inventory.txt
```
web1 ansible_host=server1.company.com ansible_connection=ssh ansible_ssh_pass=P@ssW
db ansible_host=server2.company.com ansible_connection=winrm ansible_ssh_pass=P@s
web2 ansible_host=server3.company.com ansible_connection=ssh ansible_ssh_pass=P@ssW
```

ไฟล์​ playbook.yml
```
- 
    name: Add DNS server to resolv.conf
    hosts: localhost
    vars:
        dns_server: 10.1.250.10
    tasks:
        - 
            lineinfile:
            patch: /etc/resolv.conf
            line: 'nameserver 10.1.250.10'
```
ไฟล์​ variables
```
variable1: value1
variable2: value2
```

ไฟล์​ playbook.yml ที่มีการใช้งาน variables
```
- 
    name: Add DNS server to resolv.conf
    hosts: localhost
    vars:
        dns_server: 10.1.250.10
    tasks:
        - 
            lineinfile:
            patch: /etc/resolv.conf
            line: 'nameserver {{ dns_server }}'
```

ลองมาดูตัวอย่าง การใช้งาน ansible variable โดย
set-firewall-configure.yml
```
-
    name: Set Firewall Configurations
    hosts: web
    tasks:
        -
            firewalld:
                service: https
                permanent: true
                state: enabled
        - 
            firewalld:  
                port: 8081/tcp
                permanent: true
                state: disabled
        - 
            firewalld:
                port: 161-162/udp
                permanent: true
                state: disabled
        - 
            firewalld:
                source: 192.0.2.0/24
                Zone: internal
                state: enabled
```

Simple Inventory File Variables
```
web http_port=8081  snmp_port=161-162  inter_ip_range=192.0.2.0
```

set-firewall-configure.yml
```
-
    name: Set Firewall Configurations
    hosts: web
    tasks:
        -
            firewalld:
                service: https
                permanent: true
                state: enabled
        - 
            firewalld:  
                port: '{{ http_port }}'/tcp
                permanent: true
                state: disabled
        - 
            firewalld:
                port: '{{ snmp_port }}'/udp
                permanent: true
                state: disabled
        - 
            firewalld:
                source: '{{ inter_ip_range }}'/24
                Zone: internal
                state: enabled
```

หรือ Simple variable file ตัวอย่างเช่น web.yml
```
http_port: 8081
snmp_port: 161-162
inter_ip_range: 192.0.2.0
```

### Jinja2 Templating
```
{{          }}

source: {{ inter_ip_range }}    <-------------- ไม่ถูกต้อง
source: '{{ inter_ip_range }}'   <-------------- ถูกต้อง
source: SomThing{{ inter_ip_range }}Somthing   <-------------- ถูกต้อง
```

### Ansible Variable Practice Testing
```
-
    name: 'Update nameserver entry into resolv.conf file on localhost'
    hosts: localhost
    vars:
        car_model: "BMW M3"
        country_name: "USA"
        title: "Systems Engineer"
    tasks:
        -
            name: 'Print my car model'
            command: 'echo "My car''s model is {{ car_model }}"'
        -
            name: 'Print my country'
            command: 'echo "I live in the {{ country_name }}"'
        -
            name: 'Print my title'
            command: 'echo "I work as a {{ title }}"'
```