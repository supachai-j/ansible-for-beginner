# Setup VMs and Install Ansible

ทำการสร้าง Virtual Machine จำนวน 3 เครื่อง โดยที่เป็น
- ansible-master x 1 node
- ansible-target  x 2 nodes

เข้าไปที่โฟเดอร์ 1-Setup-VMs-and-Install-Ansible/vagrant-centos7 จากนั้นก็ 
```
$ vagrant up

$ vagrant status
Current machine states:

master                    running (virtualbox)
target1                   running (virtualbox)
target2                   running (virtualbox)

This environment represents multiple VMs. The VMs are all listed
above with their current state. For more information about a specific
VM, run `vagrant status NAME`.
```

ทำการ Change ssh configuration แป๊บ
```
vagrant ssh target1 -c "sudo sed -ri 's/^PermitRootLogin\s+.*/PermitRootLogin yes/' /etc/ssh/sshd_config && sudo sed -ri 's/^PasswordAuthentication no\s+.*/PasswordAuthentication yes/' /etc/ssh/sshd_config && sudo systemctl restart sshd"

vagrant ssh target2 -c "sudo sed -ri 's/^PermitRootLogin\s+.*/PermitRootLogin yes/' /etc/ssh/sshd_config && sudo sed -ri 's/^PasswordAuthentication no\s+.*/PasswordAuthentication yes/' /etc/ssh/sshd_config && sudo systemctl restart sshd"
```


ติดตั้ง Ansible บน master โหนด 
```
$vagrant ssh master

[vagrant@master ~]$sudo yum install epel-release -y
[vagrant@master ~]$sudo yum install ansible -y
[vagrant@master ~]$ ansible --version
ansible 2.9.15
  config file = /etc/ansible/ansible.cfg
  configured module search path = [u'/home/vagrant/.ansible/plugins/modules', u'/usr/share/ansible/plugins/modules']
  ansible python module location = /usr/lib/python2.7/site-packages/ansible
  executable location = /usr/bin/ansible
  python version = 2.7.5 (default, Apr  2 2020, 13:16:51) [GCC 4.8.5 20150623 (Red Hat 4.8.5-39)]
[vagrant@master ~]$
```
[วิธีการติดตั้ง ansible บน CentOS](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html#installing-ansible-on-rhel-centos-or-fedora)

## Demo Ansible Inventory
เข้าไป ssh ไปที่ master 
```
$ vagrant ssh master

[vagrant@master ~]$mkdir test-project
[vagrant@master ~$cd test-project
[vagrant@master test-project]$cat > inventory.txt
target1 ansible_host=192.168.55.21 ansible_user=root ansible_ssh_pass=ansible

[vagrant@master test-project]$ ansible target1 -m ping -i inventory.txt 
target1 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python"
    }, 
    "changed": false, 
    "ping": "pong"
}
[vagrant@master test-project]$
```

ถ้าเจอ error แบบนี้ 
```
[vagrant@master test-project]$ ansible target1 -m ping -i inventory.txt 
target1 | FAILED! => {
    "msg": "Using a SSH password instead of a key is not possible because Host Key checking is enabled and sshpass does not support this.  Please add this host's fingerprint to your known_hosts file to manage this host."
}
[vagrant@master test-project]$
```

ให้ทำการเพิ่มไฟล์​ ansible.cfg ที่ home directory ของ user ที่ใช้งานก็ได้ครับ
```
[vagrant@master test-project]$ cat ~/.ansible.cfg 
[defaults]
host_key_checking = False
[vagrant@master test-project]$ 
```
หรือ ไปกำหนดที่  /etc/ansible/ansible.cfg ก็ได้เช่นกันครับ


เจอ error แบบนี้ 
```
[vagrant@master test-project]$ ansible target1 -m ping -i inventory.txt 
'target1 | UNREACHABLE! => {
    "changed": false, 
    "msg": "Invalid/incorrect password: Permission denied, please try again.", 
    "unreachable": true
}
[vagrant@master test-project]$
```
ทำการเพิ่ม ansible_ssh_user บน inventory ไฟล์
```
[vagrant@master test-project]$ cat inventory.txt 
target1 ansible_host=192.168.55.21 ansible_user=root ansible_ssh_pass=ansible
target2 ansible_host=192.168.55.22 ansible_user=root ansible_ssh_pass=ansible
[vagrant@master test-project]$
```

ลองทำการใช้ module ping อีกที
```
[vagrant@master test-project]$ ansible target1 -m ping -i inventory.txt 
target1 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python"
    }, 
    "changed": false, 
    "ping": "pong"
}
[vagrant@master test-project]$ ansible target2 -m ping -i inventory.txt 
target2 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python"
    }, 
    "changed": false, 
    "ping": "pong"
}
[vagrant@master test-project]$ 
```

### Lab Ansible Inventory 
```
# Sample Inventory File

# Web Servers
web1 ansible_host=server1.company.com ansible_connection=ssh ansible_user=root ansible_ssh_pass=Password123!
web2 ansible_host=server2.company.com ansible_connection=ssh ansible_user=root ansible_ssh_pass=Password123!
web3 ansible_host=server3.company.com ansible_connection=ssh ansible_user=root ansible_ssh_pass=Password123!

# Database Servers
db1 ansible_host=server4.company.com ansible_connection=winrm ansible_user=administrator ansible_password=Password123!


# Group of Servers
[web_servers]
web1
web2
web3

[db_servers]
db1

# Group of Group 
[all_servers:children]
web_servers
db_servers

```

```
# Inventory

# Linux Servers
sql_db1 ansible_host=sql01.xyz.com ansible_connection=ssh ansible_user=root ansible_ssh_pass=Lin$Pass
sql_db2 ansible_host=sql02.xyz.com ansible_connection=ssh ansible_user=root ansible_ssh_pass=Lin$Pass

# Windows Servers
web_node1 ansible_host=web01.xyz.com ansible_connection=winrm ansible_user=administrator ansible_password=Win$Pass
web_node2 ansible_host=web02.xyz.com ansible_connection=winrm ansible_user=administrator ansible_password=Win$Pass
web_node3 ansible_host=web03.xyz.com ansible_connection=winrm ansible_user=administrator ansible_password=Win$Pass

# Group of Servers
[db_nodes]
sql_db1
sql_db2

[web_nodes]
web_node1
web_node2
web_node3

[boston_nodes]
sql_db1
web_node1

[dallas_nodes]
sql_db2
web_node2
web_node3

[us_nodes:children]
boston_nodes
dallas_nodes

```