# logs
Создаем файл provision.yml для выполнения плэйбука при поднятии виртуалок
На виртуальной машине web устанавливаем nginx

![image](https://github.com/user-attachments/assets/097d1919-3d4f-4840-b4e5-bea164eb4e7f)

Добавляем в файл nginx.conf следующие строки для указания удаленного сервера и уровня логов, которые нужно отправлять
```
error_log syslog:server=192.168.56.15:514,tag=nginx_error;
access_log syslog:server=192.168.56.15:514,tag=nginx_access,severity=info combined;
```
Заменяем измененным файлом nginx.conf оригинальный файл на виртуальной машине и перезапускаем nginx

![image](https://github.com/user-attachments/assets/40662e23-02c3-4f18-b45f-226e32256f27)

В файле rsyslog.conf раскоментируем строки для открытия порта 514
```
# provides UDP syslog reception
module(load="imudp")
input(type="imudp" port="514")

# provides TCP syslog reception
module(load="imtcp")
input(type="imtcp" port="514")
```
Добавим следующие строки
```
$template RemoteLogs,"/var/log/rsyslog/%HOSTNAME%/%PROGRAMNAME%.log"
*.* ?RemoteLogs
& ~
```
Заменяем оригинальный файл rsyslog.conf и перезапускаем rsyslog

![image](https://github.com/user-attachments/assets/21b8098c-1fc9-4fb3-a177-9c684e649e41)

Пробуем несколько раз зайти по адресу http://192.168.56.10 и смотрим логи

![image](https://github.com/user-attachments/assets/f640959e-2f13-4e0d-811f-2b6e07cec95f)

Генерируем ошибку с помощью:
```
mv /var/www/html/index.nginx-debian.html /var/www/
```
Теперь смотрим логи ошибок

![image](https://github.com/user-attachments/assets/2375dff8-5272-45cb-b169-392534cf845b)
