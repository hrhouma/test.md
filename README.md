# test.md

```shell
touch docker-compose index.php Dockerfile
```

# Dockerfile
```shell
FROM php:8.0-apache
RUN docker-php-ext-install mysqli && docker-php-ext-enable mysqli
```

# docker-compose

```shell
version: '3.8'

services:
  php-apache-environment:
    container_name: php-apache
    build:
      context: ./php
      dockerfile: Dockerfile
    depends_on:
      - db
    volumes:
      - ./php/src:/var/www/html/
    ports:
      - 8000:80

  db:
    container_name: db
    image: mysql
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: MYSQL_ROOT_PASSWORD
      MYSQL_DATABASE: MYSQL_DATABASE
      MYSQL_USER: MYSQL_USER
      MYSQL_PASSWORD: MYSQL_PASSWORD
    ports:
      - "9906:3306"


  phpmyadmin:
    image: phpmyadmin/phpmyadmin
    ports:
      - '8080:80'
    restart: always
    environment:
      PMA_HOST: db
    depends_on:
      - db
```
 
# index.php

```php
<?php
//These are the defined authentication environment in the db service

// The MySQL service named in the docker-compose.yml
$host = "db";

// Database user name
$user = 'MYSQL_USER';

// Database user password
$pass = 'MYSQL_PASSWORD';

// Database name
$mydatabase = 'MYSQL_DATABASE';

// Check the mysql connection status
$conn = new mysqli($host, $user, $pass, $mydatabase);

// Select query
$sql = 'SELECT * FROM users';

if ($result = $conn->query($sql)) {
    while ($data = $result->fetch_object()) {
        $users[] = $data;
    }
}

foreach ($users as $user) {
    echo "<br>";
    echo $user->username . " " . $user->password;
    echo "<br>";
}
?>
```

# SCRIPT DE LA BASE DE DONNÉES

DROP TABLE IF EXISTS `users`;
```sql
CREATE TABLE `users` (
  id INT NOT NULL AUTO_INCREMENT,
  username TEXT NOT NULL,
  password TEXT NOT NULL,
  PRIMARY KEY (id)
);

INSERT INTO `users` (username, password) VALUES
  ('admin', 'password'),
  ('Alice', 'this is my password'),
  ('Job', '12345678');
 
```



 
