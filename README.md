# ASGBD-Practica2.2

## 1. Características de la VM y versiones instaladas de Oracle 23ai Free, PostgreSQL y MariaDB.
Características de la Maquina Virtual
- Sistema Operativo: Linux Mint
- RAM asignada: 4 GB
- CPU: 2
- Almacenamiento: 50 GB

Versiones de los SGBD
- Oracle Database Free:
- PostgreSQL:
- MariaDB: 

## 2. Pasos y comandos que realmente ejecutaste, explicados con tus palabras.
Instalación de Podman:
- Sudo apt update
- Sudo apt install podman

Descarga de la imagen:
- sudo podman pull container-registry.oracle.com/database/free:23.5.0.0

Creación del contenedor y volumen:
- sudo podman volume create oracle_23ai_datos
- sudo podman run -d --name cont-oracle -p 1521:1521 --restart=always -v oracle_23ai_datos:/opt/oracle/oradata:U containerregistry.oracle.com/database/free:23.5.0.0

Creación del usuario:
- sudo podman exec -it cont-oracle sqlplus / as sysdba

SQL
- ALTER SESSION SET CONTAINER = FREEPDB1;
- CREATE USER orauser IDENTIFIED BY "Contraseña";
- GRANT CREATE SESSION, CREATE TABLE TO orauser;
- ALTER USER orauser QUOTA 100M ON USERS;
- EXIT;

Instalación y servicio:
- sudo apt update
- sudo apt install postgresql postgresql-contrib php-pgsql
- sudo systemctl start postgresql
- sudo systemctl enable postgresql

Creación de base de datos y usuario:
- sudo -i -u postgres
- psql

SQL
- CREATE DATABASE dbpg;
- CREATE USER pguser WITH PASSWORD 'Contraseña';
- GRANT ALL PRIVILEGES ON DATABASE dbpg TO pguser;
- \q

Creación de base de datos y usuario:
- sudo mariadb

SQL
- CREATE DATABASE dbm CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
- CREATE USER 'muser'@'localhost' IDENTIFIED BY 'MiContraseñaReal';
- GRANT ALL PRIVILEGES ON dbm.* TO 'muser'@'localhost';
- FLUSH PRIVILEGES;
- EXIT;

## 3. Cómo comprobaste el estado de los tres gestores y la conexión con orauser, pguser y muser.
- Oracle (Puerto 1521): nc -zv localhost 1521
- PostgreSQL (Puerto 5432): nc -zv localhost 5432
- MariaDB (Puerto 3306): nc -zv localhost 3306

Comprobación de conexión con los clientes:
- Para saber que los usuarios tenían acceso, inicié sesión desde la terminal usando la maquina cliente

Conexión a Oracle:
- sudo podman exec -it cont-oracle sqlplus orauser@//localhost:1521/FREEPDB1

Conexión a PostgreSQL (usuario pguser):
- psql -h localhost -U pguser -d dbpg -W

Conexión a MariaDB (usuario muser):
- mariadb -u muser -p dbm

## 4. Configuración de red realizada, si habilitaste conexiones desde otra máquina.
Configuración de red

En PostgreSQL:
- Primero me metí a editar el archivo postgresql.conf y le cambié el listen_addresses para ponerle la IP de la máquina. Después abrí el archivo pg_hba.conf y le añadí una línea al final para darle paso a mi subred con el usuario pguser. Guardé los dos ficheros, reinice el servicio con systemctl para que aplicara los cambios y abrí el puerto 5432 en el cortafuegos UFW.

En MariaDB:
- Aquí busqué el archivo de configuración, que suele ser el 50-server.cnf, y modifiqué el bind-address para poner la IP de mi máquina. Entré a la consola de MariaDB y, cree otro usuario muser con la IP de la maquina cliente, dándole los permisos sobre la base de datos dbm. Reinicié el servicio de MariaDB y abrí el puerto 3306 en el UFW para esa IP.

## 5. Problemas encontrados, mensajes de error relevantes y cómo los resolviste; si no hubo incidencias, indícalo.
- No hubo incidencias

## 6. Fuentes consultadas y, si aportas capturas o salidas de comandos, una breve explicación de lo que demuestran.
- El documento de la practica y al documento de Oracle Database Free
