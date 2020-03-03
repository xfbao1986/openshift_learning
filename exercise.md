# creating a mysql db instance
> podman run --name mysql-basic -e MYSQL_USER=user1 -e MYSQL_PASSWORD=mypa55 -e MYSQL_DATABASE=items -e MYSQL_ROOT_PASSWORD=r00tpa55 -d rhscl/mysql-57-rhel7:5.7-3.14
> podman ps --format "{{.ID}} {{.Image}} {{.Names}}"
> podman exec -it mysql-basic /bin/bash
  > mysql -uroot
  > show databases;
  > use items;
  > CREATE TABLE Projects (id int(11) NOT NULL, name varchar(255) DEFAULT NULL, code varchar(255) DEFAULT NULL, PRIMARY KEY (id));
  > insert into Projects (id, name, code) values (1,'DevOps','DO180');
  > select * from Projects;

# Creating Containerized Services
> podman run -d -p 8080:80 --name httpd-basic redhattraining/httpd-parent:2.4
> curl http://localhost:8080
> podman exec -it httpd-basic /bin/bash
  > echo "Hello World" > /var/www/html/index.html
> curl http://localhost:8080
