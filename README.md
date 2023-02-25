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

```

### Grafana

```

```
### Alertmanager

```

```
### BlackBox

```

```

## Заключение
