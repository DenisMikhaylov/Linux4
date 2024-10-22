
# Настройка почтовой системы.

Подклюбчаемся к компьютеру Gate

Установка postfix

```
# apt install postfix -y
```
```
Internet sile
System mail name = corp.ru
```


Установка dovecot

```
apt install dovecot-imapd -y
```

Правим параметры аутификации

```
nano /etc/dovecot/conf.d/10-auth.conf
```
```
disable_plaintext_auth = no
```
Проверка 

```
dovecot -n
```

```
nano /etc/dovecot/conf.d/10-ssl.conf
```
```
ssl = no 
```
Проверка 

```
# dovecot -n

# service dovecot restart
```

Настройка Web интерфейса Roundcube

```
apt install mariadb-server -y
apt install roundcube roundcube-mysql -y
```
```
yes
пароль Pa$$w0rd
```

Настройка
```
nano /etc/roundcube/config/config.inc.php
или
nano /var/lib/roundcube/config/config.inc.php
```
```
$config['default_host'] = 'localhost';
$config['smtp_host'] = 'localhost:25';
$config['smtp_user'] = '';
$config['smtp_pass'] = '';
$rcmail_config['mail_domain'] = 'corp.ru';

```
```
nano /etc/apache2/conf-enabled/roundcube.conf
```
```
убрать комментарий перед строкой Alias
```

перезапускаем apache2

```
systemctl restart apache2
```

Настройка MTA postfix
```
root@server:~# cat /etc/postfix/main.cf
```
```
...
mydestination = gate.corp.ru, localhost.corp.ru, , localhost, corp.ru
...
root@server:~# postconf

root@server:~# service postfix check

root@server:~# service postfix reload
```
