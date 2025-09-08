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
    echo '<h1>Project 1 - Page2</h1>' > /usr/local/var/www/project1/html/api.html
    echo '<h1>Project 2 - Hello again!</h1>' > /usr/local/var/www/project2/html/page1.html
    echo '<h1>Project 2 - Page2</h1>' > /usr/local/var/www/project2/html/admin.html
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
4) пишем конфигурации для [проекта 1](projects/project1/nginx.conf) и [проекта 2](projects/project2/nginx.conf)

5) настраиваем основной конфиг:
   ```
   cat > /opt/homebrew/etc/nginx/nginx.conf << 'EOF'
   worker_processes  1;
   
   events {
       worker_connections  1024;
   }
   
   http {
       include       mime.types;
       default_type  application/octet-stream;
       
       sendfile        on;
       keepalive_timeout  65;
       
       # Подключаем виртуальные хосты 
       include servers/*;
   }
   EOF
   ```

### Проверка

1) проверяем редирект http -> https:
   
   <img width="439" height="118" alt="Снимок экрана 2025-09-08 в 15 24 13" src="https://github.com/user-attachments/assets/4800e674-fb08-415d-9911-f8a136f98543" />

2) проверка https с самоподписными сертификатами:
   
    <img width="439" height="39" alt="Снимок экрана 2025-09-08 в 15 25 10" src="https://github.com/user-attachments/assets/487cc778-fdfd-4f44-bb0e-e136119efdf5" />

3) проверяем работу alias
   
   <img width="467" height="39" alt="Снимок экрана 2025-09-08 в 15 29 41" src="https://github.com/user-attachments/assets/c1311f41-a94c-43cc-a33d-2b97c3d953c9" />

   

   <img width="489" height="39" alt="Снимок экрана 2025-09-08 в 15 31 20" src="https://github.com/user-attachments/assets/01bff251-4d29-419b-b633-60a51ce5f699" />

4) проверка виртуальных хостов
   
   <img width="489" height="64" alt="Снимок экрана 2025-09-08 в 15 33 19" src="https://github.com/user-attachments/assets/9b98ac1f-3ef8-4766-a446-77eaa397b65d" />


## Вывод
 в результате выполнения лабораторной я познакомилась с абсолютно новым для меня отвлетлением в программировании и изучила основы ngix, честно говоря по началу было сложно разобраться что да как, но вроде бы все получилось


