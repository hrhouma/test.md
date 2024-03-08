# Étape 1 - création des fichiers

```shell
touch docker-compose.yaml Dockerfile index.php Auth.php
chmod 777 docker-compose.yaml index.php Dockerfile index.php Auth.php
nano Dockerfile
nano docker-compose
nano index.php
nano Auth.php
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
$msg="";
    if(isset($_GET['reg']))
    {
     $link= mysqli_connect("db","root","MYSQL_ROOT_PASSWORD","employee");

        $qry="insert into emp_info values($_GET[txt_id],'$_GET[txt_name]','$_GET[txt_uname]','$_GET[txt_pwd]','$_GET[txt_email]',$_GET[txt_no])";

        mysqli_query($link,$qry);
        if(mysqli_affected_rows($link)>0)
        {
            $msg="<font color='green' size='5px'>Registration Done.....</font>";
        }
        else
        {
            $msg="<font color='red' size='5px'>Registration Fail.....</font>";
        }
        mysqli_close($link);
    }
?>
<!DOCTYPE html>
<html>
    <head>
        <meta charset="UTF-8">
        <title></title>
        <script>
            temp=0;
            temp1=0;
            function CheckEmail()
            {
                temp=0;
                email=document.getElementById('email').value;
                ht=new XMLHttpRequest();
                ht.open("get","Auth.php?id="+email,true);
                ht.send();
                ht.onreadystatechange=function() {
                    if(ht.readyState==4 && ht.status==200)
                    {
                        document.getElementById("d1").innerHTML=ht.responseText;
                         if(ht.responseText=="Email-Id Already Exist")
                        temp=1;
                        
                    }
                }
             
            }
            function CheckUsername()
            {
                temp1=0;
                uname=document.getElementById('uname').value;

                ht1=new XMLHttpRequest();
                ht1.open("get","Auth.php?id1="+uname,true);
                ht1.send();
                ht1.onreadystatechange=function() {
                    if(ht1.readyState==4 && ht1.status==200)
                    {
                        document.getElementById("d2").innerHTML=ht1.responseText;
                        if(document.getElementById("d2").innerHTML=="Username Already Exist")
                        {temp1=1; }
                        
                    }
                }

            }
            function validate()
            {
                if(temp==1)
               return false;
           if(temp1==1)
               return false;
           else 
               return true;
            }
        </script>
        <style>
            .mystyle{
                border-radius: 10px;
                padding-left: 5px;
                height: 25px;
                width:200px;
                font-size: 15px;
            }
            .style1{
                width:120px;
                height: 30px;
                border-radius: 10px;
                padding-left: 5px;
                font-family: Time In Roman;
                font-size: 20px;
            }
            label{
                font-family: Time In Roman;
                font-size: 20px;
            }
        </style>
    </head>
    <body>
        <hr size="4" color="green"/>
        <h2 style="font-size: 30px; color: maroon; font-family: Time In Roman; text-align: center"><br>Employee Registration Form<br></h2>
        <hr size="4" color="green"/><br>
        <div align="center"><form method="get" onsubmit="return validate()" >
                <table width="50%">
                <tr>
                    <td><label>Employee ID</label></td>
                    <td><input class="mystyle" type="text" name="txt_id" value="" required="" /></td>
                </tr>
                <tr>
                    <td><label>Employee Name</label></td>
                    <td><input class="mystyle" type="text" name="txt_name" value="" required="" /></td>
                </tr>
                <tr>
                    <td><label>Username</label></td>
                    <td><input class="mystyle" id="uname" type="text" name="txt_uname" value="" onchange="CheckUsername()" required="" /><br><div id="d2" style="color:red"></div></td>
                </tr>
                <tr>
                    <td><label>Password</label></td>
                    <td><input class="mystyle" type="password" name="txt_pwd" value="" required="" /></td>
                </tr>
                <tr>
                    <td><label>Email ID</label></td>
                    <td><input class="mystyle" id="email" type="email" name="txt_email" onchange="CheckEmail()" value="" required="" /><br><div id="d1" style="color:red"></div></td>
                </tr>
                <tr>
                    <td><label>Phone Number</label></td>
                    <td><input class="mystyle" type="text" name="txt_no" value="" required="" /></td>
                </tr>
                <tr>
                    <td></td>
                    <td><input class="style1" type="submit"  value="Register" name="reg"/></td>
                </tr>
                  <tr>
                      <td colspan="2"><?php echo $msg; ?></td>
                </tr>
            </table>
            </form></div>
    </body>
</html>

```

## Auth.php

```php
<?php
if(isset($_GET['id']))
{
       $link= mysqli_connect("db","root","MYSQL_ROOT_PASSWORD","employee");
        mysqli_query($link,"select * from emp_info where emp_email='$_GET[id]'");
        if(mysqli_affected_rows($link)>0)
        {
            echo "Email-Id Already Exist";
        }
        mysqli_close($link);
}
if(isset($_GET['id1']))
{
        $link= mysqli_connect("db","root","MYSQL_ROOT_PASSWORD","employee");
        mysqli_query($link,"select * from emp_info where emp_username='$_GET[id1]'");
        if(mysqli_affected_rows($link)>0)
        {
            echo "Username Already Exist";
        }
        mysqli_close($link);
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
mysql> DROP TABLE IF EXISTS `employee`;
mysql> show databases;
mysql> create database employee;
mysql> show databases;
mysql> use employee;
mysql> create table emp_info(emp_id int(11),emp_name varchar(50),emp_username varchar(50),emp_password varchar(50),emp_email varchar(50),emp_phone bigint(20)); 
mysql> show tables;
mysql> desc emp_info;
mysql> exit; (quitter mysql)
```
```shell
exit ; (quitter le conteneur bd)

```

# Étape 5 - Vérification de phpmyadmin
```shell
localhost:8080 ou IP:8080
username : root
password: MYSQL_ROOT_PASSWORD
```
 
# Étape 6 - Vérification du site web
```shell
localhost:8000 ou IP:8000
```

# Annexe# 1 pour mysql - commandes utiles

```sql
# apt-get install mysql-server 
# mysql
# mysql> CREATE USER 'eleve'@'localhost' IDENTIFIED BY 'eleve';
# mysql> GRANT ALL PRIVILEGES ON *.* TO 'eleve'@'localhost' WITH GRANT OPTION;
# mysql> ALTER USER 'root'@'localhost' IDENTIFIED WITH 'mysql_native_password' BY 'eleve';
# mysql> FLUSH PRIVILEGES; 
# mysql> \! Clear;
# mysql> DESCRIBE mysql.user; 
# mysql> SELECT User, Host FROM mysql.user;
# mysql> SELECT user, authentication_string, plugin, host FROM mysql.user;
# mysql> exit;
# mysql> FLUSH PRIVILEGES;
# mysql> exit
# mysql -u root –p (sans espace après le p)
UTILISEZ LE MOT DE PASSE ELEVE
```
# Annexe# 2 pour mariadb - commandes utiles

```sql
=====> VERSION MARIADB SUR AMAZON LINUX 2023 <===
#mysql
MariaDB [(none)]>CREATE USER 'eleve'@'localhost' IDENTIFIED BY 'eleve';
MariaDB [(none)]> GRANT ALL PRIVILEGES ON *.* TO 'eleve'@'localhost' WITH GRANT OPTION;
MariaDB [(none)]> ALTER USER 'root'@'localhost' IDENTIFIED BY 'eleve';
MariaDB [(none)]> FLUSH PRIVILEGES;
MariaDB [(none)]> DESCRIBE mysql.user;
MariaDB [(none)]> SELECT User, Host FROM mysql.user;
MariaDB [(none)]> SELECT user, authentication_string, plugin, host FROM mysql.user;
MariaDB [(none)]> FLUSH PRIVILEGES;
MariaDB [(none)]> show databases;
MariaDB [(none)]> create database employee;
MariaDB [(none)]> use employee;
MariaDB [(employee)]> create table emp_info(emp_id int(11),emp_name varchar(50),emp_username varchar(50),emp_password varchar(50),emp_email varchar(50),emp_phone bigint(20));
MariaDB [(employee)]> show tables;
MariaDB [(none)]> exit
#mysql -u root –p (mot de passe eleve)
MariaDB [(none)]> show databases;
MariaDB [(none)]> use employee;
MariaDB [(employee)]> show tables;
MariaDB [(none)]> select * from emp_info;
```
