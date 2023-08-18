# Kittygram — социальная сеть для обмена фотографиями любимых питомцев. Это полностью рабочий проект, который состоит из бэкенд-приложения на Django и фронтенд-приложения на React.

## Описание проекта
Пользователи могут регистрироваться, загружать фотографии своих котов с кратким описанием и смотреть котиков других пользователей.

### Примеры запросов API:
* Создание нового пользователя (POST-запрос):
  
  - api/users/
```
    {
        "email": "string",
        "username": "string",
        "password": "string"
    }

``` 
* Получение токена для аутентификации (POST-запрос): 

  - api/token/login/
```
    {
        "username": "string",
        "password": "string"
    }

```

* Получить данные о всех пользователях (GET-запрос):

  - api/users/
```
    {
    "count": 2,
    "next": null,
    "previous": null,
    "results": [
        {
            "email": "user1@mail.ru",
            "id": 1,
            "username": "user1"
        }
        {
            "email": "user2@mail.ru",
            "id": 2,
            "username": "user2"
        }
    ]
}

```

* Получить список всех котиков (GET-запрос): 

  - api/сats/

```
{
    "count": 10,
    "next": null,
    "previous": null,
    "results": [
        {
            "id": 1,
            "name": "Барсик",
            "color": "black",
            "birth_year": 2002,
            "achievements": [
                {
                    "id": 1,
                    "achievement_name": "Съел рыбку"
                },
                {
                    "id": 2,
                    "achievement_name": "Упал с забора"
                }
            ],
            "owner": "Сергей",
            "age": 44,
            "image": "http://127.0.0.1:8080/media/cats/images/temp.png",
            "image_url": "/media/cats/images/temp.png"
        },
  ....

```
* Разместить фото котика (POST-запрос): 

  - api/cats/
  - В поле image передавать строку с картинкой в формате base64 

```
        {
            "name": "Василий",
            "color": "#DCDCDC",
            "birth_year": 2020,
            "image": base64
            "achievements": [
                {"achievement_name": "разбил вазу"}
            ]
        }   

```


# Установка проекта на локальный компьютер из репозитория Github
- форкнуть репозиторий git@github.com:isv160179/infra_sprint1.git себе на GitHub
- клонировать репозиторий `git clone <адрес вашего репозитория>` на локальный компьютер
- перейти в директорию с клонированным репозиторием
- установить виртуальное окружение `python3 -m venv venv`
- установить зависимости `pip install -r requirements.txt`
- в директории `/backend/kittygram_backend/` создать файл .env
- в файле .env прописать ваш `SECRET_KEY`, `DEBUG=True` и `ALLOWED_HOSTS`


# Деплой проекта на удаленный сервер

## Подключение сервера к аккаунту на GitHub
- на сервере должен быть установлен Git. Если сервер работает на операционной системе Линукс, то можно выполнить проверку наличия Git командой `sudo apt update git --version`
- с этого пункта подразумевается, что сервер работает на Линуксе, поэтому все команды будут написаны для операционной системы Линукс.
- если Git не установлен - установить командой `sudo apt install git`
- находясь на сервере сгенерировать пару SSH-ключей командой `ssh-keygen`
- сохранить открытый ключ в вашем аккаунте на GitHub. Для этого вывести ключ в терминал командой `cat .ssh/id_rsa.pub` Скопировать ключ от символов ssh-rsa, включительно, и до конца. Добавить это ключ к вашему аккаунту на GitHub.
- клонировать проект с GitHub на сервер: `git clone git@github.com:Ваш_аккаунт/infra_sprint1.git`


## Запуск backend проекта на сервере
- установить пакетный менеджер и утилиту для создания виртуального окружения `sudo apt install python3-pip python3-venv -y`
- находясь в директории с проектом создать виртуальное окружение `python3 -m venv venv` и активировать его `source venv/bin/activate`
- установить зависимости `pip install -r requirements.txt`
- выполнить миграции `python manage.py migrate`
- создать суперюзера `python manage.py createsuperuser`

## Запуск frontend проекта на сервере
- установить на сервер `Node.js` командами `curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash - &&\ sudo apt-get install -y nodejs`
- установить зависимости frontend приложения. Из директории `infra_sprint1/frontend/` выполнить команду: `npm i`


## Установка и запуск Gunicorn
- при активированном виртуальном окружении проекта проверить наличие установленного пакета gunicorn `pip install gunicorn==20.1.0`
- В директории `/etc/systemd/system/` создайте файл `gunicorn.service` командой `sudo nano /etc/systemd/system/gunicorn.service` со следующим кодом (без комментариев):

```
  [Unit]

  Description=gunicorn daemon

  After=network.target

  [Service]

  User=yc-user

  WorkingDirectory=/home/<имя пользователя в системе>/infra_sprint1/backend/

  ExecStart=/home/<имя пользователя в системе>/infra_sprint1/venv/bin/gunicorn --bind 0.0.0.0:8080 kittygram_backend.wsgi

  [Install]

  WantedBy=multi-user.target
```

Чтобы точно узнать путь до Gunicorn можно при активированном виртуальном окружении использовать команду `which gunicorn`


## Установка и настройка Nginx
- на сервере из любой директории выполнить команду: `sudo apt install nginx -y`
- для установки ограничений на открытые порты выполнить по очереди команды: `sudo ufw allow 'Nginx Full'` потом `sudo ufw allow OpenSSH`
- включить файервол `sudo ufw enable`


## Сбор и размещение статики frontend-приложения.
- перейти в директорию `infra_sprint1/frontend/` и выполнить команду `npm run build` Результат сохранится в директории `.../frontend/build/` В системную директорию сервера `/var/www/` скопировать содержимое папки `/frontend/build/`
- открыть файл конфигурации веб-сервера `sudo nano /etc/nginx/sites-enabled/default` и заменить его содержимое следующим кодом:

```
  server {

      listen 80;
      server_name публичный_ip_вашего_удаленного_сервера;
      server_tokens off;
      location / {
      root   /var/www/<имя проекта>;
      index  index.html index.htm;
      try_files $uri /index.html;
      }

  }
```

- проверить корректность конфигурации `sudo nginx -t` Это самый важный пункт, потому что при любых изменениях в настройках нужно проверять их корректность
- перезагрузить конфигурацию Nginx `sudo systemctl reload nginx`


## Настройка проксирования запросов
- Открыть файл конфигурации Nginx `/etc/nginx/sites-enabled/default` и добавить в него ещё один блок `location`

```
  server {

      listen 80;
      server_name публичный_ip_вашего_удаленного_сервера;
      server_tokens off;
      location /api/ {
          proxy_pass http://127.0.0.1:8080;
      }
      
      location /admin/ {
  	    proxy_pass http://127.0.0.1:8000;
  		}
  	
      location / {
          root   /var/www/<имя_проекта>;
          index  index.html index.htm;
          try_files $uri /index.html;
      }

  }
```

- сохранить изменения, проверить и перезагрузить конфигурацию веб-сервера: `sudo nginx -t` потом  `sudo systemctl reload nginx`


## Добавление доменного имени в настройки Django
- в файле .env добавить в список `ALLOWED_HOSTS` доменное имя: `ALLOWED_HOSTS=`
- сохранить изменения и перезапустить gunicorn sudo systemctl restart gunicorn
- внести изменения в конфигурацию Nginx. Открыть конфигурационный файл командой: `sudo nano /etc/nginx/sites-enabled/default`
- добавьте в строку `server_name` выданное вам доменное имя (через пробел, без < >):

```
  server {
  ...
      server_name <ваш-ip> <ваш-домен>;
  ...
  }
```

- проверить конфигурацию `sudo nginx -t` и перезагрузить её командой `sudo systemctl reload nginx`, чтобы изменения вступили в силу.

## Получение и настройка SSL-сертификата
### Установка certbot

- зайдите на сервер и последовательно выполните команды:

```
 sudo apt install snapd
 sudo snap install core; sudo snap refresh core
 sudo snap install --classic certbot
 sudo ln -s /snap/bin/certbot /usr/bin/certbot
```

- запустить certbot и получить SSL-сертификат: `sudo certbot --nginx`
- сертификат автоматически сохранится на вашем сервере в системной директории `/etc/ssl/` Также будет автоматически изменена конфигурация Nginx: в файл `/etc/nginx/sites-enabled/default` добавятся новые настройки и будут прописаны пути к сертификату.
- перезагрузить конфигурацию Nginx `sudo systemctl reload nginx`
