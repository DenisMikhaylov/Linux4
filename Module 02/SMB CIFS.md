Установка SAMBA

```
# apt install samba

# mkdir -p /disk2/samba && chown games /disk2/samba
```

Настройка samba сервера в режиме DOMAIN/ADS c WINBIND

```
gate# nano /etc/samba/smb.conf
```
```
[global]

...

[homes]
; may be need make homedir
        read only = no    
        valid users = %S    

[corp_share]
        path = /disk2/samba

;with winbind
;        valid users = CORP\user1 CORP\Administrator CORP\root
;        valid users = @CORP\group1
;        valid users = "@CORP\domain users"

;without winbind, group1 must be master group
;        valid users = @group1 games

;without winbind      
;        valid users = user1 user2 games
        
        read only = no
        force user = games
```

Автоматическое создание домашних каталогов

Установка PAM библиотек
```
root@gate:~# apt install libpam-modules
root@gate:~# pam-auth-update --enable mkhomedir
```
Посмотреть настройки
```
root@gate:~# less /etc/pam.d/common-session
```
```
session    required     pam_mkhomedir.so
```
```
# nano /etc/pam.d/samba
```
```
...
@include common-session-noninteractive
session optional pam_mkhomedir.so
```
```
gate# nano /etc/samba/smb.conf
```
```
[global]
...
        obey pam restrictions = yes
```
