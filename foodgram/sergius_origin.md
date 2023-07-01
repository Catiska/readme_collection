# :man_cook: Foodgram-project :woman_cook:
___

#### - Чтобы хорошо готовить, недостаточно любить покушать. Надо полюбить сам процесс, вкладывать в каждую его минуту любовь. Любовь — это основа всей жизни, в том числе и кулинарии.
___
## :hammer_and_wrench: Инструменты и технологии 

<div>
<img src="https://img.icons8.com/fluency/240/null/python.png" width="40" height="40"/>
<img src="https://img.icons8.com/color/480/null/django.png" width="40" height="40"/>
<img src="https://img.icons8.com/color/48/null/javascript--v1.png" width="40" height="40"/>
<img src="https://img.icons8.com/color/240/null/nodejs.png" width="40" height="40"/>
<img src="https://cdn.icon-icons.com/icons2/2415/PNG/512/react_original_wordmark_logo_icon_146375.png" width="40" height="40"/>
<img src="https://img.icons8.com/color/240/docker.png" alt="docker" width="40" height="40"/>
<img width="40" height="40" src="https://img.icons8.com/color/480/nginx.png" alt="nginx"/>
<img width="40" height="40" src="https://svg2raster.fileformat.info/vlz.jsp?svg=%2Flogos%2Fgunicorn%2Fgunicorn-icon.svg&width=40&height=40" alt="gunicorn"/>
</div>

### Функционал сайта 

После регистрации у вас есть возможность:

- Просматривать, добавлять и удалять свои рецепты;
- Просматривать свои и чужие рецепты на главной странице с возможностью фильтроваить их по тэгам; 
- Просматривать страницы других пользователей;
- Подписываться на публикации других авторов;
- Работать с персональным списком избранного: добавлять в него рецепты или удалять их, просматривать свою страницу избранных рецептов.
- Работать с персональным списком покупок: добавлять/удалять любые рецепты, выгружать файл с количеством необходимых ингредиентов для рецептов из списка покупок.

### Демонстрация сайта
Посмотреть рабочую версию сайта можно по ссылке: https://foodgram-project.hopto.org/



## Установка 

1. Форкните репозиторий, клонируйте его, и перейдите в него:

    ```bash
    git clone ...
    ```
    ```bash
    cd foodgram-project-react
    ```
2. Создайте файл .env и заполните его своими данными:

    ```bash
    POSTGRES_DB=...
    POSTGRES_USER=...
    POSTGRES_PASSWORD=...
    DB_HOST=...
    DB_PORT=...
    ```

### Создание Docker-образов

1. Для создания Docker-образов последовательно выполните следующие команды, замените username на ваш логин на DockerHub:

    ```bash
    cd frontend
    docker build -t username/foodgram_frontend .
    cd ../backend
    docker build -t username/foodgram_backend .
    cd ../infra
    docker build -t username/foodgram_gateway . 
    ```

2. Загрузите образы на DockerHub:

    ```bash
    docker push username/foodgram_frontend
    docker push username/foodgram_backend
    docker push username/foodgram_gateway
    ```

### Деплой на сервере

1. Подключитесь к удаленному серверу

    ```bash
    ssh -i путь_до_файла_с_SSH_ключом/название_файла_с_SSH_ключом имя_пользователя@ip_адрес_сервера 
    ```

2. Создайте на сервере директорию foodgram

    ```bash
    mkdir foodgram
    ```

3. Для установки docker compose на сервер, поочередно выполните следующие команды:

    ```bash
    sudo apt update
    sudo apt install curl
    curl -fSL https://get.docker.com -o get-docker.sh
    sudo sh ./get-docker.sh
    sudo apt-get install docker-compose-plugin
    ```

4. Скопируйте на сервер в директорию foodgram/ файлы docker-compose.production.yml и .env:

    ```bash
    scp -i path_to_SSH/SSH_name docker-compose.production.yml username@server_ip:/home/username/foodgram/docker-compose.production.yml
    ```

5. Переходите в директорию foodgram/ и запустите docker compose в режиме демона:

    ```bash
    sudo docker compose -f docker-compose.production.yml up -d
    ```

6. Выполните миграции, соберите статические файлы бэкенда и скопируйте их в /backend_static/static/:

    ```bash
    sudo docker compose -f docker-compose.production.yml exec backend python manage.py migrate
    sudo docker compose -f docker-compose.production.yml exec backend python manage.py collectstatic
    sudo docker compose -f docker-compose.production.yml exec backend cp -r /app/collected_static/. /backend_static/static/
    ```

7. На сервере в редакторе nano откройте конфиг Nginx:

    ```bash
    nano /etc/nginx/sites-enabled/default
    ```

8. Измените настройки location в секции server:

    ```bash
    location / {
        proxy_pass http://127.0.0.1:8000;
    }
    ```

9. Проверьте работоспособность конфига и перезапустите Nginx:

    ```bash
    sudo nginx -t 
    sudo service nginx reload
    ```

### Настройка CI/CD

1. Файл workflow уже написан. Он находится в директории

    ```bash
    foodgram-project-react/.github/workflows/main.yml
    ```

2. Для адаптации его на своем сервере добавьте секреты в GitHub Actions:

    ```bash
    DOCKER_PASSWORD - пароль от аккаунта DockerHub
    DOCKER_USERNAME - логин DockerHub
    HOST - IP адресс сервера
    USER - логин на сервере
    SSH_KEY - SSH ключ
    SSH_PASSPHRASE - пароль от сервера
    TELEGRAM_TO - ваш Telegram ID
    TELEGRAM_TOKEN - токена вашего Telegram бота
    ```
