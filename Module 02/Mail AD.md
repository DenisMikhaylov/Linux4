
# Настройка почтовой системы.

Подклюбчаемся к компьютеру Gate

Установка postfix

```
# apt install postfix -y
```
```
Internet sile
System mail name = corp.ru
```


Установка dovecot

```
apt install dovecot-imapd -y
```

Правим параметры аутификации

```
nano /etc/dovecot/conf.d/10-auth.conf
```
```
disable_plaintext_auth = no
```
Проверка 

```
dovecot -n
```

```
nano /etc/dovecot/conf.d/10-ssl.conf
```
```
ssl = no 
```
Проверка 

```
# dovecot -n

# service dovecot restart
```

Настройка Web интерфейса Roundcube

```
apt install mariadb-server -y
apt install roundcube roundcube-mysql -y
```
```
yes
пароль Pa$$w0rd
```

Настройка
```
nano /etc/roundcube/config/config.inc.php
или
nano /var/lib/roundcube/config/config.inc.php
```
```
$config['default_host'] = 'localhost';
$config['smtp_host'] = 'localhost:25';
$config['smtp_user'] = '';
$config['smtp_pass'] = '';
$rcmail_config['mail_domain'] = 'corp.ru';

```
```
nano /etc/apache2/conf-enabled/roundcube.conf
```
```
убрать комментарий перед строкой Alias
```

перезапускаем apache2

```
systemctl restart apache2
```

Настройка MTA postfix
```
root@server:~# cat /etc/postfix/main.cf
```
```
...
mydestination = gate.corp.ru, localhost.corp.ru, , localhost, corp.ru
...
root@server:~# postconf

root@server:~# service postfix check

root@server:~# service postfix reload
```
Управление ключами KERBEROS в режиме ADS

```
gate# klist -ek /etc/krb5.keytab
```
```
gate# kinit Administrator
```
```
samba4.9+# net ads keytab add_update_ads HTTP -k

samba4.9+# net ads keytab add_update_ads imap -k

samba4.9+# net ads keytab add_update_ads smtp -k
   
samba4.9+# net ads keytab add_update_ads xmpp -k

gate# klist -ek /etc/krb5.keytab

gate# net ads setspn list gate
```
Kerberos GSSAPI аутентификация

```
apt install dovecot-gssapi
```
Добавляем пользователя в AD

```
Login: gatesmtp
Login: gateimap
Password: Pa$$w0rd
Пароль не меняется и не устаревает
```

Создаем ключ сервиса http 
```
C:\>ktpass -princ imap/gate.corp.ru@CORP.RU -mapuser gateimap -pass 'Pa$$w0rd' -out gateimap.keytab

C:\>ktpass -princ smtp/gate.corp.ru@CORP.RU -mapuser gatesmtp -pass 'Pa$$w0rd' -out gatesmtp.keytab
```
Копируем ключ сервиса http сервер dovecot
```
C:\>pscp gateimap.keytab root@gate:

C:\>pscp gatesmtp.keytab root@gate:
```

Копируем ключи в системный keytab

```
root@gate:~# ktutil
```
```
ktutil:  rkt /root/gateimap.keytab
ktutil:  rkt /root/gatesmtp.keytab
ktutil:  wkt /etc/krb5.keytab
ktutil:  quit
```
```
root@gate:~# klist -k /etc/krb5.keytab
```

Настройка dovecot на использование GSSAPI
```
# nano /etc/dovecot/conf.d/10-auth.conf
```
```
...
auth_gssapi_hostname = "$ALL"
...
auth_mechanisms = gssapi plain
...
```
```
# chmod +r /etc/krb5.keytab
```
```
gate# mail user1
```

Roundcube LDAP Addressbook
Установка
```
# apt install php-net-ldap3
```
Настройка
```
# nano /var/lib/roundcube/config/config.inc.php
```
```
...
$config['ldap_public']['ldap'] = array(
  'name'          => 'Global Addresses',
  'hosts'         => array('dc.corp.ru'),
  'port'          => 389,
  'use_tls'       => false,
  'ldap_version'  => 3,       // using LDAPv3
  'network_timeout' => 10,    // The timeout (in seconds) for connect + bind arrempts. This is only supported in PHP >= 5.3.0 with OpenLDAP 2.x
  'user_specific' => false,   // If true the base_dn, bind_dn and bind_pass default to the user's IMAP login.

  'base_dn'       => 'cn=Users,dc=corp,dc=ru',
  'bind_dn'       => 'cn=Administrator,cn=Users,dc=corp,dc=ru',
  'bind_pass'     => 'Pa$$w0rd',

  'search_filter'  => '',   // e.g. '(&(objectClass=posixAccount)(uid=%u))'
  'fieldmap' => array(
    // Roundcube  => LDAP:limit
    'name'        => 'cn',
    'surname'     => 'sn',
    'firstname'   => 'givenName',
    'jobtitle'    => 'title',
    'email'       => 'mail:*',
    'phone:home'  => 'homePhone',
    'phone:work'  => 'telephoneNumber',
    'phone:mobile' => 'mobile',
    'phone:pager' => 'pager',
    'phone:workfax' => 'facsimileTelephoneNumber',
    'street'      => 'street',
    'zipcode'     => 'postalCode',
    'region'      => 'st',
    'locality'    => 'l',
    'country'      => 'c',
    'organization' => 'o',
    'department'   => 'ou',
    'jobtitle'     => 'title',
    'notes'        => 'description',
    'photo'        => 'jpegPhoto',
  ),
  'sort'           => 'cn',         // The field to sort the listing by.
  'scope'          => 'sub',        // search mode: sub|base|list
  'filter'         => '(mail=*)',      // used for basic listing (if not empty) and will be &'d with search queries. example: status=act
  'fuzzy_search'   => true,         // server allows wildcard search
  'vlv'            => false,        // Enable Virtual List View to more efficiently fetch paginated data (if server supports it)
  'vlv_search'     => false,        // Use Virtual List View functions for autocompletion searches (if server supports it)
  'numsub_filter'  => '(objectClass=organizationalUnit)',   // with VLV, we also use numSubOrdinates to query the total number of records. Set this filter to get all numSubOrdinates attributes for counting
  'config_root_dn' => 'cn=config',  // Root DN to search config entries (e.g. vlv indexes)
  'sizelimit'      => '0',          // Enables you to limit the count of entries fetched. Setting this to 0 means no limit.
  'timelimit'      => '0',          // Sets the number of seconds how long is spend on the search. Setting this to 0 means no limit.
  'referrals'      => false,        // Sets the LDAP_OPT_REFERRALS option. Mostly used in multi-domain Active Directory setups
  'dereference'    => 0,            // Sets the LDAP_OPT_DEREF option. One of: LDAP_DEREF_NEVER, LDAP_DEREF_SEARCHING, LDAP_DEREF_FINDING, LDAP_DEREF_ALWAYS
                                    // Used where addressbook contains aliases to objects elsewhere in the LDAP tree.
```
```
service apache2 restart
```
