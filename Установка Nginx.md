## Установка с помощью менеджера пакетов

1. [[Debian]]
```bash
sudo apt-get install nginx
```
2. [[Linux]] (на основе [[rpm]])
```bash
sudo yum install nginx
```
3. [[FreeBSD]]
```bash
sudo pkg_install -r nginx
```

Описанные команды устанавливают Nginx в стандартные местя, зависящие от ОС. Эти способы являются предпочтительными для установки.

---
## Установка через двоичные файлы 

Команда Nginx предусмотрела ткже двоичные файлы стабильной версии, если в Дистрибутиве отсутствует пакет Nginx, к примеру, как [[CentOS]] 

Пример установки через двоичные файлы:
1. CentOS:
```bash
sudo vi /etc/yum.repos.d/nginx.repo

[nginx]
name=nginx repo
baseurl=http://nginx.org/packges/cenos/6/$basearch/
gpgcheck=0
enabled=1
```
	затем, для установки нужно просто выполнить команду
```bash
sudo yum install nginx
```

2. Debian
	Нужно скачать ключ подписания Nginx - [Ссылка](http://nginx.org/keys/nginx_signing.key)
	И добавить его в связку ключей [apt]:
```bash
sudo apt-key add nginx_signing.key
```
	Потом нужно добавить репозиторий nginx.org в конец файлы `/etc/apt/sources.list`
```bash
vi /etc/apt/sources.list
deb http://nginx.org/packages/debian/ squeeze nginx
deb-src http://nginx.org/packages/debian/ squeeze nginx
```
	И установить nginx
```bash
sudo apt-update 
sudo apt-get install nginx
```

---

![[Установка дополнительных модулей Nginx]]