Авторизация с использованием LDAP сервера

Подключаемся к gate

Установка LDAP клиента
```
root@gate:~# apt install ldap-utils
```

Тестирование доступности каталога с клиентов
```
gate# ldapsearch -x -D "cn=Administrator,cn=Users,dc=corp,dc=ru" -W -H ldap://dc -b "dc=corp,dc=ru" "sAMAccountName=user1"

или через ldaps:

gate# LDAPTLS_REQCERT=never ldapsearch -x -D "cn=Administrator,cn=Users,dc=corp,dc=ru" -w 'Pa$$w0rd' -H ldaps://dc.corp.ru -b "dc=corp,dc=ru" "sAMAccountName=user1"

или с Kerberos GSSAPI аутентификацией

gate# apt install libsasl2-modules-gssapi-mit

gate# kinit Administrator

gate# ldapsearch -h dc -b "dc=corp,dc=ru" "sAMAccountName=user1"
