GITLAB

Установка из репозитория
```
# apt install -y curl ca-certificates perl

# curl https://packages.gitlab.com/install/repositories/gitlab/gitlab-ce/script.deb.sh | bash

# EXTERNAL_URL="http://$(hostname)" apt-get install gitlab-ce
```


Установка и настройка OpenLDAP
```
apt install slapd ldap-utils
```

Настройка
```
nano /etc/gitlab/gitlab.rb
```
```
...
gitlab_rails['ldap_enabled'] = true

gitlab_rails['ldap_servers'] = YAML.load <<-'EOS'
  main:
    label: 'LDAP'
    host: 'dc.corp.ru'
    port: 389
    uid: 'sAMAccountName'
    bind_dn: 'cn=Administrator,cn=Users,dc=corpX,dc=un'
    password: 'Pa$$w0rd'
    encryption: 'plain'
    active_directory: true
    base: 'dc=corp,dc=ru'
EOS
...
```
