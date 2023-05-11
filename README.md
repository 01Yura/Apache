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
