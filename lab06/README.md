### Лабораторная работа №6. Балансирование нагрузки в облаке и авто-масштабирование

_______________________

##### недавно удалось восстановить доступ к AWS аккаунта

_______________________

### Цели

### [LINK](https://github.com/MSU-Courses/cloud-computing/tree/main/_lab/06_Cloud_Load_Balancer_And_AutoScalig)

_______________________

### Шаг 1. Создание VPC и подсетей

![](https://i.imgur.com/iC1WS0f.png)

#### -- подсети готовы --

![](https://i.imgur.com/3oUbId3.png)


#### -- маршруты к публичным подсетям --

![](https://i.imgur.com/sYBC3lY.png)

_______________________

### Шаг 2. Создание и настройка виртуальной машины

далее в созданой VPC необходимо запустить и создать ВМ 

#### -- базовая настройка --

###### ОС + AMI

![](https://i.imgur.com/bK3zxH7.png)


###### VPC + rules

![](https://i.imgur.com/bjyruQk.png)

###### keypair

![](https://i.imgur.com/guy4Q5I.png)

###### сборка доп метрик

![](https://i.imgur.com/a0Z7X5k.png)

###### скрипт `User Data`

```bash
#!/bin/bash

yum update -y
amazon-linux-extras install nginx1 -y
systemctl enable nginx
systemctl start nginx
echo "<h2>GitHub page not found</h2>" > /usr/share/nginx/html/index.html
```

![](https://i.imgur.com/D5jvwYY.png)

![](https://i.imgur.com/dr0ctDL.png)


![](https://i.imgur.com/V1xojjU.png)


###### в последствии мне пришлось пересоздать инстанс, тк 7не заметив, я его создал в приватнолй под сети

![](https://i.imgur.com/66dMYKp.png) 


**скрипт в целях работы не был найцден**

_______________________

### Шаг 3. Создание AMI

![](https://i.imgur.com/XGAzMr5.png)

> Что такое image и чем он отличается от snapshot? Какие есть варианты использования AMI?

AMI — это шаблон виртуальной машины в AWS, который содержит операционную систему, настройки, установленное ПО и данные, необходимые для запуска EC2-инстанса. Его используют для создания новых инстансов с заранее настроенной конфигурацией. Snapshot — это копия диска (EBS volume) в конкретный момент времени, которая нужна для резервного копирования и восстановления данных. Основное отличие в том, что AMI предназначена для запуска инстансов, а snapshot хранит только данные диска и напрямую не запускается как инстанс.
