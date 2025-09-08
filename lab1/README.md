# Лабораторная №1. Отчет

## ТЗ:
1. Должен работать по https c сертификатом
2. Настроить принудительное перенаправление HTTP-запросов (порт 80) на HTTPS (порт 443) для обеспечения безопасного соединения.
3. Использовать alias для создания псевдонимов путей к файлам или каталогам на сервере.
4. Настроить виртуальные хосты для обслуживания нескольких доменных имен на одном сервере.

## Ход работы:

### Подготовка:
0) изучаем что такое вообще ngix, что, о чем и как
1) устанавливаем Ngix и запускаем
   ```
     brew install nginx
   ```

   ```
     brew services start nginx
   ```

2) создаем 2 директории для наших проектов
   
   ```
   mkdir -p /usr/local/var/www/project1/{html,logs}
   mkdir -p /usr/local/var/www/project2/{html,logs}
   ```

3) создаем html файлы для 2х проектов
   ```
    echo '<h1>Project 1 - Hello World!</h1>' > /usr/local/var/www/project1/html/page1.html
    echo '<h1>Project 1 - Page2</h1>' > /usr/local/var/www/project1/html/page2.html
    echo '<h1>Project 2 - Hello again!</h1>' > /usr/local/var/www/project2/html/page1.html
    echo '<h1>Project 2 - Page2</h1>' > /usr/local/var/www/project2/html/page2.html
   ```

### Основная часть:

1) для выполнения первого пункта `Должен работать по https c сертификатом` нам нужно создать самоподписной сертификат.
   создаю директории для них и сами сертификаты с помощью этого кода:
   ```
    mkdir -p /usr/local/etc/letsencrypt/live/domain1.com
    mkdir -p /usr/local/etc/letsencrypt/live/domain2.com


    openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
        -keyout /usr/local/etc/letsencrypt/live/domain1.com/privkey.pem \
        -out /usr/local/etc/letsencrypt/live/domain1.com/fullchain.pem \
        -subj "/CN=domain1.com"

    openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
        -keyout /usr/local/etc/letsencrypt/live/domain2.com/privkey.pem \
        -out /usr/local/etc/letsencrypt/live/domain2.com/fullchain.pem \
        -subj "/CN=domain2.com"
   ```

   Так как сертификат подписан не доверенным центром сертификации, то браузер выдает предупреждение, что является нормой для самоподписного сертификата:
   <img width="680" height="487" alt="v" src="https://github.com/user-attachments/assets/de612d3f-230b-4a86-bc58-90117e5da78a" />

2) создаем директорию для конфигураций ngix:
   ```
   mkdir -p /opt/homebrew/etc/nginx/servers
   ```
4) пишем конфигурации для [проекта 1](project1/nginx.conf) и [проекта 2](project2/nginx.conf)
    
