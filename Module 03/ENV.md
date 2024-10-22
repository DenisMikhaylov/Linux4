Установка переменных окружения
```
$ nano .bashrc
```
```
...
export http_proxy=http://gate.corp.ru:3128/
export https_proxy=http://gate.corp.ru:3128/
export no_proxy=localhost,127.0.0.1,corp.ru
...
```
```
# nano /etc/environment
```
```
...
#http_proxy=http://gate.corp.ru:3128
https_proxy=http://gate.corp.ru:3128
no_proxy=localhost,127.0.0.1,corp.ru
```
