# Домашнее задание к занятию «Docker. Часть 2»

---

### Задание 1

**Напишите ответ в свободной форме, не больше одного абзаца текста.**

Установите Docker Compose и опишите, для чего он нужен и как может улучшить вашу жизнь.

#### Ответ:
Docker Compose — это инструментальное средство, входящее в состав Docker. Оно разработано для помощи в определении и совместном использовании многоконтейнерных приложений. С помощью Docker Compose можно создать 1 YAML-файл для определения служб и с помощью одной команды запустить и остановить все, что нужно при развертывании многоконтейнерных приложений.

---

### Задание 2 

**Выполните действия и приложите текст конфига на этом этапе.** 

Создайте файл docker-compose.yml и внесите туда первичные настройки: 

 * version;
 * services;
 * networks.

При выполнении задания используйте подсеть 172.22.0.0.
Ваша подсеть должна называться: <ваши фамилия и инициалы>-my-netology-hw.

#### Скриншот:

![2](https://github.com/AleksandrNiylek/-Docker.-2-/assets/136619576/7057e8b7-7114-4579-b47e-12ac2796d609)



---

### Задание 3 

**Выполните действия и приложите текст конфига текущего сервиса:** 

1. Установите PostgreSQL с именем контейнера <ваши фамилия и инициалы>-netology-db. 
2. Предсоздайте БД <ваши фамилия и инициалы>-db.
3. Задайте пароль пользователя postgres, как <ваши фамилия и инициалы>12!3!!
4. Пример названия контейнера: ivanovii-netology-db.
5. Назначьте для данного контейнера статический IP из подсети 172.22.0.0/24.

#### Скриншот:

![3](https://github.com/AleksandrNiylek/-Docker.-2-/assets/136619576/3356ccd5-7b6a-4c9b-b97c-5ef0e1dd465e)




---

### Задание 4 

**Выполните действия:**

1. Установите pgAdmin с именем контейнера <ваши фамилия и инициалы>-pgadmin. 
2. Задайте логин администратора pgAdmin <ваши фамилия и инициалы>@ilove-netology.com и пароль на выбор.
3. Назначьте для данного контейнера статический IP из подсети 172.22.0.0/24.
4. Прокиньте на 80 порт контейнера порт 61231.

В качестве решения приложите:

* текст конфига текущего сервиса;
* скриншот админки pgAdmin.

#### Скриншот:
![4](https://github.com/AleksandrNiylek/-Docker.-2-/assets/136619576/e8c36c71-e197-4d78-8c80-872fa0d470f3)



---

### Задание 5 

**Выполните действия и приложите текст конфига текущего сервиса:** 

1. Установите Zabbix Server с именем контейнера <ваши фамилия и инициалы>-zabbix-netology. 
2. Настройте его подключение к вашему СУБД.
3. Назначьте для данного контейнера статический IP из подсети 172.22.0.0/24.

#### Скриншот:
![5](https://github.com/AleksandrNiylek/-Docker.-2-/assets/136619576/4734900b-f43d-4899-956e-16d51f2135de)



---

### Задание 6

**Выполните действия и приложите текст конфига текущего сервиса:** 

1. Установите Zabbix Frontend с именем контейнера <ваши фамилия и инициалы>-netology-zabbix-frontend. 
2. Настройте его подключение к вашему СУБД.
3. Назначьте для данного контейнера статический IP из подсети 172.22.0.0/24.

#### Скриншот:
![6](https://github.com/AleksandrNiylek/-Docker.-2-/assets/136619576/53cdb6de-2044-4a88-853d-c4b3f175f7dd)


---

### Задание 7 

**Выполните действия.**

Настройте линки, чтобы контейнеры запускались только в момент, когда запущены контейнеры, от которых они зависят.

В качестве решения приложите:

* текст конфига **целиком**;
* скриншот команды docker ps;
* скриншот авторизации в админке Zabbix.

#### Текст конфига:
version: '3'
services:
  podluzhniy-netology-db:
    image: postgres:latest
    container_name: podluzhniy-netology-db
    ports:
      - 5432:5432
    volumes:
      - ./pg_data:/var/lib/postgresql/data/pgdata
    environment:
      POSTGRES_PASSWORD: 123
      POSTGRES_DB: podluzhniy_db
      PGDATA: /var/lib/postgresql/data/pgdata
    networks:
      podluzhniy-my-netology:
        ipv4_address: 172.22.0.2
    restart: always

  pgadmin:
    image: dpage/pgadmin4
    container_name: podluzhniy-netology-pgadmin
    environment:
      PGADMIN_DEFAULT_EMAIL: podluzhniy@ilove-netology.com
      PGADMIN_DEFAULT_PASSWORD: 123
    ports:
      - "61231:80"
    networks:
      podluzhniy-my-netology:
        ipv4_address: 172.22.0.3
    restart: always

  zabbix-server:
    image: zabbix/zabbix-server-pgsql
    links:
      - podluzhniy-netology-db
    container_name: podluzhniy-zabbix-netology
    environment:
      DB_SERVER_HOST: '172.20.0.2'
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: 123
    ports:
      - "10051:10051"
    networks:
      podluzhniy-my-netology:
        ipv4_address: 172.22.0.4
    restart: always

  zabbix_wgui:
    image: zabbix/zabbix-web-apache-pgsql
    links:
      - podluzhniy-netology-db
      - zabbix-server
    container_name: podluzhniy-netolog-zabbix-frontend
    environment:
      DB_SERVER_HOST: '172.20.0.2'
      POSTGRES_USER: 'postgres'
      POSTGRES_PASSWORD: 123
      ZBX_SERVER_HOST: "zabbix_wqui"
      PHP_TZ: "Europe/Moscow"
    ports:
      - "80:8080"
      - "443:8443"
    networks:
      podluzhniy-my-netology:
        ipv4_address: 172.22.0.5
    restart: always


networks:
  podluzhniy-my-netology:
    driver: bridge
    ipam:
      config:
      - subnet: 172.22.0.0/24



---

### Задание 8 

**Выполните действия:** 

1. Убейте все контейнеры и потом удалите их.
1. Приложите скриншот консоли с проделанными действиями.

---

## Дополнительные задания* (со звёздочкой)

Их выполнение необязательное и не влияет на получение зачёта по домашнему заданию. Можете их решить, если хотите лучше разобраться в материале.

---

### Задание 9* 

Запустите свой сценарий на чистом железе без предзагруженных образов.

**Ответьте на вопросы в свободной форме:**

1. Сколько ушло времени на то, чтобы развернуть на чистом железе написанный вами сценарий?
2. Чем вы занимались в процессе создания сценария так, как это видите вы?
3. Что бы вы улучшили в сценарии развёртывания?
