# SSH cheat sheets

SSH-ключ используется, например, для аутентификации для Git-серверов. 

## Генерация открытого SSH ключа
Первый шаг настройки аутентификации SSH-ключа - его генерация. 
Сгенерированный ключ записывается в файл id_dsa.pub или id_rsa.pub. 
Файл с расширением .pub — открытый ключ, второй файл — приватный ключ.

Проверим, что ключ еще не был сгенерирован.
По умолчанию пользовательские SSH ключи хранятся в каталоге ~/.ssh домашнем каталоге пользователя:            
```
$ cd ~/.ssh
$ ls
authorized_keys2  id_dsa       known_hosts
config            id_dsa.pub
```

Если указанные файлы (или даже директория .ssh/) отсутствуют, можно приступить к их генерации при помощи утилиты ssh-keygen, 
которая входит в состав пакета SSH в системах Linux/Mac, а для Windows поставляется вместе с Git:
```
$ ssh-keygen -t rsa
Generating public/private rsa key pair.
Enter file in which to save the key (/home/user/.ssh/id_rsa):
Created directory '/home/user/.ssh'.
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /home/user/.ssh/id_rsa.
Your public key has been saved in /home/user/.ssh/id_rsa.pub.
The key fingerprint is:
d0:82:24:8e:d7:f1:bb:9b:33:53:96:93:49:da:9b:e3 user@laptop.local
```

Сначала программа попросит указать расположение файла для сохранения ключа (.ssh/id_rsa), затем дважды ввести пароль для шифрования. 
Если вы не хотите вводить пароль каждый раз при использовании ключа, то можете оставить его пустым или использовать программу ssh-agent. 
Если вы решили использовать пароль для приватного ключа, то настоятельно рекомендуется использовать опцию -o, 
которая позволяет сохранить ключ в формате, более устойчивом ко взлому методом подбора, чем стандартный формат.

Теперь каждый пользователь должен отправить свой открытый ключ (id_rsa.pub) администратору Git-сервера 
(подразумевается, что ваш SSH-сервер уже настроен на работу с открытыми ключами): 
```
$ cat ~/.ssh/id_rsa.pub
ssh-rsa AAAAB3NzaC1yc2EAAAABIwAAAQEAklOUpkDHrfHY17SbrmTIpNLTGK9Tjom/BWDSU
GPl+nafzlHDTYW7hdI4yZ5ew18JH4JW9jbhUFrviQzM7xlELEVf4h9lFX5QVkbPppSwg0cda3
Pbv7kOdJ/MTyBlWXFCR+HAo3FXRitBqxiX1nKhXpHAZsMciLq8V6RjsNAQwdsdMFvSlVK/7XA
t3FaoJoAsncM1Q9x5+3V0Ww68/eIFmb1zuUFljQJKprrX88XypNDvjYNby6vw/Pb0rwert/En
mZ+AW4OZPnTPI89ZPmVMLuayrD2cE86Z/il8b+gw3r3+1nKatmIkjn2so1d01QraTlMqVSsbx
NrRFi9wrf+M7Q== user@laptop.local
```

## Настройку SSH на сервере
Будем использовать метод authorized_keys для аутентификации пользователей.
```
$ mkdir .ssh && chmod 700 .ssh
$ touch .ssh/authorized_keys && chmod 600 .ssh/authorized_keys
```

Добавим открытые SSH-ключи в файл authorized_keys (для разых пользователей в разные файлы):         
```
$ cat /tmp/id_rsa.john.pub
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCB007n/ww+ouN4gSLKssMxXnBOvf9LGt4L
ojG6rs6hPB09j9R/T17/x4lhJA0F3FR1rP6kYBRsWj2aThGw6HXLm9/5zytK6Ztg3RPKK+4k
Yjh6541NYsnEAZuXz0jTTyAUfrtU3Z5E003C4oxOj6H0rfIF1kKI9MAQLMdpGW1GYEIgS9Ez
Sdfd8AcCIicTDWbqLAcU4UpkaX8KyGlLwsNuuGztobF8m72ALC/nLF6JLtPofwFBlgc+myiv
O7TCUSBdLQlgMVOFq1I2uPWQOkOWQAHukEOmfjy2jctxSDBQ220ymjaNsHT4kgtZg2AYYgPq
dAv8JggJICUvax2T9va5 gsg-keypair
$ cat /tmp/id_rsa.john.pub >> ~/.ssh/authorized_keys
```

## Использование приватного ключа
Пусть у нас есть сгенерированный ключ ```mykey.pem```.         
1. Скачиваем ключ в необходимую директорию и настраиваем права: ```chmod 0600 mykey.pem```
2. Добавляем ключ в директорию `~/.ssh`: ```cp mykey.pem ~/.ssh/```
3. Создадим config-файл и опишем в нём подключение:
   ```
   $ touch ~/.ssh/config
   Host <HostName>
   HostName host.adress.com
   User name.surname
   Port 22
   IdentityFile {путь_до_директории_/.ssh}/mykey.pem
   
   # Поддерживать соединение включённым при помощи своего клиента для передачи нуль-пакетов на сервер.
   # Клиент будет поддерживать соединение, раз в минуту передавая пустой запрос на сервер.
   ServerAliveInterval 60
   ```
4. Теперь можно подключаться просто используя созданный идентификатор без указания имени пользователя, хостнейма и сертификата: ```ssh <HostName>```.
