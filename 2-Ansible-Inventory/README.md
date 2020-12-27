# Ansible Inventory
inventory ในมุมมองของ Ansible คือ เครื่อง Target ต่างๆ ที่เราต้องการใช้คำสั่ง เพื่อสั่งงานให้ทำตามสิ่งที่เราต้องการ
โดย Ansible จะทำเหมือนกัน System Admin ในการเข้าไปสั่งงาน เช่น ถ้าเครื่องที่เป็น Linux ก็เข้าไปด้วย ssh และเครื่องที่เป็น Windows ก็ใช้ Powershell Remoting เป็นต้น
ลักษณะการทำงานแบบนี้ Ansible เป็น Agentless ครับ คือการที่ไม่มีการติดตั้ง Sotfware Agent ลงไปบนเครื่อง Target นั้นเอง แต่จะใช้วิธีการเหมือนกับ System Admin เนี้ยแหละในการเข้าไปสั่งงานต่างๆ 

inventory จะถูกกำหนดในรูปแบบของไฟล์ โดยปกติจะอยู่ที่ /etc/ansible/hosts
```
server1.example.local
server2.example.local

[mail]
server3.exmaple.local
server4.example.local

[db]
server5.example.local
server6.example.local

[web]
server7.example.local
server8.example.local
```

ในการกำหนดไฟล์ Inventory ยังอาจจะต้องกำหนดค่าต่างๆ เพิ่มเติมเช่น
```
web ansible_host=server1.example.local  ansible_connection=ssh  ansible_user=root
db ansible_host=server2.example.local  ansible_conntection=winrm  ansible_user=admin
mail ansible_host=server3.example.local  ansible_connection=ssh  ansible_ssh_pass=P@$2020
web2 ansible_host=server4.example.local ansible_connection=winrm 

localhost ansible_connection=localhost
```
หรือ inventory parameters อื่นๆ 
- ansible_connection - ssh/winrm/localhost
- ansible_port - 22/5986
- ansible_user - root/administrator
- ansible_ssh_pass - Password

ในส่วนของไฟล์​ Inventory ที่ต้องเก็บรหัสผ่านแบบนี้ ในมุมของการทำ Security เราสามารถใช้สิ่งที่เรียกว่า Ansible Vault ได้

