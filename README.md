# Apache

apt install apache2

Дефолтный сайт лежит в /var/www/html/index.html, нам нужно создать папку в этом же месте (или другом)
например cd /var/www/html/site1/index.html
и внести изменения в index.html и загрузить необходимые файлы для работы сайта в эту папку site1

Далее нужно создать новый файл конфигурации, он должен лежать там же где и дефолтный /etc/apache2/sites-available/000-default.conf
Допутим такой /etc/apache2/sites-available/site1.conf

    ServerName petrovich.ddnsking.com - днс имя
    ServerAdmin webmaster@localhost - без изменений
    DocumentRoot /var/www/html/site1 - путь до сайта

a2ensite site1.conf - включаем виртуальный хост
systemctl reload apache2 - перегружаем Apache

Так можно поместить много сайтов на один IP под разными DNS именами

                            ЛОГ ФАЙЛ

awk '{print $1}' /var/log/apache2/access.log - найти все ip адреса с лог файла
awk '{print $1}' /var/log/apache2/access.log | sort -u - уникальные обращения или
awk '{print $1}' /var/log/apache2/access.log | sort | uniq - тоже самое

awk '{print $1}' /var/log/apache2/access.log | sort | uniq -c | sort -n - самый активный ip адрес

whois 34.76.153.113 - инфа о ip адресе

                                    ROUND ROBIN

1.  Создать в Апаче 3 сайта (3 виртуальных хоста) на 3х разных портах и активируем их "a2ensite название" и пергружаем сервер Апачи
    например такой
    <VirtualHost \*:8080> - это порт меняем в каждом файле конфигурации виртуального хоста

            ServerAdmin webmaster@localhost - адрес почты сисадмина
            DocumentRoot /var/www/8080 - папка местоположения сайта


            ErrorLog ${APACHE_LOG_DIR}/error.log
            CustomLog ${APACHE_LOG_DIR}/access.log combined

</VirtualHost>

2.  Прописать эти порты в /etc/apache2/ports.conf
    Listen 8080
    Listen 8081
    Listen 8082

    <IfModule ssl_module>
            Listen 443
    </IfModule>

    <IfModule mod_gnutls.c>
            Listen 443
    </IfModule>

3.  В Nginx создаем в папке /etc/nginx/sites-available файл с названием upstream с содеожимым как ниже:
    upstream apache { - назавание сервера любое, но оно потом указывается и далее!
    server 212.192.31.43:8080;
    server 212.192.31.43:8081;
    server 212.192.31.43:8082;
    }

4.  Редактируем дефолтный файл сайта Nginx /etc/nginx/sites-available/default добавив строчки
    server {
    listen 80 default_server;
    listen [::]:80 default_server;

        root /var/www/html;

        server_name _;

        location / {
                # First attempt to serve request as file, then
                # as directory, then fall back to displaying a 404.
                try_files $uri $uri/ =404;
                proxy_pass http://apache; - вот этф строка, назавание сервера (apache) строго как впункте 4.
        }

5.  nginx -t - проверка не накосячили ли
6.  systemctl reload nginx - перегружаем Nginx
