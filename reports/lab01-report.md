# Отчёт по лабораторной работе №1
## Настройка локального окружения разработчика

**Студент:** Жуков Дмитрий Сергеевич
**Группа:** 23-ИСбо-1
**Дата:** 18.09.2026

---

## 1. Информация о системе

### 1.1. Версия ОС
```
No LSB modules are available.
Distributor ID: Ubuntu
Description:    Ubuntu 26.04.1 LTS
Release:        26.04
Codename:       resolute
```

### 1.2. Версия ядра Linux
```
7.0.0-31-generic
```

## 2. Установленные инструменты

| Инструмент | Версия |
|---|---|
| Git | 2.53.0 |
| Docker | 29.8.1 |
| Docker Compose | v5.5.1 |
| Make | 4.4.1 |
| curl | 8.18.0 |
| jq | 1.8.1 |
| kubectl | v1.37.0 |
| Helm | v3.22.0+g144ca65 |

## 3. Настройка Git

### 3.1. Конфигурация Git
```
user.name=Дмитрий Жуков
user.email=bodyabadguy@gmail.com
init.defaultbranch=main
core.autocrlf=input
core.editor=nano
pull.rebase=true
fetch.prune=true
```

## 4. SSH-ключ

### 4.1. Проверка подключения к GitLab
```
Welcome to GitLab, @DmitriyLOL1!
```

### 4.2. Отпечаток ключа
```
256 SHA256:W2Yb/f6PdhQEnZVWXnd8njDZSoE88d74LLwstDDyuAY ваш.email@example.com (ED25519)
```

## 5. Проверка Docker

### 5.1. Запуск hello-world
```
Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 https://hub.docker.com/

For more examples and ideas, visit:
 https://docs.docker.com/get-started/
```

## 6. Выводы
```
В ходе лабораторной работы было настроено локальное окружение разработчика: установлены WSL2 с Ubuntu 26.04, все необходимые CLI-инструменты, 
настроен Git и SSH-аутентификация. При подключении к GitLab возникла ошибка Permission denied (publickey), которая была решена корректной
установкой прав на файлы ключей, повторным запуском ssh-agent и добавлением ключа через ssh-add. После устранения проблемы все инструменты успешно 
проверены, а отчёт закоммитен и запушен в репозиторий на GitLab.

1.Чем WSL2 отличается от WSL1? Почему для нашего курса важен именно WSL2?
WSL1 отличается от WSL2 тем, что WSL1 — это слой трансляции системных вызовов Linux в вызовы Windows (без настоящего ядра Linux), 
а WSL2 — это полноценное ядро Linux, работающее в лёгкой виртуальной машине. Для нашего курса важен именно WSL2, потому что 
он даёт полную совместимость с Linux-окружением: поддерживает systemd, корректно работает с Docker, Kubernetes и другими инструментами, 
которые мы будем использовать.

2.Что хранится в файле `~/.ssh/id_ed25519` и что в `~/.ssh/id_ed25519.pub`? Почему приватный ключ нельзя никому показывать?
В файле ~/.ssh/id_ed25519 хранится приватный ключ, а в ~/.ssh/id_ed25519.pub — публичный.
Приватный ключ нельзя показывать потому, что любой, кто его получит, сможет выдать себя за вас и получить доступ ко всем системам, 
где добавлен соответствующий публичный ключ.

3.Зачем нужна команда `ssh-agent`? Что произойдёт, если её не запускать?
ssh-agent нужен для того, чтобы хранить расшифрованные приватные ключи в памяти на время сессии и не вводить парольную фразу при каждом подключении 
к GitLab. Если его не запускать, то при каждом git push или git pull по SSH придётся заново вводить парольную фразу.

4.В чём разница между `git config --global` и `git config --local`?
git config --global отличается от git config --local тем, что --global применяет настройки ко всем репозиториям текущего пользователя, 
а --local — только к текущему репозиторию.

5.Почему мы используем `docker compose` (через плагин), а не отдельный `docker-compose`?
docker compose (через плагин) отличается от отдельного docker-compose тем, что плагин устанавливается вместе с Docker Engine и управляется через 
docker-compose-plugin, а отдельный docker-compose — это устаревший Python-скрипт, который больше не развивается.

6.Что означает флаг `--rm` в команде `docker run --rm hello-world`?
Флаг --rm означает, что контейнер будет автоматически удалён после его остановки. В случае hello-world контейнер одноразовый: вывел приветствие и 
завершился. Без --rm он остался бы в системе как остановленный контейнер и занимал бы место.

7.Зачем мы добавляем пользователя в группу `docker`? Какие риски это несёт?
Пользователя добавляют в группу docker для того, чтобы можно было запускать команды docker без sudo. Это нужно потому, что Docker-daemon работает от 
имени root, и по умолчанию только root имеет доступ к сокету /var/run/docker.sock. Риск в том, что членство в группе docker фактически эквивалентно 
полному root-доступу к хосту: пользователь может смонтировать корневую файловую систему в контейнер и изменить любые системные файлы.

8.Что такое `IdentitiesOnly yes` в `~/.ssh/config` и зачем он нужен?
IdentitiesOnly yes — это опция, которая говорит SSH-клиенту использовать только те ключи, которые явно указаны в IdentityFile для данного хоста, 
и не перебирать все остальные ключи в ~/.ssh. Она нужна для того, чтобы ускорить подключение и предотвратить ошибку Too many authentication failures, 
когда сервер блокирует соединение из-за слишком большого числа попыток аутентификации.
EOF
```

