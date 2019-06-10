### MySql
```
apt-get install mysql-server
/etc/init.d/mysql start|stop|restart
/etc/mysql/
/var/lib/mysql

mysql -u root -p - přihlášení do databáze pod uživatele root
mysql -u root -p -h 192.168.0.100 -P 3309 - přihlášení z jiného stroje na 192.168.0.100:3309

~/.my.cnf - obsahuje defaultní přihlašovací údaje, které jsou použity pokud je příkaz mysql použit bez parametrů
	[client]
	user=user
	password=pass

/etc/mysql/my.cnf
bind-adress = 10.0.0.1 #adresa na které mysql poslouchá
[mysqld]
port = 1234    #pro port pod 1024 musí být ještě user = root
[client]
port = 1234

mysqldump 
mysqladmin
rsync -rav /var/lib/mysql/ /srv/zaloha-mysql/

mysql -uroot -pheslo databaze
SHOW DATABASES;
SHOW TABLES;
USE databaze;
DESC tabulka;
SHOW CREATE TABLE tabulka\G

CREATE USER 'db01'@'localhost' IDENTIFIED BY 'password';
CREATE DATABASE db01 DEFAULT CHARACTER SET utf8  DEFAULT COLLATE utf8_general_ci;
GRANT ALL PRIVILEGES ON db01.* to 'db01'@'localhost' IDENTIFIED BY 'password';
CREATE TABLE table01(
   id INT(6) UNSIGNED AUTO_INCREMENT PRIMARY KEY,
   firstname VARCHAR(30) NOT NULL,
   lastname VARCHAR(30) NOT NULL,
   email VARCHAR(50),
   reg_date TIMESTAMP
)

<?php
$servername = "localhost";
$username = "username";
$password = "password";
$dbname = "myDB";

// Create connection
$conn = new mysqli($servername, $username, $password, $dbname);
// Check connection
if ($conn->connect_error) {
    die("Connection failed: " . $conn->connect_error);
} 

$sql = "SELECT id, firstname, lastname FROM MyGuests";
$result = $conn->query($sql);

if ($result->num_rows > 0) {
    // output data of each row
    while($row = $result->fetch_assoc()) {
        echo "id: " . $row["id"]. " - Name: " . $row["firstname"]. " " . $row["lastname"]. "<br>";
    }
} else {
    echo "0 results";
}
$conn->close();
?>

```





### PostgreSQL
```
apt-get install postgresql-9.4
su - postgres
/etc/postgresql/9.4/main/
/var/lib/postgresql/9.4/main/
pg_hba.conf

cat ~/.pgpass
localhost:5432:db01:db01:password

pg_hba.conf:
local   all         postgres                         peer
local   all         all                              md5
host    all         all             127.0.0.1/32     md5
host    all         all             ::1/128          md5


psql -U root databaze
\l
\c databaze
\dt 
\d tabulka
\x on | \x off

pg_dump
pg_restore

createuser
createdb

CREATE USER db01 WITH PASSWORD 'pasword';
CREATE DATABASE db01 WITH OWNER=db01;
GRANT ALL PRIVILEGES ON DATABASE db01 to db01;
CREATE TABLE table01(
   id SERIAL PRIMARY KEY,
   firstname VARCHAR(30) NOT NULL,
   lastname VARCHAR(30) NOT NULL,
   email VARCHAR(50),
   reg_date TIMESTAMP
);

<?php
// Connecting, selecting database
$dbconn = pg_connect("host=localhost dbname=publishing user=www password=foo")
    or die('Could not connect: ' . pg_last_error());

// Performing SQL query
$query = 'SELECT * FROM authors';
$result = pg_query($query) or die('Query failed: ' . pg_last_error());

// Printing results in HTML
echo "<table>\n";
while ($line = pg_fetch_array($result, null, PGSQL_ASSOC)) {
    echo "\t<tr>\n";
    foreach ($line as $col_value) {
        echo "\t\t<td>$col_value</td>\n";
    }
    echo "\t</tr>\n";
}
echo "</table>\n";

// Free resultset
pg_free_result($result);

// Closing connection
pg_close($dbconn);
?>

bash:
for i in `seq 1 50`; do 
   echo "INSERT INTO table01 (firstname, lastname, email, reg_date) VALUES ('$(pwgen 5 1)','$(pwgen 10 1)','$(pwgen 5 1)@spos-jindra.spos',now())" | mysql db01
done

```