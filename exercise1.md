# managing a mysql container
> podman run --name mysql rhscl/mysql-57-rhel7
> podman run --name mysql -d -e MYSQL_USER=user1 -e MYSQL_PASSWORD=mypa55 -e MYSQL_DATABASE=items -e MYSQL_ROOT_PASSWORD=r00tpa55 rhscl/mysql-57-rhel7
> podman logs mysql
> podman ps
> podman inspect -f '{{ .NetworkSettings.IPAddress }}' mysql
> mysql -uuser1 -h<IP> -pmypa55 items
  > CREATE TABLE Projects (id int(11) NOT NULL, name varchar(255) DEFAULT NULL, code varchar(255) DEFAULT NULL, PRIMARY KEY (id));
  > insert into Projects (id, name, code) values (1,'DevOps','DO180');
> podman ps -a --format="table {{.ID}} {{.Names}} {{.Status}}"
> podman exec mysql /bin/bash -c 'mysql -uuser1 -p -e "select * from items.Projects;"'

# Attaching Persistent Storage to Containers
## Preparing the Host Directory
> sudo mkdir -pv /var/local/mysql
> sudo chown -Rv 27:27 /var/local/mysql
> sudo semanage fcontext -a -t container_file_t '/var/local/mysql(/.*)?'
> sudo restorecon -Rv /var/local/mysql
> ls -dZ /var/dbfiles
## Mounting a Volume
> sudo podman pull rhscl/mysql-57-rhel7
> sudo podman run --name persist-db -d -v /var/local/mysql:/var/lib/mysql/data -e MYSQL_USER=user1 -e MYSQL_PASSWORD=mypa55 -e MYSQL_DATABASE=items -e MYSQL_ROOT_PASSWORD=r00tpa55 rhscl/mysql-57-rhel7
> sudo podman ps --format="table {{.ID}} {{.Names}} {{.Status}}"
> ls -l /var/local/mysql

# mapping metwork ports
> sudo podman run -d --name apache1 -p 8080:80 rhscl/httpd-24-rhel7:2.4
> sudo podman run -d --name apache2 -p 127.0.0.1:8081:80 rhscl/httpd-24-rhel7:2.4
> sudo podman run -d --name apache3 -p 127.0.0.1::80 rhscl/httpd-24-rhel7:2.4
> sudo podman port apache3
> sudo podman run -d --name apache4 -p 80 rhscl/httpd-24-rhel7:2.4
> sudo podman port apache4

# loading database
> sudo podman run --name mysqldb-port -d -v /var/local/mysql:/var/lib/mysql/data -p 13306:3306 -e MYSQL_USER=user1 -e MYSQL_PASSWORD=mypa55 -e MYSQL_DATABASE=items -e MYSQL_ROOT_PASSWORD=r00tpa55 rhscl/mysql-57-rhel7
> mysql -uuser1 -h 127.0.0.1 -pmypa55 -P13306 items < /home/student/DO180/labs/manage-networking/db.sql
> mysql -uuser1 -h 127.0.0.1 -pmypa55 -P13306 items -e "SELECT * FROM Item"
> sudo podman exec -it mysqldb-port /opt/rh/rh-mysql57/root/usr/bin/mysql -uroot items -e "SELECT * FROM Item"

# Configuring Registries in Podman
/etc/containers/registries.conf 

[registries.search]
registries = ["registry.access.redhat.com", "quay.io"]
[registries.insecure]
registries = ['localhost:5000']

# Registry Authentication
> sudo podman login -u username -p password registry.access.redhat.com
> access token: curl -u username:password -Ls "https://sso.redhat.com/auth/realms/rhcc/protocol/redhat-docker-v2/auth?service=docker-registry"
> curl -H "Authorization: Bearer <token>" -Ls https://registry.redhat.io/v2/rhscl/mysql-57-rhel7/tags/list | python -mjson.tool

> podman search [OPTIONS] <term>
> podman pull quay.io/bitnami/nginx
> podman images
> podman pull rhscl/mysql-57-rhel7:5.7
> podman run rhscl/mysql-57-rhel7:5.7
> podman save -o mysql.tar registry.access.redhat.com/rhscl/mysql-57-rhel7:5.7
> podman load -i mysql.tar
> podman rmi IMAGE / podman rmi -a
> podman commit [OPTIONS] CONTAINER [REPOSITORY[:PORT]/]IMAGE_NAME[:TAG]
> podman diff mysql-basic
> podman commit mysql-basic mysql-custom
> podman tag [OPTIONS] IMAGE[:TAG] [REGISTRYHOST/][USERNAME/]NAME[:TAG]
> podman tag mysql-custom devops/mysql
> podman tag mysql-custom devops/mysql:snapshot
> podman rmi devops/mysql:snapshot
> podman push quay.io/bitnami/nginx

# Creating a Custom Apache Container Image
> podman login quay.io
> podman run -d --name official-httpd -p 8180:80 redhattraining/httpd-parent
> podman exec -it official-httpd /bin/bash
	> echo "DO180 Page" > /var/www/html/do180.html
> podman inspect -f "{{range .Mounts}}{{println .Destination}}{{end}}" official-httpd
> podman diff official-httpd
> podman stop official-httpd
> podman commit -a 'Your Name' official-httpd do180-custom-httpd
> podman images
> podman tag do180-custom-httpd quay.io/${RHT_OCP4_QUAY_USER}/do180-custom-httpd:v1.0
> podman push quay.io/${RHT_OCP4_QUAY_USER}/do180-custom-httpd:v1.0
> podman pull -q quay.io/${RHT_OCP4_QUAY_USER}/do180-custom-httpd:v1.0
> podman run -d --name test-httpd -p 8280:80 ${RHT_OCP4_QUAY_USER}/do180-custom-httpd:v1.0
> curl http://localhost:8280/do180.html

# manage image
> podman search docker.io/nginx -f is-official=true
> podman pull docker.io/nginx:1.17
> podman run --name official-nginx -d -p 8080:80 docker.io/nginx:1.17
> podman exec -it official-nginx /bin/bash
> echo 'DO180' > /usr/share/nginx/html/index.html
> podman stop official-nginx
> podman commit -a 'Your Name' official-nginx do180/mynginx:v1.0-SNAPSHOT
> podman run --name official-nginx-dev -d -p 8080:80 do180/mynginx:v1.0-SNAPSHOT
> podman exec -it official-nginx-dev /bin/bash
> echo 'DO180 Page' > /usr/share/nginx/html/index.html
> podman stop official-nginx-dev
> podman commit -a 'Your Name' official-nginx-dev do180/mynginx:v1.0
> podman rm official-nginx-dev
> podman ps -a --format="{{.ID}} {{.Names}} {{.Status}}"
> podman rmi do180/mynginx:v1.0-SNAPSHOT
> podman run -d --name my-nginx -p 8280:80 do180/mynginx:v1.0
> curl 127.0.0.1:8280
> podman build -t NAME:TAG DIR

# Creating a Basic Apache Container Image
> Dockerfile
FROM ubi7/ubi:7.7
MAINTAINER Your Name <youremail>
LABEL description="A basic Apache container on RHEL 7 UBI"
RUN yum install -y httpd && \
    yum clean all
RUN echo "Hello from Dockerfile" > /usr/share/httpd/noindex/index.html
EXPOSE 80
ENTRYPOINT  ["httpd", "-D", "FOREGROUND"]

> podman build --layers=false -t do180/apache .
> podman run --name lab-apache -d -p 10080:80 do180/apache
> curl 127.0.0.1:10080

> Custom Dockerfile
FROM ubi7/ubi:7.7
MAINTAINER Your Name <youremail>
ENV PORT 8080
RUN yum install -y httpd && \
    yum clean all
RUN sed -ri -e "/^Listen 80/c\Listen ${PORT}" /etc/httpd/conf/httpd.conf && \
chown -R apache:apache /etc/httpd/logs/ && \
chown -R apache:apache /run/httpd/
USER apache
EXPOSE ${PORT}
COPY ./src/ /var/www/html/
CMD ["httpd", "-D", "FOREGROUND"]

> podman build -t do180/custom-apache .
> podman run -d --name dockerfile -p 20080:8080 do180/custom-apache
> curl 127.0.0.1:20080

# Kubernetes Resource Types
Pods (po)
Services (svc)
Replication Controllers (rc)
Persistent Volumes (pv)
Persistent Volume Claims (pvc)
ConfigMaps (cm) and Secrets

# OpenShift Resource Types
Deployment config (dc)
Build config (bc)
Routes

# Discovering Services
1. automatically defined and injected into containers for all pods inside the same project
SVC_NAME_SERVICE_HOST is the service IP address.
SVC_NAME_SERVICE_PORT is the service TCP port.
2. Each service is dynamically assigned an SRV record with an FQDN of the form:
SVC_NAME.PROJECT_NAME.svc.cluster.local

# oc command
> oc port-forward mysql-openshift-1-glqrp 3306:3306
> oc new-app mysql MYSQL_USER=user MYSQL_PASSWORD=pass MYSQL_DATABASE=testdb -l db=mysql
> oc new-app --docker-image=myregistry.com/mycompany/myapp --name=myapp
> oc new-app https://github.com/openshift/ruby-hello-world --name=ruby-hello
> oc get RESOURCE_TYPE 
> oc get all
> oc describe RESOURCE_TYPE RESOURCE_NAME
