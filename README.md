# infra_sprint1
## Описание проекта
Финальный проект спринта: деплой проекта Kittygram на удалённый сервер.
Kittygram — социальная сеть для обмена фотографиями любимых питомцев. Это полностью рабочий проект, который состоит из бэкенд-приложения на Django и фронтенд-приложения на React.

## Деплой проекта на удаленный сервер

### Запуск проекта на сервер
#### Backend
- Склонируйте репозиторий:
``` git@github.com:Milkyaway13/infra_sprin1.git ```
- установите на сервер пакетный менеджер и утилиту для создания виртуального окружения:
``` sudo apt install python3-pip python3-venv -y ```
- Перейдите в директорию с бэкенд-приложением проекта:
``` /infra_sprin1/backend/ ```
- Установите и активируйте виртуальное окружение:
``` python3 -m venv venv ```  
``` source venv/bin/activate ``` 
- Установите зависимости из файла requirements.txt:   
``` pip install -r requirements.txt ```
- Примените миграции:   
``` python manage.py migrate ```
- Создайте суперюзера:   
``` python manage.py createsuperuser ```

### Frontend
- Установите на сервер пакетный менеджер npm:
``` curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash - &&\ ```
``` sudo apt-get install -y nodejs ```
- Установите зависимости для приложения в директории infra_sprint1/frontend:
``` npm i  ```

### Установка Gunicorn
- Установите и запустите Gunicorn из директории /backend/:
``` pip install gunicorn==20.1.0  ```
``` gunicorn --bind 0.0.0.0:8080 backend_kittygram.wsgi  ```
- Создайте юнит для Gunicorn:
``` sudo nano /etc/systemd/system/gunicorn_kittygram.service  ```
- Управление юнитом для Gunicorn:
``` sudo systemctl start/stop/restart gunicorn_kittygram ```
``` sudo systemctl enable gunicorn_kittygram ```
``` sudo systemctl status gunicorn_kittygram ```

### Установка Nginx
- Установите и запустите nginx:
``` sudo apt install nginx -y ```
``` sudo systemctl start nginx ```
- Укажите файрволу, какие порты должны остаться открытыми, включите его:
``` sudo ufw allow 'Nginx Full' ```
``` sudo ufw allow OpenSSH  ```
``` sudo ufw enable  ```


### Сборка статики frontend-приложения
- Соберите frontend-приложение из директории /frontend/:
``` npm run build  ```
- Скопируйте в системную директорию веб-сервера содержимое папки /frontend/build/:
``` sudo cp -r /home/yc-user/infra_sprint1/frontend/build/. /var/www/kittygram/ ```
- Опишите настройки для работы со статикой:
``` sudo nano /etc/nginx/sites-enabled/default ```
- Перезагрузите конфигурацию Nginx:
``` sudo systemctl reload nginx ```


### Сборка статики backend-приложения
- В settings.py укажите:
STATIC_URL = 'static_backend'
STATIC_ROOT = BASE_DIR / 'static_backend'
- Соберите статику:
``` python manage.py collectstatic ```
- Перейдите в корень проекта и скопируйте директорию static_backend/ в директорию /var/www/kittygram/:
``` sudo cp -r /home/yc-user/infra_sprint1/backend/static_backend/ /var/www/kittygram/ ```
- Чтобы изменения в файле settings.py вступили в силу, перезапустите Gunicorn:
``` sudo systemctl restart gunicorn_kittygram ```

#### Автор проекта

[Боярчук Василий](https://github.com/Milkyaway13)
