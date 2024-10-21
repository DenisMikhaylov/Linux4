Упражнение 1: Развертывание AD DS

Сценарий

В рамках расширения бизнеса корпорация COPR хочет развернуть новые контроллеры домена на
удалённых сайтах с минимальным участием со стороны ИТ-персонала. Вы будете использовать Windows PowerShell для
развертывания новых контроллеров домена.

Основные задачи для этого упражнения следующие:
1. Установка двоичных файлов AD DS.
2. Подготовка установки AD DS и продвижение удалённого сервера.
3. Запуск анализатора передового опыта AD DS


Задача 1: Установка двоичных файлов AD DS
1. В диспетчере серверов щелкните Tools, а затем щелкните Windows PowerShell.
2. В командной строке интерфейса командной строки Windows PowerShell введите следующую
команду и нажмите Enter:
Install-WindowsFeature –Name AD-Domain-Services –ComputerName LON-SVR1
3. Введите следующую команду, чтобы убедиться, что роль AD DS установлена ​​на LON-SVR1, а затем нажмите Enter:
Get-WindowsFeature –ComputerName LON-SVR1


Настройка DNS сервера
```
DNS→SERVER→Properties→Forwardes→8.8.8.8
```
Добавление пользователей в домен
Открыть Powershell
```
New-ADUser -Name "Ivan I. Ivanov" -DisplayName "Ivan I. Ivanov" -GivenName "Ivan" -Initials "I" -Surname "Ivanov" `
-SamAccountName "user1" -AccountPassword(ConvertTo-SecureString -AsPlainText 'Pa$$w0rd' -Force) `
-Enabled $true -ChangePasswordAtLogon $false

New-ADUser -Name "Petr P. Petrov" -DisplayName "Petr P. Petrov" -GivenName "Petr" -Initials "P" -Surname "Petrov" `
-SamAccountName "user2" -AccountPassword(ConvertTo-SecureString -AsPlainText 'Pa$$w0rd' -Force) `
-Enabled $true -ChangePasswordAtLogon $false
```
