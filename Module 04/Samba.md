Установка 

Добавить дополнителдьную виртуальную машину server 2 на linux.

Подключиться к server2

```
# apt update

# apt install samba winbind
```
```
# mv /etc/samba/smb.conf /etc/samba/smb_old.conf
```

Инициализация домена
```
samba-tool domain provision --use-rfc2307 --interactive
```
```
Realm [CORP.ru]:
 Domain [CORP]:
 Server Role (dc, member, standalone) [dc]:
 DNS backend (SAMBA_INTERNAL, BIND9_FLATFILE, BIND9_DLZ, NONE) [SAMBA_INTERNAL]:
 DNS forwarder IP address (write 'none' to disable forwarding) [8.8.8.8]:
Administrator password: Pa$$w0rd
Retype password: Pa$$w0rd
```
Настройка Kerberos клиента
```
apt install krb5-user
```

```
nano /etc/krb5.conf
```
```
[libdefaults]
    default_realm = CORP.RU
```

```
cp /var/lib/samba/private/krb5.conf /etc/krb5.conf
```
```
testparm
```
```
nano /etc/samba/smb.conf
```
```
[global]
        ldap server require strong auth = no
```

Настройка контроллером существующего домена

```
# kinit administrator
```
```
# samba-tool domain join corp.ru DC -k yes --dns-backend=SAMBA_INTERNAL --option="dns forwarder=8.8.8.8"
```

Запуск репликации
```
# samba-tool drs showrepl
```
```
server2# samba-tool user list
```
```
server2# samba-tool ldapcmp ldap://dc.corp.ru ldap://server.corp.ru -Uadministrator
```
Добавление пользователя
```
samba-tool user create user3 'Pa$$w0rd4' --given-name 'Сергей' --initials 'М' --surname 'Сергеевич' #--mail-address=user3@corp.ru
```
```
samba-tool user list
```

GPO
```
find /var/lib/samba/sysvol
```
Требуется в ручную копирование с MS AD
```
robocopy \\dc\SYSVOL\corp.ru\ \\SERVER2\SYSVOL\corp.ru\ /mir /sec
```
Замена MS на Samba4

```
server# samba-tool fsmo show
```


Проверка на DC
```
PS C:\Users\Administrator> ntdsutil
```
```
roles
connections
connect to server server2
q
transfer naming master
transfer infrastructure master
transfer rid master
transfer schema master
transfer pdc
q
q
```

```
server2# samba-tool fsmo seize --role=forestdns

server2# samba-tool fsmo seize --role=domaindns

server2# samba-tool fsmo show | grep SERVER
```

