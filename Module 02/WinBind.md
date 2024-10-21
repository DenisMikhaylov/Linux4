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

Установка WinBind

```
gate:~# apt install winbind
```
