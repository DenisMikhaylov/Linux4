Ansible

Установка на gate
```
# apt install ansible
```
Настройка
```
# mkdir ansible-pull-gpo

# cd ansible-pull-gpo/
```
Директории надо создать предварительно
```
nano kerberos/files/etc/krb5.conf
```

```
[libdefaults]
  default_realm = CORP.RU
```
```
nano kerberos/files/etc/pam.d/common-auth
```
```
auth    [success=2 default=ignore]      pam_krb5.so minimum_uid=1000
auth    [success=2 default=ignore]      pam_unix.so nullok_secure try_first_pass
auth    requisite                       pam_deny.so
auth    sufficient                      pam_script.so
auth    required                        pam_permit.so
```

```
nano kerberos/files/usr/share/libpam-script/pam_script_auth
```
```
#!/bin/bash

id "$PAM_USER" &>/dev/null || useradd -m -s /bin/bash "$PAM_USER"
```
```
nano kerberos/tasks/main.yml
```
```
- name: Install krb5-user libpam-krb5 libpam-script
  apt:
    pkg:
      - krb5-user
      - libpam-krb5
      - libpam-script
    state: present
    update_cache: true

- name: Copy krb5.conf common-auth
  copy:
    src: '{{item.0}}'
    dest: '{{item.1}}'
  loop:
    - [ 'etc/krb5.conf', '/etc/' ]
    - [ 'etc/pam.d/common-auth', '/etc/pam.d/' ]

- name: Copy pam_script_auth
  copy:
    src: usr/share/libpam-script/pam_script_auth
    dest: /usr/share/libpam-script/
    mode: '0755'
```

```
nano proxy/files/etc/environment
```
```
https_proxy=http://gate.corp.ru:3128
no_proxy=localhost,127.0.0.1,corp.ru
```

```
nano proxy/tasks/main.yml
```

```
- name: Copy environment
  copy:
    src: etc/environment
    dest: /etc/environment
```

Создание play book
```
nano local.yml
```
```
- hosts: localhost
  roles:
    - role: kerberos
    - role: proxy
```

Запуск

```
ansible-playbook local.yml
```
```
reboot
```

Разместим все в  GitLab { Если есть}
```
# apt install git
# cd ansible-pull-gpo/
```
```
# git config --global user.email "student@corp.ru"

# git init --initial-branch=main

# git remote add origin http://gate.corp.ru/student/ansible-pull-gpo.git

# git add .

# git commit -m "Initial commit"

# git  push -u origin main
```

На клиенте
```
apt install git ansible
```
```
ansible-pull -U http://gate.corp.ru/student/ansible-pull-gpo.git
```
```
reboot
```
