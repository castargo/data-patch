# SSH cheat sheets

SSH (Secure SHell) - сетевой протокол прикладного уровня, позволяющий производить удалённое управление операционной системой и туннелирование TCP-соединений (например, для передачи файлов и других сетевых протоколов). Шифрует весь трафик при помощи различных алгоритмов шифрования (также шифрует и сами пароли). Подтверждает, что удаленный узел является именно тем, за кого себя выдает (чем предотвращает атаку man-in-the-middle).

Алгоритм работы протокла SSH:
1. Установка TCP-соединения
    * Cетевое подключение клиента к серверу на TCP-порт, указанный в опции Port в файле конфигурации сервера /etc/ssh/sshd_config (по умолчанию 22);
2. Открытие защищенного канала
    * Клиент и сервер обмениваются версиями SSH-протокола (шифрование (RSA, DSA, при помощи пароля, прочее), обмен ключами, сжатие данных и т. д.) и выбирают совпадающие алгоритмы;
    * При помощи алгоритма Диффи — Хеллмана (DH) создается сессионный (сеансовый) ключ шифрования: сервер от клиента клиент свой ключ (DSA, RSA или т.п.), клиент сравнивает присланный ключ с ключом, записанным в /home/username/.ssh/known_hosts, генерируется сеансовый ключ для симметричного шифрования канала.
3. Аутентификация клиента (по паролю или ключам)
    *  При аутентификации по ключевой паре предварительно генерируется пара открытого и закрытого ключей для определённого пользователя. На машине, с которой требуется произвести подключение, хранится закрытый ключ, а на удалённой машине — открытый.
    * При подключении клиент отсылает серверу имя пользователя и свой публичный ключ. Сервер проверяет в файле /home/username/.ssh/authorized_keys наличие присланного клиентом открытого ключа, если ключ найден, то делается проверка зашифрования/расшифрования некоторого сообщения. Если проверка пройдена, то происходит аутентификация.
4. Уровень подключения
    * Пользователь получает возможность передавать команды серверу или копировать файлы.

OpenSSH - реализация протокола SSH, которая содержит только клиент.
Чтобы система могла также принимать удаленные соединения, должна быть установлена и настроена утилита OpenSSH-server.

Ссылки:
1. [SSH - Wiki](https://ru.wikipedia.org/wiki/SSH)
2. [Алгоритм установления соединения в протоколе SSH](https://habr.com/ru/articles/425637/)

## Генерация открытого SSH ключа
[How to use ssh-keygen to generate a new SSH key](https://www.ssh.com/academy/ssh/keygen)

SSH-ключ используется, например, для аутентификации для Git-серверов. 

Первый шаг настройки аутентификации SSH-ключа - его генерация. 
Сгенерированный ключ записывается в файл id_dsa.pub или id_rsa.pub.

Проверим, что ключ еще не был сгенерирован.
По умолчанию пользовательские SSH ключи хранятся в каталоге ~/.ssh домашнем каталоге пользователя:            
```
$ cd ~/.ssh
$ ls
authorized_keys2  id_dsa       known_hosts
config            id_dsa.pub
```

Файлы в директории .ssh/:
* ```id_dsa``` - приватный ключ
* ```id_dsa.pub``` - открытый ключ
* ```config``` - конфиг с хостами
* ```known_hosts``` - конфиг с кючами удаленных хостов

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

## Команды OpenSSH
* ```ssh <remote>``` - подключение к удаленному узлу по его имени
* ```exit``` - разрыв соединения
* ```scp``` - secure copy
  * ```scp <remote>:<path/to/file> <path/to/path>```
* ```sftp``` - safe ftp (File Transfer Protocol)
  * ```sftp <remote>```

## Аналог SSH для Windows
PuTTY
