# Étape 1 - création des fichiers

```shell
touch docker-compose.yaml index.php Dockerfile
chmod 777 docker-compose.yaml index.php Dockerfile
nano Dockerfile
nano docker-compose
nano index.php
```

# Étape 2 - copier le code à partir d'ici

## Dockerfile
```shell
FROM php:8.0-apache
RUN docker-php-ext-install mysqli && docker-php-ext-enable mysqli
```

## docker-compose

```shell
version: '3.8'

services:
  php-apache-environment:
    container_name: php-apache
    build:
      context: .
      dockerfile: Dockerfile
    depends_on:
      - db
    volumes:
      - .:/var/www/html/
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
 
## index.php

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

# Étape 3 - construction des conteneurs

```shell
docker-compose up -d
docker ps 
```
# Étape 4 - ajout des utilisateurs dans la base de données et SCRIPTS DE LA BASE DE DONNÉES

```shell
docker ps
docker exec -it bd bash
mysql -u root -p
le mot de passe est MYSQL_ROOT_PASSWORD dans docker-compose.yaml
```
```sql
mysql> show databases;
mysql> use MYSQL_DATABASE;
mysql> show tables;
mysql> DROP TABLE IF EXISTS `users`;
mysql>  CREATE TABLE `users` (
  id INT NOT NULL AUTO_INCREMENT,
  username TEXT NOT NULL,
  password TEXT NOT NULL,
  PRIMARY KEY (id)
);

mysql>  INSERT INTO `users` (username, password) VALUES
  ('admin', 'password'),
  ('Alice', 'this is my password'),
  ('Job', '12345678');
mysql> exit;
```



 
