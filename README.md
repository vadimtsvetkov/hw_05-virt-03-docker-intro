# Домашнее задание к занятию 4 «Оркестрация группой Docker контейнеров на примере Docker Compose»

## Задача 1

Решение: https://hub.docker.com/repository/docker/vadimtsvetkofff/custom_nginx/general
## Задача 2
![img](https://github.com/vadimtsvetkov/hw_05-virt-03-docker-intro/blob/main/screen/docker1.png)
![img](https://github.com/vadimtsvetkov/hw_05-virt-03-docker-intro/blob/main/screen/docker2.png)
![img](https://github.com/vadimtsvetkov/hw_05-virt-03-docker-intro/blob/main/screen/docker3.png)


## Задача 3
1. Воспользуйтесь docker help или google, чтобы узнать как подключиться к стандартному потоку ввода/вывода/ошибок контейнера "custom-nginx-t2".
2. Подключитесь к контейнеру и нажмите комбинацию Ctrl-C.
![img](https://github.com/vadimtsvetkov/hw_05-virt-03-docker-intro/blob/main/screen/docker4.png)
3. Выполните ```docker ps -a``` и объясните своими словами почему контейнер остановился.
```
Выход был вызван командой Ctrl-C.
```

4. Перезапустите контейнер
5. Зайдите в интерактивный терминал контейнера "custom-nginx-t2" с оболочкой bash.
6. Установите любимый текстовый редактор(vim, nano итд) с помощью apt-get.
7. Отредактируйте файл "/etc/nginx/conf.d/default.conf", заменив порт "listen 80" на "listen 81".
8. Запомните(!) и выполните команду ```nginx -s reload```, а затем внутри контейнера ```curl http://127.0.0.1:80 ; curl http://127.0.0.1:81```.
![img](https://github.com/vadimtsvetkov/hw_05-virt-03-docker-intro/blob/main/screen/docker5.png)
9. Выйдите из контейнера, набрав в консоли  ```exit``` или Ctrl-D.
10. Проверьте вывод команд: ```ss -tlpn | grep 127.0.0.1:8080``` , ```docker port custom-nginx-t2```, ```curl http://127.0.0.1:8080```. Кратко объясните суть возникшей проблемы.
```
Проблема в том, что контейнер слушает порт 81, но мы пытаемся обратиться к порту 8080
```


## Задача 4


- Запустите первый контейнер из образа ***centos*** c любым тегом в фоновом режиме, подключив папку  текущий рабочий каталог ```$(pwd)``` на хостовой машине в ```/data``` контейнера, используя ключ -v.
- Запустите второй контейнер из образа ***debian*** в фоновом режиме, подключив текущий рабочий каталог ```$(pwd)``` в ```/data``` контейнера. 
- Подключитесь к первому контейнеру с помощью ```docker exec``` и создайте текстовый файл любого содержания в ```/data```.
- Добавьте ещё один файл в текущий каталог ```$(pwd)``` на хостовой машине.
- Подключитесь во второй контейнер и отобразите листинг и содержание файлов в ```/data``` контейнера.

В качестве ответа приложите скриншоты консоли, где видно все введенные команды и их вывод.

![img](https://github.com/vadimtsvetkov/hw_05-virt-03-docker-intro/blob/main/screen/docker6.png)
## Задача 5

1. Создайте отдельную директорию(например /tmp/netology/docker/task5) и 2 файла внутри него.
"compose.yaml" с содержимым:
```
version: "3"
services:
  portainer:
    image: portainer/portainer-ce:latest
    network_mode: host
    ports:
      - "9000:9000"
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
```
"docker-compose.yaml" с содержимым:
```
version: "3"
services:
  registry:
    image: registry:2
    network_mode: host
    ports:
    - "5000:5000"
```

И выполните команду "docker compose up -d". Какой из файлов был запущен и почему? (подсказка: https://docs.docker.com/compose/compose-application-model/#the-compose-file )
```
Docker Compose полагается на файл конфигурации YAML, обычно называемый compose.yaml. 
Файл compose.yaml следует правилам, предусмотренным спецификацией Compose в разделе "Как определить многоконтейнерные приложения". 
Это реализация формальной спецификации Compose в Docker Compose
```
![img](https://github.com/vadimtsvetkov/hw_05-virt-03-docker-intro/blob/main/screen/docker7.png)
2. Отредактируйте файл compose.yaml так, чтобы были запущенны оба файла. (подсказка: https://docs.docker.com/compose/compose-file/14-include/)
```
Чтобы запустить оба файла, можно использовать опцию -f для явного указания файлов при запуске.
Отредактируем файл compose.yaml следующим образом

version: "3"
services:
  portainer:
    image: portainer/portainer-ce:latest
    network_mode: host
    ports:
      - "9000:9000"
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
---
version: "3"
services:
  registry:
    image: registry:2
    network_mode: host
    ports:
      - "5000:5000"
```
![img](https://github.com/vadimtsvetkov/hw_05-virt-03-docker-intro/blob/main/screen/docker8.png)
3. Выполните в консоли вашей хостовой ОС необходимые команды чтобы залить образ custom-nginx как custom-nginx:latest в запущенное вами, локальное registry. Дополнительная документация: https://distribution.github.io/distribution/about/deploying/
![img](https://github.com/vadimtsvetkov/hw_05-virt-03-docker-intro/blob/main/screen/docker9.png)
4. Откройте страницу "https://127.0.0.1:9000" и произведите начальную настройку portainer.(логин и пароль адмнистратора)
5. Откройте страницу "http://127.0.0.1:9000/#!/home", выберите ваше local  окружение. Перейдите на вкладку "stacks" и в "web editor" задеплойте следующий компоуз:

```
version: '3'

services:
  nginx:
    image: 127.0.0.1:5000/custom-nginx
    ports:
      - "9090:80"
```
6. Перейдите на страницу "http://127.0.0.1:9000/#!/2/docker/containers", выберите контейнер с nginx и нажмите на кнопку "inspect". В представлении <> Tree разверните поле "Config" и сделайте скриншот от поля "AppArmorProfile" до "Driver".

![img](https://github.com/vadimtsvetkov/hw_05-virt-03-docker-intro/blob/main/screen/scr.png)

7. Удалите любой из манифестов компоуза(например compose.yaml).  Выполните команду "docker compose up -d". Прочитайте warning, объясните суть предупреждения и выполните предложенное действие. Погасите compose-проект ОДНОЙ(обязательно!!) командой.

WARN[0000] Found orphan containers ([task5-portainer-1]) for this project. If you removed or renamed this service in your compose file, you can run this command with the --remove-orphans flag to clean it up.

Ошибка указывает на то, что существуют контейнеры, которые не соответствуют текущему составу docker-compose.yaml.

Выполнил docker compose down --remove-orphans.
