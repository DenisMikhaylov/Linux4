Сервис sssd


Подключиться к lclient

Установка sssd

```
lcliant# apt install packagekit sssd-ad sssd-tools realmd adcli auditd
```
Просмотр настроек
```
lcliant# less /etc/nsswitch.conf
```

Присоединение к домену MS AD
```
lcliant# realm -v discover corp.ru
```
```
lcliant# realm -v join corp.ru
```
Просмотр настроек
```
lcliant# realm list

lcliant# less /etc/krb5.keytab

lcliant# getent passwd user1@corp.ru

lcliant# id user1@corp.ru
```
```
lcliant# nano /etc/sssd/sssd.conf
```
```
[sssd]
default_domain_suffix = corp.ru
full_name_format = %1$s
...
[domain/corp.ru]
...
fallback_homedir = /home/%u
...

```
```
lcliant# service sssd restart
```
```
cliant# getent passwd user1
```
