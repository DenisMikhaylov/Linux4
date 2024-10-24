Установка

```
server2:~# apt install krb5-kdc krb5-admin-server
```

Настройка
```
nano /etc/krb5.conf
```
```
[libdefaults]
    default_realm = CORP.RU
```

```
krb5_newrealm
```

```
# ls -l /var/lib/krb5kdc/
```

Перезапуск

```
systemctl restart krb5-kdc
```

Регистрация пользователей в  kerberos

```
# kadmin.local
kadmin.local:  addprinc user4
...
Enter password for principal "user4@CORP.RU": Pa$$w0rd
Re-enter password for principal "user4@CORP.RU": Pa$$w0rd
...
kadmin.local:  listprincs
...
kadmin.local: quit
```

Проверки
```
# kinit user4
# klist
```

