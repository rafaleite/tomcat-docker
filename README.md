# Tomcat Docker

```bash
docker run -it -d  -p 8080:8080 -p 8443:8443 -p 8000:8000 \
 -v /path/to/arquivos/:/root/arquivos/ \
 -v /path/to/logs/:/usr/local/tomcat/logs/ \
 rafaleite/tomcat-docker
```

## Deploy

```bash
docker cp /path/to/projeto.war <container_name>:/usr/local/tomcat/webapps/projeto.war
```

## Logs

```bash
docker logs -f <container_name>
```

## HTTPS - SSL
Este container Tomcat suporta SSL. Para obter o certificado `openssl`:

```bash
openssl req -new -newkey rsa:4096 -days 3650 -nodes -x509 -subj \
    "/C=BR/ST=Bahia/L=Salvador/O=Rleite/CN=tomcat.example.com" -keyout \
    ./ssl.key -out ./ssl.crt
```

Altere o arquivo `server.xml` existente e adicione o certificado SSL e a chave privada com:

```xml
<Connector port="8443"
       maxThreads="150"
       enableLookups="false"
       disableUploadTimeout="true"
       acceptCount="100"
       scheme="https"
       secure="true"
       SSLEnabled="true"
       SSLCertificateFile="${catalina.base}/conf/ssl.crt"
       SSLCertificateKeyFile="${catalina.base}/conf/ssl.key" />
```

```bash
docker run -it -d  -p 8080:8080 -p 8443:8443 -p 8000:8000 \
    -v /path/to/server.xml:/usr/local/tomcat/conf/server.xml \
    -v /path/to/ssl.crt:/usr/local/tomcat/conf/ssl.crt \
    -v /path/to/ssl.key:/usr/local/tomcat/conf/ssl.key \
    rafaleite/tomcat-docker
```

ou utilizando `docker-compose.yml`:

```yaml
mytomcat:
  image: rafaleite/tomcat
  ports:
    - "8080:8080"
    - "443:8443"
    - "8000:8000"
  volumes:
    - /path/to/ssl.crt:/usr/local/tomcat/conf/ssl.crt
    - /path/to/ssl.key:/usr/local/tomcat/conf/ssl.key
    - /path/to/server.xml:/usr/local/tomcat/conf/server.xml
    - /path/to/arquivos/:/root/arquivos/
    - /path/to/logs/:/usr/local/tomcat/logs/
```

Para iniciar o container:

```bash
docker-compose up
```
