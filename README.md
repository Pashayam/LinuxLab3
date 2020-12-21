# Работа с пользователями


### Цель:
Работа состоит из 2х пунктов. `часть отмеченная "*" не обязательна к выполнению.`
1. Первая часть:
* Создать нескольких пользователей, задать им пароли, домашние директории и шеллы;* Создать группу admin;
* Включить нескольких из ранее созданных пользователей, а также пользователя root, в группу admin;
* Запретить всем пользователям, кроме группы admin, логин в систему по SSH в выходные дни (суббота и воскресенье, без учета праздников).
* \* С учётом праздничных дней.
(Для упрощения проверки можно разрешить парольную аутентификацию по SSH и использовать ssh user@localhost проверяя логин с этой же машины)

2. Вторая часть:
* Установить docker;
* Дать конкретному пользователю:
* права работать с docker (выполнять команды docker ps и т.п.);
* \* возможность перезапускать демон docker (systemctl restart docker) не выдавая прав более, чем для этого нужно;


### Часть 1
Создадим трех пользователей с указанием домашней директории и путей для bash. 
Для этого выполним команды 
```
sudo useradd -d /home/us1 -s /bin/bash us1
sudo useradd -d /home/us2 -s /bin/bash us2
sudo useradd -d /home/us3 -s /bin/bash us3
```

Результат выполнения команд:

![](https://github.com/Pashayam/LinuxLab3/blob/main/images/1.png)


Добавляем пользователям паролои командой `sudo passwd <имя пользователя>`.
Результат выполнения команд:

![](https://github.com/Pashayam/LinuxLab3/blob/main/images/2.png)


Добавление группы admin.

Добавления позьлователей us1, us2 и root в группу admin следующими командами:
```
sudo usermod -aG admin us1
sudo usermod -aG admin us3
sudo usermod -aG admin root
```
Чтобы удостовериться в этом введем команды `id <имя пользователя>`.

![](https://github.com/Pashayam/LinuxLab3/blob/main/images/3.png)


Реализация запрета всем пользователям, кроме группы admin, логин в систему по SSH в выходные дни 

Устанавливаем pam_script `sudo apt install libpam-script`.

Создаем файл /usr/share/libpam-script/pam_script_acct, и пишем в нем код:

![](https://github.com/Pashayam/LinuxLab3/blob/main/images/5.png)

Далее выполняем команду `sudo chmod +x /usr/share/libpam-script/pam_script_acct`
![](https://github.com/Pashayam/LinuxLab3/blob/main/images/4.png)

Далее необходимо записать строку "account required pam_script.so" в файл `/etc/pam.d/sshd`


### Проверка что все работает верно:

Попытка войти в понедельник под разными пользователями:
![](https://github.com/Pashayam/LinuxLab3/blob/main/images/6.png)
![](https://github.com/Pashayam/LinuxLab3/blob/main/images/7.png)
![](https://github.com/Pashayam/LinuxLab3/blob/main/images/8.png)

Попытка войти в воскресенье под разными пользователями:
![](https://github.com/Pashayam/LinuxLab3/blob/main/images/9.png)
![](https://github.com/Pashayam/LinuxLab3/blob/main/images/10.png)
![](https://github.com/Pashayam/LinuxLab3/blob/main/images/11.png)


### Часть 2

Для установки докера были выполнены команды:
```
sudo apt update && sudo apt upgrade
sudo apt install apt-transport-https ca-certificates curl software-properties-common
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu bionic stable"
sudo apt update && apt-cache policy docker-ce
sudo apt install -y docker-ce
```
Выдача прав пользователю pavel производилась командой: 
```
sudo usermod -aG docker pavel 
```
Чтобы пользовать мог пользоваться основными командами docker'a необходимо установить пакет docker compose, для этого необходимо выполнить следующие команды:

```
sudo curl -L "https://github.com/docker/compose/releases/download/1.25.0/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
```

Попробуем выполнить команды работы с docker'ом:
![](https://github.com/Pashayam/LinuxLab3/blob/main/images/14.png)

Для того, чтобы пользователь мог пользоваться docker необходимо его дабавить в группу docker.
В файле /etc/groups можно посмотерть у каких пользователей есть права на использование docker.
![](https://github.com/Pashayam/LinuxLab3/blob/main/images/12.png)

Попробуем воспользоваться docker от имени позльователя us1
![](https://github.com/Pashayam/LinuxLab3/blob/main/images/15.png)
