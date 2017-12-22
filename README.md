# Un Vagrantfile para Ruby on Rails (RoR)
Este `Vagrantfile` crea una máquina virtual (MV) para VirtualBox (VB) instalando

- Ruby Versión Manager (RVM)
- La versión de Ruby estable 2.3.1
- Docker Community Edition (Docker CE )
- PostgreSQL a través de la imagen oficial de PostgresSQL en Docker Hub.

La idea es realizar la edición en local con un editor a la carta y ejecutar en
la MV. La MV comparte el directorio de trabajo de la máquina anfitrión `/path/to/directory` con el directorio de la MV `/vagrant`.

Todo empieza con obtener el fichero `Vagrantfile`
```
$ git clone http://github.com/carmelocuenca/rails_projects
$ cd rails_machine
```

Continúa con levantar la máquina y provisionarla

```
$ vagrant up --debug # Sé paciente...
```

# ¿Y para trabajar?

1. Establecer la redirección de puertos desde la máquina anfitrión a la MV para poder utilizar el navegador desde la máquina anfitrión.
```
$ vagrant ssh -- -L 3000:localhost:3000
vagrant@...:~$
```

2. En la MV crea un *gemset* para la aplicación para instalar *RoR*
```
vagrant@...:~$ rvm gemset use ruby-2.3.1@first_app --create
...
vagrant@...:~$ gem install rails --no-ri --no-rdoc # Sé paciente...
...
```

3. Crear la aplicación en el directorio compartido con la máquina anfitrión.
En este ejemplo la aplicación se llama `first_app`
```
vagrant@...:~$ rvm gemset use 2.3.1@first
vagrant@...:~$ cd /vagrant
vagrant@...:~$  rails new first_app --skip-turbolinks --skip-spring \
  --skip-test -d postgresql
vagrant@...:~$ cd first_app
```

4. PostreSQL fue instalado con usurio "postgres' y password "password".
Una opción es cambiar el fichero de configuración de base de datos `config/database.yml` para la configuación
de *development* y *test*. Cuidado con las indentaciones del fichero `.yml`
```
host: localhost
username: postgres
password: password
```
O bien crear el usuario con la contraseña apropiada. Por ejemplo, para un super usuario "skywalker" y contraseña "RDD2"
```
vagrant@...:~$ sudo docker run -it --rm -e PGPASSWORD="password" --link some-postgres:postgres postgres:9.6 psql -h postgres -U postgres -c "create user skywalker with superuser password 'RDD2';"
```
5. Compureba la aplicación
lanzando el servidor *RoR* en la MV y accede desde la máquina anfitrión
```
vagrant@...:~$ rails db:create
vagrant@...:~$ rails server
$  curl http://localhost:3000
```
