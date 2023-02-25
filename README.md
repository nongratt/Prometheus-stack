# Prometheus-stack

 - [ZERO TO HERO](#Сборка-приложения-в-DOCKER-с-помощью-Jenkins-pipline)
   - [What](#what)
   - [Linux](#linux-bashzsh)
     - [Docker (with bash)](#Docker)
     - [Prometheus (with bash)](#Prometheus-NodeExporter)
     - [Grafana (with bash)](#Grafana)
     - [AlertManager (with bash)](#Alertmanager)
     - [Blackbox (with bash)](#BlackBox)
   - [So, what is next ?](#So-,-what-is-next-?)
   - [Все о Docker](#Все-о-Docker)
     - [Основы Docker](#Теория-Docker)
     - [Архитектура](#Основые-компоненты)
     - [Dockerfile](#Операторы-файла-докер)
   - [Заключение](#Заключение)

## What

Приложение springboot-hello было фокрнуто из https://github.com/VanAbsenter/springboot-hello.git, для обучения работы с процессами CI/CD.

В данном разделе привиден пример составления pipeline Jenkins for push docker images in docker.hub.



## Linux bash/zsh

### Docker

```
Установка репозитория

sudo apt-get update #Обновляем состав устанолвенных пакетов 

sudo apt-get install \
    ca-certificates \
    curl \
    gnupg \
    lsb-release
sudo mkdir -m 0755 -p /etc/apt/keyrings

curl -fsSL https://download.docker.com/linux/debian/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg  #Ключ для работы с официальным репозиторием Docker  (дергаем из ранее созданной папки по пути  /etc/apt/keyrings)

 echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/debian \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null     #Настройка репозитория

Установка DOCKER

sudo apt-get update

sudo chmod a+r /etc/apt/keyrings/docker.gpg

sudo apt-get update

sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin -y  #Установка всего необходимо из ранее добавленного репозитория докер

sudo systemctl start docker #Запуск

sudo systemctl enable docker #Добавление в загрузчик 

```
### Jenkins

```
iptables -I INPUT -p tcp --dport 8080 -j ACCEPT     #Доступность портов 80 с помощью firewall iptables если его нет, sudo apt-get install iptables

apt install iptables-persistent

netfilter-persistent save

apt install default-jdk

update-alternatives --config java

apt-get install gnupg2   #Выполняем только если получаем ошибка о том что модуль не найден 

vi /etc/apt/sources.list.d/jenkins.list
deb https://pkg.jenkins.io/debian-stable binary/ #Первую строчку открываем, вторую добавляем это репозиторий 

wget -q -O - https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo apt-key add -    #Импорт ключей для доступа к репозиторию

apt install ca-certificates #Выполняем только если получаем ошибку

apt-get update

systemctl enable jenkins #Разрешаем автозапуск сервиса 

Открываем браузер и переходим по адресу http://<IP-адреса сервера Jenkins>:8080 — откроется окно «Unlock Jenkins». В нем будет путь до файла, в котором нужно взять парольную фразу для разблокировки портала:

cat /var/lib/jenkins/secrets/initialAdminPassword   полученный результат вбиваем в строчку веб морды

Выбираем левый пункт настройки плагинов после установки создаем Вашего первого пользователя пользователя

После первого входа наш Jenkins необходимо настроить, начнем с плагинов, для нашей задачи мы установим все необходимое.
После того как мы вошли в нашу учетную запись, с левой стороны выбирает "Настроить Jenkins------> Управление плагинами------->Available plugins
В поиске находим следующее:
Docker API Plugin
Docker Commons Plugin
Docker Pipeline
Docker plugin
docker-build-step
Git client plugin
Git plugin
GitHub
GitHub API Plugin
Pipeline: GitHub Groovy Libraries
GitHub Branch Source Plugin

Далее мы регистрируемся на https://hub.docker.com/signup

После чего переходим в настройки аккаунта в раздел security и создаем Access Tokens

Как только ТОКЕН будет создан возвращаемся в Настроить Jenkins------>Credentials------->и создаем новый кред где мы будем использовать ТОКЕН

После перейти в Jenkins------>Node------>Создайте node вы можете увидеть его в моем pipeline в разделе "agent" 

```

### Git

```
sudo yum install git -y
git --version
git config --global user.name "TESTINVS"  #Базовая информация об фиксации изминений в репе
git config --global user.email "test@invs.ru" #Базовая информация об фиксации изминений в репе
git config --list
```

## So, what is next ?

После произведенных установок пришло время узнать, что такое Docker и Jenkins.

## Все о Docker

### Теория Docker

Docker — программное обеспечение для автоматизации развёртывания и управления приложениями
в средах с поддержкой контейнеризации, контейнеризатор приложений.
Операционная система: Linux, Microsoft Windows, macOS и Unix-подобная операционная система

### Основные компоненты


![info](https://github.com/nongratt/springboot-hello/blob/main/images/1-1.png)

Docker host (докер-хост) - Это компьютер, на котором работает докер. Это может быть обычный персональный компьютер, железный сервер или виртуальная машина в облаке.

Docker daemon (докер-демон) - Это фоновый процесс, который работает постоянно и ожидает команды. Все операции по управлению контейнерами отправляются именно в демон, например: запустить или остановить контейнер, скачать образ.

Docker client (докер-клиент) - Это клиент, при помощи которого пользователи взаимодействуют с демоном и отправляют ему команды. Это может быть консоль, API или графический интерфейс.

Docker image (докер-образ) - Это неизменяемый образ, из которого разворачивается контейнер. Его можно рассматривать как набор файлов, необходимых для запуска и работы приложения на другом хосте.

Docker container (докер-контейнер) - Это уже развернутое и запущенное приложение. Продолжая аналогию с установкой ПО, контейнер мо

Docker registry - Это репозиторий, в котором хранятся образы. Когда разработчики создают приложения, они размещают свои образы в этих репозиториях, откуда их могут скачать другие люди. Есть публичные репозитории, например Docker Hub. А можно создать свой репозиторий, для использования внутри компании или команды.

Dockerfile (докер-файл) - Это файл-инструкция для сборки образа. 
docker pull. Эта команда скачивает образ из docker registry и помещает его в локальное хранилище на хосте.
docker build. Эта команда читает докер-файл и собирает образ.
docker run. Эта команда создает из образа контейнер и запускает его.


### Операторы файла докер

FROM — задаёт родительский (главный) образ;

LABEL — добавляет метаданные для образа. Хорошее место для размещения информации об авторе;

ENV — создаёт переменную окружения;

RUN — запускает команды, создаёт слой образа. Используется для установки пакетов и библиотек внутри контейнера;

COPY  — копирует файлы и директории в контейнер;

ADD  — делает всё то же, что и инструкция COPY. Но ещё может распаковывать локальные .tar файлы;

CMD — указывает команду и аргументы для выполнения внутри контейнера. Параметры могут быть переопределены. Использоваться может только одна инструкция CMD;

WORKDIR — устанавливает рабочую директорию для инструкции CMD и ENTRYPOINT;

ARG — определяет переменную для передачи Docker’у во время сборки;

ENTRYPOINT — предоставляет команды и аргументы для выполняющегося контейнера. Суть его несколько отличается от CMD, о чём мы поговорим ниже;

EXPOSE — открывает порт;

VOLUME — создаёт точку подключения директории для добавления и хранения постоянных данных.

## Все о Jenkins

### Теория Jenkins


Jenkins — программная система с открытым исходным кодом на Java, предназначенная для обеспечения процесса непрерывной интеграции программного обеспечения

### Основные компоненты

![jenkins](https://github.com/nongratt/springboot-hello/blob/main/images/maxresdefault.jpg)

В нашем примере будет построен конвейер аналогичный скриншоту GIT----->JENKINS PIPELINE----->BUILD IMAGES----->PUSH DOCKER.HUB

## CI CD
В разработке ПО, CI/CD или CICD — это комбинация непрерывной интеграции и непрерывного развертывания программного обеспечения в процессе разработки. CI/CD объединяет разработку, тестирования и развёртывания приложения. В настоящий момент DevOps-программисты стремятся применять CI/CD практически для всех задач.

### Dockerfile
```
#Этап 1

FROM maven:3-openjdk-18-slim AS build  #Обращз приложения maven 

COPY src/ /home/app/src # Копирование директории 
COPY pom.xml /home/app  # Копирование кода
WORKDIR /home/app/ # Создание рабочей директории

RUN mvn package # Обычная команда shell

#Этап 2 ( это сборка multistage) позволяет собирать образ из жвух оператов FROM Вы можете выборочно копировать артефакты с одного этапа на другой, оставляя только то, что вам необходимо в конечном образе

FROM eclipse-temurin:latest

EXPOSE 9000

COPY --from=build /home/app/target/springboot-hello-world.jar /data/springboot-hello-world.jar

CMD ["java","-jar","/data/springboot-hello-world.jar"]

В нашем репозитории создаем файл Dockerfile (Он должен быть один) если у вас имеется еще один такой файл он должен находиться в другой папке или переименовам 
по дефолту запуск сборка производится командой docker build . 
если файл назван по другому docker build -f <название файла> . 
```
### Pipeline
```
Сборка производится обычным выполнением этапов ( в данному случае это stages)

pipeline{

	agent { node {label "docker-node"}} 

	environment {
		DOCKERHUB_CREDENTIALS=credentials('docker-hub-test8686')
		DOCKER_IMAGE = "springboot-hello/app_test"
                DOCKER_TAG = "latest"    # Переменное окружение в данному случае креды 
	}

	stages {
	    

		stage('Build') {

			steps {
				sh 'docker build -t test8686/app_test:latest .'  #Этап сборки как писалось ранее выполняется обычнм shell скриптом docker build -t test8686/app_test:latest .
			}
		}

		stage('Login') {

			steps {
				sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'  # Этап логина на докер.хаб 
			}
		}

		stage('Push') {

			steps {
				sh 'docker push test8686/app_test:latest'  #Пуш образа в наш репозиторий 
			}
		}
	}

	post {
		always {
			sh 'docker logout'
		}
	}

}

Копируем данный код и создаем файл у себя в репозитории Jenkinsfile и заполняем его.

```
## Заключение
Сборка была завершена, как мы видим состояла из 4 этапов как они и прописаны в Pipeline

![qwe](https://github.com/nongratt/springboot-hello/blob/main/images/%D0%B9%D1%86%D1%83%D0%BA2.PNG)

Во время сборки произошел удачный push в нашу репозиторию
![qw1e](https://github.com/nongratt/springboot-hello/blob/main/images/%D0%BF%D1%80%D0%B8%D0%BC%D0%B5%D1%80.PNG)
