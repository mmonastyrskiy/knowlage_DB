Дисклеймер.  
Все инструменты описанные в данной статье реальны, находятся в свободном доступе и используются специалистами по Информационной Безопасности для проведения тестирования на проникновение информационных систем компаний и предприятий. Автор статьи не занимается взломом и не поощряет подобных действий, все описанное ниже было выполнено на специально подготовленной для этого виртуальной машине

Начало привычное, ничего нового
[[nmap]]


```
Nmap scan report for 10.10.10.193

Host is up (0.12s latency).

Not shown: 988 filtered ports

PORT STATE SERVICE VERSION

53/tcp open domain?

| fingerprint-strings:

| DNSVersionBindReqTCP:

| version

|_ bind

80/tcp open http Microsoft IIS httpd 10.0

| http-methods:

|_ Potentially risky methods: TRACE

|http-server-header: Microsoft-IIS/10.0

|http-title: Site doesn't have a title (text/html).

88/tcp open kerberos-sec Microsoft Windows Kerberos (server time: 2020-10-05 16:11:22Z)

135/tcp open msrpc Microsoft Windows RPC

139/tcp open netbios-ssn Microsoft Windows netbios-ssn

389/tcp open ldap Microsoft Windows Active Directory LDAP (Domain: fabricorp.local, Site: Default-First-Site-Name)

445/tcp open microsoft-ds Windows Server 2016 Standard 14393 microsoft-ds (workgroup: FABRICORP)

464/tcp open kpasswd5?

593/tcp open ncacn_http Microsoft Windows RPC over HTTP 1.0

636/tcp open tcpwrapped

3268/tcp open ldap Microsoft Windows Active Directory LDAP (Domain: fabricorp.local, Site: Default-First-Site-Name)

3269/tcp open tcpwrapped

1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :

SF-Port53-TCP:V=7.80%I=7%D=10/5%Time=5F7B4147%P=x86_64-pc-linux-gnu%r(DNSV

SF:ersionBindReqTCP,20,"\0\x1e\0\x06\x81\x04\0\x01\0\0\0\0\0\0\x07version\

SF:x04bind\0\0\x10\0\x03");

Service Info: Host: FUSE; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:

|clock-skew: mean: 2h38m49s, deviation: 4h02m31s, median: 18m47s

| smb-os-discovery:

| OS: Windows Server 2016 Standard 14393 (Windows Server 2016 Standard 6.3)

| Computer name: Fuse

| NetBIOS computer name: FUSE\x00

| Domain name: fabricorp.local

| Forest name: fabricorp.local

| FQDN: Fuse.fabricorp.local

|_ System time: 2020-10-05T09:13:45-07:00

| smb-security-mode:

| account_used: guest

| authentication_level: user

| challenge_response: supported

|_ message_signing: required

| smb2-security-mode:

| 2.02:

|_ Message signing enabled and required

| smb2-time:

| date: 2020-10-05T16:13:44

|_ start_date: 2020-10-05T08:01:00

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
```
Сервисов много, а интересного мало, лучше пойдем посмотрим в вебчик, там как минимум больше шансов поймать что-то стоящее.

Веб сразу редеректит нас на доменное имя, хм, ну ладно, хорошо что мы знаем, что с этим делать прописываем его в **hosts** и погнали смотреть.

система логирования взаимодействий с принтером? Не самое обыкновенное развлечение, ну ладно, запустим [[gobuster]]….

gobusterничего не дал ну да ладно, как минимум руками мы нашли список пользователей, которые что-то печатали, можно уже с этим как-то работать

Вспомнив про то, что у нас есть модуль [[metasploit]] smb_login

Для того чтобы им корректно воспользоваться нужен словарь из имен пользователей, который мы только что «надыбали» и словарь с паролями, можно конечно попробовать привычный rockyou.txt, но во-первых долго, во-вторых толку 0. Попробуем составить более узконаправленный словарь, специально для нашей цели, воспользуемся инструментом cewl

Прогоном получаем учетки в [[Samba]], зайти тоже не дает, требует смену пароля. ну… окей поменяем у нас есть smbpasswd

смена пароля дает некоторый доступ к местным данным, гуляя по ним находим более полный список пользователей и один рабочий пароль не известно от какого пользователя. попробуем таким методом получить удаленный доступ, вспомнив что это Windows, можно просканировать дополнительно порт 5985 в попытке найти winRM, и когда он там найдется, снова сходить в [[metasploit]] и использовать модуль проверки **winrm,** когда подходящая учетка найдется использовать [[evil-winrm]] для получения доступа, забрать **user.txt**

затем сканируем комп при помощи [[winPEAS]] находим неправильную конфигурацию в работе драйверов, затем находим **C-шный** эксплоит на **GitHub**, не будем же мы писать его с 0, вносим нужные изменения и компилим в exe при помощи [[gcc]] потом заливаем на «тачку» используем и получаем шелл под админом .