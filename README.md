Задание по Systemd

1. Было выполнено создание сервиса и таймера для него:
   Предварительно я скопировал в /var/log/watchlog.log какой-то логфайл и добавил в него контрольное слово ALERT.
   Далее, сделал необходимые файлы:
   nano /etc/sysconfig/watchlog              - создан файл конфигурации
   nano /opt/watchlog.sh                     - создан сам скрипт для сервиса
   chmod +x /opt/watchlog.sh                 - скрипту даны права на выполнение
   nano /etc/systemd/system/watchlog.service - создан сервис
   nano /etc/systemd/system/watchlog.timer   - создан таймер для сервиса
   systemctl start watchlog.service
   systemctl start watchlog.timer
   
  В логе были записи о срабатывании:
	Dec 17 17:11:05 magma systemd[1]: watchlog.service: Succeeded.
	Dec 17 17:11:05 magma systemd[1]: Started My watchlog service.
	Dec 17 17:11:39 magma systemd[1]: Starting My watchlog service...
	Dec 17 17:11:39 magma root[4521]: Sun Dec 17 17:11:39 UTC 2023: word found
	Dec 17 17:11:39 magma systemd[1]: watchlog.service: Succeeded.
	Dec 17 17:11:39 magma systemd[1]: Started My watchlog service.
	Dec 17 17:12:39 magma systemd[1]: Starting My watchlog service...
	Dec 17 17:12:39 magma root[4533]: Sun Dec 17 17:12:39 UTC 2023: word found
	Dec 17 17:12:39 magma systemd[1]: watchlog.service: Succeeded.
	Dec 17 17:12:39 magma systemd[1]: Started My watchlog service.
	Dec 17 17:13:39 magma systemd[1]: Starting My watchlog service...
	Dec 17 17:13:39 magma root[4545]: Sun Dec 17 17:13:39 UTC 2023: word found
	Dec 17 17:13:39 magma systemd[1]: watchlog.service: Succeeded.
	Dec 17 17:13:39 magma systemd[1]: Started My watchlog service.

2. Запуск нескольких инстансов Апача
   Установил spawn-fcgi
   yum install epel-release -y && yum install spawn-fcgi php php-cli mod_fcgid httpd -y
   Изменил /etc/sysconfig/spawn-fcgi
   Создал юнит
   nano /etc/systemd/system/spawn-fcgi.service
   Запустил spawn-fcgi:
   systemctl start spawn-fcgi
   Т.к. не рекомендуется править напрямую файл /usr/lib/systemd/system/httpd.service, я сделал его командой
   systemctl edit httpd
   Создал 2 файла окружения - /etc/sysconfig/httpd-first и /etc/sysconfig/httpd-second
   В каталог с конфигами апача /etc/httpd/conf положил 2 файла first.conf и second.conf с необходимыми параметрами PidFile и Listen
   Запустил апачи:
   systemctl start httpd@first
   systemctl start httpd@second
   
   Проверка работы
   ss -tnulp | grep httpd

   tcp   LISTEN 0      511          0.0.0.0:80        0.0.0.0:*    users:(("httpd",pid=6269,fd=3),("httpd",pid=6268,fd=3),("httpd",pid=6267,fd=3),("httpd",pid=6265,fd=3))
   tcp   LISTEN 0      511          0.0.0.0:8080      0.0.0.0:*    users:(("httpd",pid=5671,fd=3),("httpd",pid=5670,fd=3),("httpd",pid=5669,fd=3),("httpd",pid=5667,fd=3))
 
   Оба инстанса слушают нужные порты - 80 и 8080
   Все

