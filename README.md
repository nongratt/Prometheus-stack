# Prometheus-stack

 - [Building monitoring](#Сборка-приложения-в-DOCKER-с-помощью-Jenkins-pipline)
   - [What](#what)
   - [Все о Prometheus](#Все-о-Prometheus)
     - [Основы Prometheus](#Теория-Prometheus)
     - [Архитектура](#Основые-компоненты)
   - [So, what is next ?](#So-,-what-is-next-?)
   - [Linux](#linux-bashzsh)
     - [Docker (with bash)](#Docker)
     - [Prometheus (with bash)](#Prometheus-NodeExporter)
     - [Grafana (with bash)](#Grafana)
     - [AlertManager (with bash)](#Alertmanager)
     - [Blackbox (with bash)](#BlackBox)
   - [Заключение](#Заключение)

## What

Данный раздел преднозначем для изучения системы мониторинга Prometheus.
Разберем зачем он нужен, как его настраивать и получать с него интересующую нас информацию.

## Все о Prometheus


### Теория Prometheus

Prometheus — это бесплатное программное приложение, используемое для мониторинга и оповещения о событиях. Он записывает метрики в реальном времени в базу данных временных рядов, созданную с использованием модели извлечения HTTP, с гибкими запросами и оповещениями в реальном времени

### Основные компоненты



Сервер, который считывает метрики и сохраняет их в темпоральной (time series) базе данных;

Клиентские библиотеки для различных языков программирования (Go, Java, Python, Ruby; сообществом также созданы библиотеки для Bash, Node.js, Haskell, .NET/C#);

Pushgateway — компонент для приёма метрик кратковременных процессов;

PROMDASH — дашборд для метрик;

Инструменты для экспорта данных из сторонних приложений (Statsd, Ganglia, HAProxy и других);

Менеджер уведомлений AlertManager (на текущий момент находится на стадии бета-тестирования);

Клиент командной строки для выполнения запросов к данным.



![123](https://github.com/nongratt/Prometheus-stack/blob/main/%D0%A1%D0%BD%D0%B8%D0%BC%D0%BE%D0%BA123.PNG)

## So, what is next ?

## Linux bash/zsh

Установки производятся на ОС Debian 10

### Docker

```
Установка репозитория

apt-get update #Обновляем состав устанолвенных пакетов 

apt-get install \
    ca-certificates \
    curl \
    gnupg \
    lsb-release
mkdir -m 0755 -p /etc/apt/keyrings

curl -fsSL https://download.docker.com/linux/debian/gpg | gpg --dearmor -o /etc/apt/keyrings/docker.gpg  #Ключ для работы с официальным репозиторием Docker  (дергаем из ранее созданной папки по пути  /etc/apt/keyrings)

 echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/debian \
  $(lsb_release -cs) stable" | tee /etc/apt/sources.list.d/docker.list > /dev/null     #Настройка репозитория

Установка DOCKER

apt-get update

chmod a+r /etc/apt/keyrings/docker.gpg

apt-get update

apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin  #Установка всего необходимо из ранее добавленного репозитория докер

systemctl start docker #Запуск

systemctl enable docker #Добавление в загрузчик 

Cнизу бесплатная утилита для мини оркестрации наших контейнеров

https://docs.portainer.io/start/install/server/docker/linux

https://www.portainer.io/take-5


```
![1342](https://github.com/nongratt/Prometheus-stack/blob/main/%D0%A1%D0%BD%D0%B8%D0%BC%D0%BE%D0%BA4343.PNG)

### Prometheus NodeExporter

```
mkdir -p /opt/prometheus_stack/{prometheus,grafana,alertmanager,blackbox}  # Создание рабочей директории

touch /opt/prometheus_stack/docker-compose.yml  #Создание композ файла в одной папке может быть лишь один композ файл

vi docker-compose.yml

version: '3.9'

services:

  prometheus:
    image: prom/prometheus:latest
    volumes:
      - ./prometheus:/etc/prometheus/
    container_name: prometheus
    hostname: prometheus
    command:
      - --config.file=/etc/prometheus/prometheus.yml
    ports:
      - 9090:9090
    restart: unless-stopped
    environment:
      TZ: "Europe/Moscow"
    networks:
      - default

  node-exporter:
    image: prom/node-exporter
    volumes:
      - /proc:/host/proc:ro
      - /sys:/host/sys:ro
      - /:/rootfs:ro
    container_name: exporter
    hostname: exporter
    command:
      - --path.procfs=/host/proc
      - --path.sysfs=/host/sys
      - --collector.filesystem.ignored-mount-points
      - ^/(sys|proc|dev|host|etc|rootfs/var/lib/docker/containers|rootfs/var/lib/docker/overlay2|rootfs/run/docker/netns|rootfs/var/lib/docker/aufs)($$|/)
    ports:
      - 9100:9100
    restart: unless-stopped
    environment:
      TZ: "Europe/Moscow"
    networks:
      - default


vi prometheus/prometheus.yml

scrape_configs:
  - job_name: node
    scrape_interval: 5s
    static_configs:
    - targets: ['node-exporter:9100']


docker-compose up -d
Ждем несколько секунд и можно пробовать подключиться. Открываем браузер и переходим по адресу http://<IP-адрес сервера>:9090 — мы должны увидеть страницу Prometheus:
Переходим по адресу http://<IP-адрес сервера>:9100 — мы должны увидеть страницу Node Exporter:

```

### Grafana

```
Дозаполняем наш файл vi docker-compose.yml синтаксис аналогичен коду выше

grafana:
    image: grafana/grafana
    user: root
    depends_on:
      - prometheus
    ports:
      - 3000:3000
    volumes:
      - ./grafana:/var/lib/grafana
      - ./grafana/provisioning/:/etc/grafana/provisioning/
    container_name: grafana
    hostname: grafana
    restart: unless-stopped
    environment:
      TZ: "Europe/Moscow"
    networks:
      - default

```
### Alertmanager

```
Перед написаем конфига для AlertManager в телеграмм найдите @my_id_bot уточните свой id и  @BotFather создайте бота и получите его API TOKEN 
После чего добавляем в наш файл выше доп конфигурацию синтаксис аналогичный


 alertmanager-bot:       #телеграм бот для prometheus alertmanager
    command:
      - --alertmanager.url=http://alertmanager:9093
      - --log.level=info
      - --store=bolt
      - --bolt.path=/data/bot.db
      - --telegram.admin=Ваш чат айди
      - --telegram.token=Ваш токен бота
    image: metalmatze/alertmanager-bot:0.4.3
    user: root
    ports:
      - 8080:8080
    container_name: alertmanager-bot
    hostname: alertmanager-bot
    environment:
      TZ: "Europe/Moscow"
    restart: unless-stopped
    volumes:
      - ./data:/data
    networks:
      - default

  alertmanager:         #истема оповещений в prometheus
    image: prom/alertmanager:v0.21.0
    user: root
    ports:
      - 127.0.0.1:9093:9093
    volumes:
      - ./alertmanager/:/etc/alertmanager/
    container_name: alertmanager
    hostname: alertmanager
    environment:
      TZ: "Europe/Moscow"
    restart: unless-stopped
    command:
      - '--config.file=/etc/alertmanager/config.yml'
      - '--storage.path=/etc/alertmanager/data'
    networks:
      - default
      
      Далее в конфигурационном файле vi prometheus/prometheus.yml добавляем 

   rule_files:
  - 'alert.rules'

alerting:
  alertmanagers:
  - scheme: http
    static_configs:
    - targets:
      - "alertmanager:9093"
(В данном примере мы указали, что наш сервер мониторинга должен использовать в качестве системы оповещения alertmanager, который доступен по адресу alertmanager:9093. Также мы добавили файл alert.rules с описанием правил оповещения)

vi prometheus/alert.rules создаем файл с правилами оповещения

groups: 
- name: test
  rules:
  - alert: PrometheusTargetMissing
    expr: up == 0
    for: 0m
    labels:
      severity: critical
    annotations:
      summary: "Prometheus target missing (instance {{ $labels.instance }})"
      description: "A Prometheus target has disappeared. An exporter might be crashed. VALUE = {{ $value }}  LABELS: {{ $labels }}"

(В данном примере мы добавили правило, которое будет срабатывать при недоступности узла (node-exporter)

vi alertmanager/config.yml

route:
    receiver: 'alertmanager-bot'

receivers:
- name: 'alertmanager-bot'
  webhook_configs:
  - send_resolved: true
    url: 'http://alertmanager-bot:8080'

(Данная конфигурация позволяет отправлять оповещения с помощью webhook на сервис alertmanager-bot:8080 (наш телеграм бот для alertmanager)

docker compose up -d перезапускаем (данная команда вводится только из папки где лежит docker-compose.yml)

```
### BlackBox

```

```

## Заключение
