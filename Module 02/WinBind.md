Ввод Linux систем в домен с использование Winbind

Задача 1: Очистка настроек 

Удаляем пользователей gate* из AD
Удаляем файлы с ключами на AD
```
c:\del *keytab
```
Удаляем ключи сервисов с gate
```
gate# rm -v /root/*keytab /etc/krb5.keytab
```
Удалить запись gate из dns

Задача 2: Установка службы winbindd

Подключить к серверу Gate

Установка Samba

```
gate:~# apt install samba
```
Установка WinBind

```
gate:~# apt install winbind
```

Регистрация unix системы в домене в режиме ADS

```
gate# nano /etc/samba/smb.conf
```
```
[global]
        workgroup = CORP
        security = ADS
        realm = CORP.RU
        kerberos method = system keytab
        winbind use default domain = Yes
```
Добавление gate в домен

```
gate# net ads join -U Administrator
```

```
gate# wbinfo -t
gate# wbinfo -u
gate# wbinfo -g
```
Проверяем Gate в DNS

Управление ключами KERBEROS в режиме ADS

```
# net ads setspn add HTTP/gate.corp.ru -U Administrator

# net ads keytab create
```

На контроллере домена

Проверка:
```
C:\>setspn -L gate
```
