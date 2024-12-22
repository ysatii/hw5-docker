# Домашнее задание к занятию 5. «Практическое применение Docker» Мельник Юрий Александрович

### Инструкция к выполнению

1. Для выполнения заданий обязательно ознакомьтесь с [инструкцией](https://github.com/netology-code/devops-materials/blob/master/cloudwork.MD) по экономии облачных ресурсов. Это нужно, чтобы не расходовать средства, полученные в результате использования промокода.
3. **Своё решение к задачам оформите в вашем GitHub репозитории.**
4. В личном кабинете отправьте на проверку ссылку на .md-файл в вашем репозитории.
5. Сопроводите ответ необходимыми скриншотами.

---
## Примечание: Ознакомьтесь со схемой виртуального стенда [по ссылке](https://github.com/netology-code/shvirtd-example-python/blob/main/schema.pdf)

---

## Задача 0
1. Убедитесь что у вас НЕ(!) установлен ```docker-compose```, для этого получите следующую ошибку от команды ```docker-compose --version```
```
Command 'docker-compose' not found, but can be installed with:

sudo snap install docker          # version 24.0.5, or
sudo apt  install docker-compose  # version 1.25.0-1

See 'snap info docker' for additional versions.
```
В случае наличия установленного в системе ```docker-compose``` - удалите его.  
2. Убедитесь что у вас УСТАНОВЛЕН ```docker compose```(без тире) версии не менее v2.24.X, для это выполните команду ```docker compose version```  
###  **Своё решение к задачам оформите в вашем GitHub репозитории!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!**

---
# Решение 0
Произведено удаление "docker-compose" 
![Скриншот 1](https://github.com/ysatii/hw5-docker/blob/main/img/docker1.jpg) 


## Задача 1
1. Сделайте в своем github пространстве fork [репозитория](https://github.com/netology-code/shvirtd-example-python/blob/main/README.md).
   Примечание: В связи с доработкой кода python приложения. Если вы уверены что задание выполнено вами верно, а код python приложения работает с ошибкой то используйте вместо main.py файл not_tested_main.py(просто измените CMD)
3. Создайте файл с именем ```Dockerfile.python``` для сборки данного проекта(для 3 задания изучите https://docs.docker.com/compose/compose-file/build/ ). Используйте базовый образ ```python:3.9-slim```. 
Обязательно используйте конструкцию ```COPY . .``` в Dockerfile. Не забудьте исключить ненужные в имадже файлы с помощью dockerignore. Протестируйте корректность сборки.  
4. (Необязательная часть, *) Изучите инструкцию в проекте и запустите web-приложение без использования docker в venv. (Mysql БД можно запустить в docker run).
5. (Необязательная часть, *) По образцу предоставленного python кода внесите в него исправление для управления названием используемой таблицы через ENV переменную.
---
### ВНИМАНИЕ!
!!! В процессе последующего выполнения ДЗ НЕ изменяйте содержимое файлов в fork-репозитории! Ваша задача ДОБАВИТЬ 5 файлов: ```Dockerfile.python```, ```compose.yaml```, ```.gitignore```, ```.dockerignore```,```bash-скрипт```. Если вам понадобилось внести иные изменения в проект - вы что-то делаете неверно!
---
## решение 1
1. Сделаем fork репозитория
![Скриншот 2](https://github.com/ysatii/hw5-docker/blob/main/img/docker2.jpg) 

3. листинг  Dockerfile.python
```
FROM python:3.9-slim
WORKDIR /app
COPY requirements.txt ./
RUN pip install -r requirements.txt
COPY main.py ./
CMD ["python", "main.py"]
```

листинг .dockerignore
```
# Git
.git
.gitignore
.gitattributes

# Python
__pycache__/
*.py[cod]
*.pyo
*.egg-info
*.egg

# Общие папки сборки
dist/
build/
bin/
obj/
lib/

# Переменные окружения
.env
.env.local

# Конфигурации IDE и редакторов
.idea/
.vscode/
*.iml
*.suo
*.user
*.db
*.sln

# Локальные настройки
*.local

# Python кэш
*.pyo
__pycache__/

# Docker кэш
docker-compose.override.yml
```

листинг .gitignore
```
# Переменные окружения
.env
.env.local
.env.*.local

# Конфигурации редакторов
.idea/
.vscode/
*.iml

# Локальные настройки
*.local

# Общие временные файлы
*.log
*.pid
*.seed
*.pid.lock

# Python кэш
*.pyc
*.pyo
*.pyd
__pycache__/

# Docker
docker-compose.override.yml
.dockerignore
```

команда для сборки образа 
```
docker build -f Dockerfile.python -t dock .
```
![Скриншот 3](https://github.com/ysatii/hw5-docker/blob/main/img/docker3.jpg) 

## Задача 2 (*)
1. Создайте в yandex cloud container registry с именем "test" с помощью "yc tool" . [Инструкция](https://cloud.yandex.ru/ru/docs/container-registry/quickstart/?from=int-console-help)
2. Настройте аутентификацию вашего локального docker в yandex container registry.
3. Соберите и залейте в него образ с python приложением из задания №1.
4. Просканируйте образ на уязвимости.
5. В качестве ответа приложите отчет сканирования.

## решение 2
1. создаем контейнер в yandex cloud container registry
```
yc container registry create --name test
```

сконфигурируем docker для использования 
```
yc container registry configure-docker
```

проверем что все сконфигурировано
```
/home/lamer/.docker/config.json
```

тегируем образ
```
docker tag dock cr.yandex/crpfc95k6pu9pkbp828q/dock:latest
```

загрузим  образ в реестр
```
docker push cr.yandex/crpfc95k6pu9pkbp828q/dock:latest
```
![Скриншот 6](https://github.com/ysatii/hw5-docker/blob/main/img/docker6.jpg) 

перейдем в яндекс облако и провизведем сканирование на наличие уязвимостей

![Скриншот 4](https://github.com/ysatii/hw5-docker/blob/main/img/docker4.jpg) 
![Скриншот 5](https://github.com/ysatii/hw5-docker/blob/main/img/docker5.jpg) 
![Скриншот 7](https://github.com/ysatii/hw5-docker/blob/main/img/docker7.jpg) 

## Задача 3
1. Изучите файл "proxy.yaml"
2. Создайте в репозитории с проектом файл ```compose.yaml```. С помощью директивы "include" подключите к нему файл "proxy.yaml".
3. Опишите в файле ```compose.yaml``` следующие сервисы: 

- ```web```. Образ приложения должен ИЛИ собираться при запуске compose из файла ```Dockerfile.python``` ИЛИ скачиваться из yandex cloud container registry(из задание №2 со *). Контейнер должен работать в bridge-сети с названием ```backend``` и иметь фиксированный ipv4-адрес ```172.20.0.5```. Сервис должен всегда перезапускаться в случае ошибок.
Передайте необходимые ENV-переменные для подключения к Mysql базе данных по сетевому имени сервиса ```web``` 

- ```db```. image=mysql:8. Контейнер должен работать в bridge-сети с названием ```backend``` и иметь фиксированный ipv4-адрес ```172.20.0.10```. Явно перезапуск сервиса в случае ошибок. Передайте необходимые ENV-переменные для создания: пароля root пользователя, создания базы данных, пользователя и пароля для web-приложения.Обязательно используйте уже существующий .env file для назначения секретных ENV-переменных!

4. Запустите проект локально с помощью docker compose , добейтесь его стабильной работы: команда ```curl -L http://127.0.0.1:8090``` должна возвращать в качестве ответа время и локальный IP-адрес. Если сервисы не стартуют воспользуйтесь командами: ```docker ps -a ``` и ```docker logs <container_name>``` . Если вместо IP-адреса вы получаете ```NULL``` --убедитесь, что вы шлете запрос на порт ```8090```, а не 5000.

5. Подключитесь к БД mysql с помощью команды ```docker exec -ti <имя_контейнера> mysql -uroot -p<пароль root-пользователя>```(обратите внимание что между ключем -u и логином root нет пробела. это важно!!! тоже самое с паролем) . Введите последовательно команды (не забываем в конце символ ; ): ```show databases; use <имя вашей базы данных(по-умолчанию example)>; show tables; SELECT * from requests LIMIT 10;```.

6. Остановите проект. В качестве ответа приложите скриншот sql-запроса.


3. рабочий файл находиться в репозитории [fork репозитория shvirtd-example-python](https://github.com/ysatii/shvirtd-example-python.git)
4. ![Скриншот 8](https://github.com/ysatii/hw5-docker/blob/main/img/docker8.jpg)   
   ![Скриншот 9](https://github.com/ysatii/hw5-docker/blob/main/img/docker9.jpg) 
5. Подключитесь к БД mysql 

```
docker exec -it shvirtd-example-python-db-1 bash
```

```
mysql -uroot -p
```

```
show databases;
```

```
show tables;
```

```
SELECT * from requests LIMIT 10;
```

  ![Скриншот 10](https://github.com/ysatii/hw5-docker/blob/main/img/docker10.jpg)   
  ![Скриншот 11](https://github.com/ysatii/hw5-docker/blob/main/img/docker11.jpg) 

## Задача 4
1. Запустите в Yandex Cloud ВМ (вам хватит 2 Гб Ram).
2. Подключитесь к Вм по ssh и установите docker.
3. Напишите bash-скрипт, который скачает ваш fork-репозиторий в каталог /opt и запустит проект целиком.
4. Зайдите на сайт проверки http подключений, например(или аналогичный): ```https://check-host.net/check-http``` и запустите проверку вашего сервиса ```http://<внешний_IP-адрес_вашей_ВМ>:8090```. Таким образом трафик будет направлен в ingress-proxy. ПРИМЕЧАНИЕ:  приложение main.py( в отличие от not_tested_main.py) весьма вероятно упадет под нагрузкой, но успеет обработать часть запросов - этого достаточно. Обновленная версия (main.py) не прошла достаточного тестирования временем, но должна справиться с нагрузкой.
5. (Необязательная часть) Дополнительно настройте remote ssh context к вашему серверу. Отобразите список контекстов и результат удаленного выполнения ```docker ps -a```
6. В качестве ответа повторите  sql-запрос и приложите скриншот с данного сервера, bash-скрипт и ссылку на fork-репозиторий.


## решение 4
1. подподготовим виртуальную машину на яндекс облаке.
  ![Скриншот 12](https://github.com/ysatii/hw5-docker/blob/main/img/docker12.jpg).

2. Установим досккер с помощию скрипта  
```sh
#!/bin/bash

# Проверяем, выполняется ли скрипт с правами root
if [ "$EUID" -ne 0 ]; then
  echo "Пожалуйста, запустите скрипт с правами root (sudo)."
  exit 1
fi

# Обновляем систему
echo "Обновляем списки пакетов..."
apt-get update -y

# Устанавливаем необходимые пакеты
echo "Устанавливаем необходимые зависимости..."
apt-get install -y \
    ca-certificates \
    curl \
    gnupg \
    lsb-release

# Добавляем GPG-ключ Docker
echo "Добавляем ключ Docker GPG..."
mkdir -p /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/$(lsb_release -is | tr '[:upper:]' '[:lower:]')/gpg | gpg --dearmor -o /etc/apt/keyrings/docker.gpg

# Добавляем репозиторий Docker
echo "Добавляем Docker-репозиторий..."
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/$(lsb_release -is | tr '[:upper:]' '[:lower:]') \
  $(lsb_release -cs) stable" | tee /etc/apt/sources.list.d/docker.list > /dev/null

# Обновляем списки пакетов
echo "Обновляем списки пакетов (после добавления репозитория)..."
apt-get update -y

# Устанавливаем Docker
echo "Устанавливаем Docker CE, CLI и контейнерный runtime..."
apt-get install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

# Проверяем установку
echo "Проверяем версию Docker..."
docker --version

# Устанавливаем автозапуск Docker
echo "Включаем автозапуск Docker..."
systemctl enable docker
systemctl start docker

echo "Установка Docker завершена!"
```

 ![Скриншот 13](https://github.com/ysatii/hw5-docker/blob/main/img/docker13.jpg)
 ![Скриншот 14](https://github.com/ysatii/hw5-docker/blob/main/img/docker14.jpg)

5. выполнение  команды ```docker ps -a```
 ![Скриншот 15](https://github.com/ysatii/hw5-docker/blob/main/img/docker15.jpg)

6. ссылка на fork репозиторий https://github.com/ysatii/shvirtd-example-python
 ![Скриншот 16](https://github.com/ysatii/hw5-docker/blob/main/img/docker16.jpg)

## Задача 5 (*)
1. Напишите и задеплойте на вашу облачную ВМ bash скрипт, который произведет резервное копирование БД mysql в директорию "/opt/backup" с помощью запуска в сети "backend" контейнера из образа ```schnitzler/mysqldump``` при помощи ```docker run ...``` команды. Подсказка: "документация образа."
2. Протестируйте ручной запуск
3. Настройте выполнение скрипта раз в 1 минуту через cron, crontab или systemctl timer. Придумайте способ не светить логин/пароль в git!!
4. Предоставьте скрипт, cron-task и скриншот с несколькими резервными копиями в "/opt/backup"

## Задача 6
Скачайте docker образ ```hashicorp/terraform:latest``` и скопируйте бинарный файл ```/bin/terraform``` на свою локальную машину, используя dive и docker save.
Предоставьте скриншоты  действий .

## ответ 6
Сохраните образ Docker в файл
```
docker save -o terraform.tar hashicorp/terraform:latest
```
Распакуйте содержимое сохранённого образа:
```
mkdir terraform_image
tar -xf terraform.tar -C terraform_image
```

найдем файл /bin/terraform
```
find extracted_layers -type f -executable -name "*terraform*"
```
ответ extracted_layers/bin/terraform

скопируем файл себе в домашнюю директорию
```
cp extracted_layers/bin/terraform ~/terraform
```
 ![Скриншот 17](https://github.com/ysatii/hw5-docker/blob/main/img/docker17.jpg)



## Задача 6.1
Добейтесь аналогичного результата, используя docker cp.  
Предоставьте скриншоты  действий .

## Задача 6.2 (**)
Предложите способ извлечь файл из контейнера, используя только команду docker build и любой Dockerfile.  
Предоставьте скриншоты  действий .

## Задача 7 (***)
Запустите ваше python-приложение с помощью runC, не используя docker или containerd.  
Предоставьте скриншоты  действий .