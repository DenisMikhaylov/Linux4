
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
Управление ключами KERBEROS в режиме ADS

```
gate# klist -ek /etc/krb5.keytab
```
```
gate# kinit Administrator
```
```
samba4.9+# net ads keytab add_update_ads HTTP -k

samba4.9+# net ads keytab add_update_ads imap -k

samba4.9+# net ads keytab add_update_ads smtp -k
   
samba4.9+# net ads keytab add_update_ads xmpp -k

gate# klist -ek /etc/krb5.keytab

gate# net ads setspn list gate
```
Kerberos GSSAPI аутентификация

```
apt install dovecot-gssapi
```
Добавляем пользователя в AD

```
Login: gatesmtp
Login: gateimap
Password: Pa$$w0rd
Пароль не меняется и не устаревает
```

Создаем ключ сервиса http 
```
C:\>ktpass -princ imap/gate.corp.ru@CORP.RU -mapuser gateimap -pass 'Pa$$w0rd' -out gateimap.keytab

C:\>ktpass -princ smtp/gate.corp.ru@CORP.RU -mapuser gatesmtp -pass 'Pa$$w0rd' -out gatesmtp.keytab
```
Копируем ключ сервиса http сервер dovecot
```
C:\>pscp gateimap.keytab root@gate:

C:\>pscp gatesmtp.keytab root@gate:
```

Копируем ключи в системный keytab

```
root@gate:~# ktutil
```
```
ktutil:  rkt /root/gateimap.keytab
ktutil:  rkt /root/gatesmtp.keytab
ktutil:  wkt /etc/krb5.keytab
ktutil:  quit
```
```
root@gate:~# klist -k /etc/krb5.keytab
```

Настройка dovecot на использование GSSAPI
```
# nano /etc/dovecot/conf.d/10-auth.conf
```
```
...
auth_gssapi_hostname = "$ALL"
...
auth_mechanisms = gssapi plain
...
```
```
# chmod +r /etc/krb5.keytab
```
```
gate# mail user1
```
