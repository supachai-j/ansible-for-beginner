# Deploy Project Demo

Project ที่จะใช้ deploy บน LAMP
```
https://github.com/supachai-j/learning-app-ecommerce.git
```

1. ลองทำการ Deploy ผ่าน vagrant + shellscript โดยใช้คำสั่ง 
```
cd vagrant-lamp
vagrant up
```

2. ในไฟล์​ vagrantfile จะมีการอ้างอิง shellscript ในการ project-deploy.sh อยู่ ถ้าต้องการ deploy แบบ manual เอง ให้ทำการ comment บรรทัดนี้
```
# config.vm.provision :shell, path: "project-deploy.sh"
```
จากนั้นทำการ ลบ vm และสร้างใหม่
```
vagrant destroy -f

vagrant up
```

