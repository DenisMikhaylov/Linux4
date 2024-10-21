Авторизация в режиме ADS/DOMAIN
```
gate# wbinfo -n user1     # может не работать на этом этапе
```
```
gate# nano /etc/samba/smb.conf
```
```
[global]
        ...
        winbind use default domain = Yes

        winbind expand groups = 1
        winbind enum users = yes
        winbind enum groups = yes
        winbind cache time = 36
        idmap config * : range = 20000-40000
        template homedir = /home/%U
        #use suitable shell (what abount /usr/sbin/nologin ?)
        template shell = /bin/sh
```
```
gate# service winbind restart
```

Авторизация на основе имени пользователя
```
gate# nano /etc/squid/conf.d/my.conf
```
```
#acl inetuser proxy_auth REQUIRED
acl inetuser proxy_auth user1@CORP.RU user2@CORP.RU
#acl inetuser proxy_auth_regex "/etc/squid/group1.acl"

http_access allow inetuser
```
Авторизация на основе членства в группе
```
gate# getent group group1 | cut -f4 -d: | tr "," "\n" | tee /etc/squid/group1.acl

gate# squid -k reconfigure
```
Для winbind авторизации
```
gate# ntlm_auth --username=user1 --require-membership-of=CORP\\group1
```
