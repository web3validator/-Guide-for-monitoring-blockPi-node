# Guide-for-monitoring-blockPi-node


![Знімок екрану з 2022-07-17 08-56-01](https://user-images.githubusercontent.com/102728347/179419628-7525b62a-1c7c-4729-a298-cd53b17e3ad5.png)


# Встановлюємо grafana і prometheus на сервер де не працює BlockPI.

### Заходимо в root користувача
```
sudo su

```

### Спочатку оновлюємо пакети
```
sudo apt update && sudo apt upgrade -y

```
![Знімок екрану з 2022-07-17 20-57-12](https://user-images.githubusercontent.com/102728347/179419501-e4e929f5-8865-441b-95d2-da789601523a.png)

### Установлюємо docker
(не обов'язково якщо встановлений docker)
```
sudo apt update
sudo apt install apt-transport-https ca-certificates curl software-properties-common
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu focal stable"
sudo apt update
apt-cache policy docker-ce
sudo apt install docker-ce

```
### Установлюємо git
(не обов'язково якщо встановлений git)
```
apt install git

```

### Завантажуємо docker-compose.yml
```
cd ~
git clone https://github.com/cybernekit/Grafana-Prometheus.git
cd ~/Grafana-Prometheus

```
![Знімок екрану з 2022-07-17 20-58-03](https://user-images.githubusercontent.com/102728347/179419552-96119570-2bc5-41e1-9885-fbddb1e1b22d.png)

### Створюємо контейнер
```
sudo docker swarm init
sudo docker stack deploy -c ~/Grafana-Prometheus/docker-compose.yml monitoring

```

![Знімок екрану з 2022-07-17 20-59-00](https://user-images.githubusercontent.com/102728347/179419593-96511073-ddc2-4763-8e84-032b8efff263.png)


### Провіряємо чи запустилися контейнери
Не спіши запускати зачикай деякий час щоб воно все запустило.

```
sudo docker ps

```

![Знімок екрану з 2022-07-17 21-01-08](https://user-images.githubusercontent.com/102728347/179419606-c73204ba-3eac-46db-bdf7-f26d2d786a31.png)

 
# Встановлюємо node-exporter на сервер де знаходться BlockPI
Встановлюємо node-exporter через таку команду
```
wget https://raw.githubusercontent.com/MaxMavaIll/BlockPI_monitoring/main/node-exporter.sh  && chmod +x node-exporter.sh && bash node-exporter.sh

```


# Налаштування конфігурації prometheus
Переходимо до конфігурацюї promtheus. Файл називадться `promtheus.yml` і запінюємо його на файл з правильною конфігурацюєю.
```
cd /var/lib/docker/volumes/monitoring_prom-configs/_data
rm prometheus.yml
https://github.com/cybernekit/-Guide-for-monitoring-blockPi-node/blob/main/
wget https://raw.githubusercontent.com/cybernekit/-Guide-for-monitoring-blockPi-node/blob/main/prometheus.yml

```

Тепер замінюємо в цьому файлі `your_address` на ваш адрес
```
sed -i 's/your_address/<Your_address>/' /var/lib/docker/volumes/monitoring_prom-configs/_data/prometheus.yml
```
### Перезапускаємо prometheus
Для того щоб перезапустити прометеус нам потрібно дізнатися id контейнера
```
sudo docker ps

```
![Знімок екрану з 2022-07-17 21-07-32](https://user-images.githubusercontent.com/102728347/179419613-5d30a29e-0927-4d8d-a89c-fac291d63473.png)

```
sudo docker restart <CONTAINER ID>
```
Все ви підняли prometheus. 
Тепер щоб провірити чи він в справному стані, ми водимо в браузері.
```
http://<your_address_grafana>:9090
```
(Error -> Якщо виводить на екран що сторінку не знайдено зачекайте трішки і спробуйте знову увести свій адрес з портом)

Натискамо Status -> Targets

![Screenshot from 2022-07-17 21-41-11](https://user-images.githubusercontent.com/59205554/179420255-daccece1-7331-46db-8c78-23782db8740b.png)

Якщо у вас такий вигляд то все підключилося і правильно працює 

![Screenshot from 2022-07-17 21-35-04](https://user-images.githubusercontent.com/59205554/179420112-7eab3fb3-e3fb-4bc7-9afb-b3537a66414f.png)

# Налаштування Grafana

Відкриваємо google на своєму компютері. 

Водимо адрес через браузер з портом `3000`:
```
http://<IP_address>:3000
```
Ми побачимо наступне(Звичайно якщо все правильно встановилося)

![Screenshot from 2022-07-17 21-57-12](https://user-images.githubusercontent.com/59205554/179420753-30f0d6cb-8ce1-4e19-b8a1-936aaa8ae384.png)

Позамовчуванню user -> `admin`; password -> `admin`.
Далі ви вам запропонує встановити свій пароль, grafana запише його в базу даних і з наступного разу, щоб зайти на свою grafana потрібно буде вести ці дані.

Коли зайшли в графану натискаємо шистиграник -> add data source -> prometheus
![Screenshot from 2022-07-16 16-44-54](https://user-images.githubusercontent.com/102728347/179357543-57fea3cc-e144-47c3-878a-d5f11790accf.png)

У строку url вставляємо такий адрес http://prometheus:9090
Далі зберізаємо натиснувши знизу `save & test`

Якщо ти отримав зелуну галочку із назвою
`Data source is working`

# Настройка панелі HyperNode

Натискаємо + -> import

Ось що ми побачимо  
![Screenshot from 2022-07-16 17-04-14](https://user-images.githubusercontent.com/102728347/179358209-ecd023cf-1ca3-47f9-82b5-5c20b5ceb039.png)

Завантажуємо таблицю від розробників BlockPI
і вставляємо її в поле `Import via panel json`

* [HyperNode](https://github.com/cybernekit/-Guide-for-monitoring-blockPi-node/blob/main/HyperNode.json)

Самого початку у вас виникне помилка.

![Screenshot from 2022-07-17 09-50-00](https://user-images.githubusercontent.com/102728347/179387475-215260fb-1f6f-441e-bfa2-f1c2a9c138f4.png)

Але для того щоб все запрацювало потрібно зайти в `dashboard settings` -> `Variables`

![Screenshot from 2022-07-17 00-18-46](https://user-images.githubusercontent.com/102728347/179372230-96412459-f16d-4c85-bd81-8710e044e1c5.png)

Тепер нам потрібно зайти в вкожний із цих варіантів, виправити `Data source` на prometheus і зберегти `Update`

![Screenshot from 2022-07-17 00-23-24](https://user-images.githubusercontent.com/102728347/179372346-c3cde8c1-49b6-45d0-ac90-1a5e42a37f67.png)

Виходимо на основний екран і натискаємо `rpc Average throughput per minute` -> `edit` і `Number of work coroutines` -> `edit`

![Screenshot from 2022-07-17 10-04-02](https://user-images.githubusercontent.com/102728347/179387810-94a4ed85-b060-489a-85f1-742c4fce159e.png)

Також вибираєте prometheus і натискаєте `apply`

![Screenshot from 2022-07-17 10-02-01](https://user-images.githubusercontent.com/102728347/179387822-0beeee9f-17e5-4d4b-80d7-e374235e6d8f.png)


Терер щоб все запустит. Виходимо на основний екран де знаходяться dashboards і натискаємо `x` 
Вам запропонує зберегти dashboard. 
Зберігаємо його.

# Вітаю тепер ти можеш відсліковувати свої характеристики
### Вуаля.....

![Screenshot from 2022-07-17 10-12-09](https://user-images.githubusercontent.com/102728347/179388047-a371b0ad-9ed4-4654-8da5-79d88c20aee2.png)

