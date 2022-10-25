При установке nginx средствами OC [[Linux]] нет возможности скофнигурировать его установку, чтоыдобавить или убрать каки-либо модули.

Если необходимо добавить модуль, нужно пересобрать вручную nginx

```bash 
nginx -V
```
>nginx version: nginx/1.12.1 
>built by gcc 4.8.5 20150623 (Red Hat 4.8.5-11) (GCC)
> built with OpenSSL 1.0.1e-fips 11 Feb 2013 TLS SNI support enabled configure arguments: --prefix=/etc/nginx --conf-path=/etc/nginx/nginx.conf --error-log-path=/var/log/nginx/error.log
> --http-client-body-temp-path=/var/lib/nginx/body --http-fastcgi-temp-path=/var/lib/nginx/fastcgi --http-log-path=/var/log/nginx/access.log --http-proxy-temp-path=/var/lib/nginx/proxy --lock-path=/var/lock/nginx.lock --pid-path=/var/run/nginx.pid --with-pcre-jit --with-http_gzip_static_module --with-http_ssl_module --with-ipv6 --without-http_browser_module --without-http_geo_module --without-http_limit_req_module --without-http_limit_zone_module --without-http_memcached_module --without-http_referer_module --without-http_scgi_module --without-http_split_clients_module --with-http_stub_status_module --without-http_ssi_module --without-http_userid_module --without-http_uwsgi_module --add-module=/tmp/buildd/nginx-1.12.0/debian/modules/nginx-echo

Сохраним выод команды `nginx -V`, тюк эта информация пригодится при конфигурировании

---

Видим, что nginx у нас версии 1.12.1
Значит нам нужно скачать ту же самую версию исходников с сайта:
```bash
wget http://nginx.org/download/nginx-1.12.1.tar.gz
```

Распакуем архив и перейдем в папку nginx-1.12.1:
```bash 
tar -xvf nginx-1.12.1.tar.gz
cd nginx-1.12.1
```
Далее, потребуется установить предустановленный набор модулей.
```bash 
bash <(curl -f -L -sS https://ngxpagespeed.com/install) \ --nginx-version $nginxversion
```
Сохраняем все модули:
```bash
bash <(curl -f -L -sS https://ngxpagespeed.com/install) -m
```
После установки пакетов приступаем к конфигурированию nginx с добавлением модулей.

Для этого копируем из предыдущего нашего сохраненного вывода `nginx -V` начиная
с *--prefix=* и до первого *--add-module=*

После чего пишем в консоли `./configure` и вставляем скоприуемое из редактора
```bash
./configure --prefix=/etc/nginx 
--conf-path=/etc/nginx/nginx.conf 
--error-log-path=/var/log/nginx/error.log 
--http-client-body-temp-path=/var/lib/nginx/body 
--http-fastcgi-temp-path=/var/lib/nginx/fastcgi 
--http-log-path=/var/log/nginx/access.log 
--http-proxy-temp-path=/var/lib/nginx/proxy 
--lock-path=/var/lock/nginx.lock 
--pid-path=/var/run/nginx.pid 
--with-pcre-jit 
--with-http_gzip_static_module --with-http_ssl_module --with-ipv6 --without-http_browser_module --with-http_geoip_module --without-http_memcached_module --without-http_referer_module --without-http_scgi_module --without-http_split_clients_module --with-http_stub_status_module --without-http_ssi_module --without-http_userid_module --without-http_uwsgi_module
```

В конец добавляем наши скаченные модули **--add-dynamic-module=/root/incubator-pagespeed-ngx-latest-stable**

Нажимем ENTER и ждем выполнения

Теперь можно собрать бинарник nginx - выполняем две команды:
```bash
make
make install 
```

# Проверка 
По окончании сборки проверяем, что nginx собрался с нужными модулями
```bash
/etc/nginx/sbin/nginx -V
```

Как видим, **--add-dynamic-module=/root/incubator-pagespeed-ngx-latest-stable** в выводе команды присутствует. Осталось заменить текущий бинарник nginx новым, который мы только что собрали.

Останавливаем nginx
```bash
service nginx stop
```
Делаем бэкап текущего nginx
```bash
mv /usr/sbin/nginx /usr/sbin/nginx_back
```
Перемещаем на его место новый собранный бинарник:
```bash
mv /etc/nginx/sbin/nginx /usr/sbin/nginx
```
Удаляем ненужную папку
```bash 
rm -r -f /etc/nginx/sbin
```
И проверяем еще раз получилось ли у нас все
```bash
nginx -V
```
И запускаем nginx:
```bash
service nginx start
```
Удаляем ненужный архив:
```bash
cd ../
rm -r -f nginx-1.12.1
```

