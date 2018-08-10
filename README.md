# EHE Health History micro service

## Run EHE Health History micro service
```bash
$ java -jar -DDB_PORT=6603 -DDB_NAME="container_epms" dist/health_history-1.0.jar
```

## 1. Docker container
### Links
[Dockerize REST Spring-boot application with Hibernate having database as MySQL](https://medium.com/@itsromiljain/dockerize-rest-spring-boot-application-with-hibernate-having-database-as-mysql-579abcc4edc4).
[Using Docker containers for your Spring boot applications](https://g00glen00b.be/docker-spring-boot/).

### `pom.xml`
```xml
<!-- ... -->

<build>
    <plugins>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
            <configuration>
                <outputDirectory>dist</outputDirectory>
                <jvmArguments>
                    -Xdebug -Xrunjdwp:transport=dt_socket,server=y,address=5005
                </jvmArguments>
            </configuration>
        </plugin>
    </plugins>
</build>

<!-- ... -->
```

### `Dockerfile`
```
FROM java:8
VOLUME /tmp
EXPOSE 8090
ADD /dist/health_history-1.0.jar health_history-1.0.jar
ENTRYPOINT ["java","-Djava.security.egd=file:/dev/./urandom","-Dspring.profiles.active=container","-jar","health_history-1.0.jar"]
```

### `application.yml`
[Spring Boot externalization](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html).
[common properties](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#common-application-properties);

```yml
server:
  port: ${PORT:8090}

management:
  port: ${PORT:8190}

spring.jpa.properties.hibernate.dialect: org.hibernate.dialect.MySQL5Dialect

spring:
  datasource:
    url: jdbc:mysql://${DB_HOST:localhost}:${DB_PORT:3306}/${DB_NAME:local_epms_dev}?useSSL=false
    username: ${DB_USERNAME:epmsuser}
    password: ${DB_PASSWORD:epmsmysql}

  jpa:
    show_sql: true
    hibernate:
      ddl-auto: update

  ## Ignore null fields from REST JSON response
  jackson:
    default-property-inclusion: NON_NULL

## Disable flyway in local dev
flyway:
  enabled: false

---
# Docker Container
spring:
  profiles: container
  jpa:
    show_sql: false
    hibernate:
      ddl-auto: none
flyway:
  enabled: true
```

### Docker commands

#### 1. Build and run locally.
```bash
$ docker network create mynetwork
$ docker run -d -p 6603:3306 --network mynetwork --name=docker-mysql --env="MYSQL_ROOT_PASSWORD=root" --env="MYSQL_PASSWORD=root" --env="MYSQL_DATABASE=test" mysql:5.6
$ docker ps -a

$ docker images
$ docker exec -it docker-mysql bash

$ docker build -t hh-service .
$ docker run -t --name hh-service -p 9090:8090 --network mynetwork --link docker-mysql:mysql -e DB_HOST=docker-mysql hh-service

$ cd ~/Documents/workspaces/HealthHistory/
$ docker build -f Dockerfile.docker-mysql -t hh-mysql .
$ docker run -d -p 6603:3306 --name=hh-mysql hh-mysql

$ mvn clean package
$ docker build -f Dockerfile -t hh-service .
$ docker run -t --name hh-service -p 9090:8090 -p 9190:8190 --link hh-mysql:hh-mysql -e DB_HOST=hh-mysql -e DB_NAME=epms hh-service

$ curl -X GET \
    'http://localhost:9090/v1/emr/sections/?gender=A&context=9001&contextId=9001&contextDate=2018-03-21'
```

#### 2. Create tarball packages.
```bash
$ docker push hh-mysql
$ docker save hh-mysql | gzip > ~/Documents/temp/hh-mysql.tar.gz
$ docker save hh-service | gzip > ~/Documents/temp/hh-service.tar.gz

$ docker load < hh-mysql.tar.gz
$ docker load < hh-service.tar.gz
$ docker images

### You should see "hh-mysql:latest" and "hh-service:latest"

$ docker run -d -p 6603:3306 --name=hh-mysql hh-mysql
$ docker ps -a

$ docker run -t --name hh-service -p 9090:8090 -p 9190:8190 --link hh-mysql:hh-mysql -e DB_HOST=hh-mysql -e DB_NAME=epms hh-service
$ docker ps -a

### Now you can access the emr API at http://localhost:9090/v1/emr

$ curl -X GET \
    'http://localhost:9090/v1/emr/sections/?gender=A&context=9001&contextId=9001&contextDate=2018-03-21'

```

## 2. Original `application.properties`
```

server.port=8090

## Spring DATASOURCE (DataSourceAutoConfiguration & DataSourceProperties)
spring.datasource.url = jdbc:mysql://localhost:3306/local_epms_dev?useSSL=false
spring.datasource.username = epmsuser
spring.datasource.password = epmsmysql

# QA
#spring.datasource.url = jdbc:mysql://qadbsrv01:3308/billing_emr?useSSL=false
#spring.datasource.url = jdbc:mysql://192.168.10.193:3308/billing_emr_hh?useSSL=false
#spring.datasource.username = dev
#spring.datasource.password = dev@user

## Hibernate Properties
# The SQL dialect makes Hibernate generate better SQL for the chosen database
spring.jpa.properties.hibernate.dialect = org.hibernate.dialect.MySQL5Dialect

# Hibernate ddl auto (create, create-drop, validate, update)
spring.jpa.hibernate.ddl-auto = update

## Ignore null fields from REST JSON response
spring.jackson.default-property-inclusion: NON_NULL

# DB Trace
spring.jpa.properties.hibernate.show_sql=true
spring.jpa.properties.hibernate.use_sql_comments=true
spring.jpa.properties.hibernate.format_sql=true

#spring.jpa.properties.hibernate.type=trace

```

## 3. Swagger
[Tutorial](https://www.vojtechruzicka.com/documenting-spring-boot-rest-api-swagger-springfox/)

### Swagger endpoints:
* [swagger-ui](http://localhost:8090/swagger-ui.html)
* [swagger-resources](http://localhost:8090/swagger-resources?context=9001)
* [api-docs](http://localhost:8090/v2/api-docs?context=9001)
* [security configuration](http://localhost:8090/swagger-resources/configuration/security?context=9001)
* [ui configuration](http://localhost:8090/swagger-resources/configuration/ui?context=9001)


### `pom.xml`
```xml
<dependencies>
    <!-- https://mvnrepository.com/artifact/io.springfox/springfox-swagger2 -->
    <!-- https://github.com/springfox/springfox -->
    <dependency>
        <groupId>io.springfox</groupId>
        <artifactId>springfox-swagger2</artifactId>
        <version>2.9.2</version>
        <scope>compile</scope>
    </dependency>

    <!-- https://mvnrepository.com/artifact/io.springfox/springfox-swagger-ui -->
    <dependency>
        <groupId>io.springfox</groupId>
        <artifactId>springfox-swagger-ui</artifactId>
        <version>2.9.2</version>
        <scope>compile</scope>
    </dependency>

</dependencies>
```

### Configurations:
* __SwaggerConfig__ (com.ehe.healthhistory.rest.configs.SwaggerConfig)
* __FilterConfig__ (com.ehe.healthhistory.rest.configs.FilterConfig)

   The filter to only apply to certain URL patterns ("/v1/emr/*").
   The @Component annotation from the filter class definition (`ParamValidatorFilter`)  
   and register the filter using a FilterRegistrationBean.

### [Swagger to pdf](https://www.npmjs.com/package/swagger-spec-to-pdf)
* get the swagger.json from [http://localhost:8090/v2/api-docs](http://localhost:8090/v2/api-docs)
* the _swagger2pdf_ will open the Swagger Editor and convert it to pdf.
* The Swagger Editor is available at [http://localhost:19849](http://localhost:19849). Hide the editor pane on the left, then print/save as pdf.

```bash
$ npm install -g swagger-spec-to-pdf

$ swagger2pdf -v
$ swagger2pdf -s ./temp/api-docs.json -o ./temp/
```
   
## 4. Spring Boot actuators
[Security settings](https://docs.spring.io/spring-boot/docs/1.2.0.M1/reference/html/production-ready-monitoring.html)

### Add the spring boot actuator dependency in `pom.xml`:
```xml
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-actuator</artifactId>
    </dependency>
</dependencies>
```

### Disabled authentication for the spring boot management in `application.yml`
```yaml
management:
  port: ${PORT_MANAGEMENT:8190}
  security:
    enabled: false
```
Access it at [http://localhost:8190/mappings](http://localhost:8190/mappings).

### Securing spring boot management
Add security dependency in `pom.xml`:
```xml
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-security</artifactId>
    </dependency>
</dependencies>
```

Add the admin credential in `application.yml`:
```yaml
security:
  user:
    name: admin
    password: hello#

management:
  port: ${PORT_MANAGEMENT:8190}
  security:
#    enabled: false
    role: SUPERUSER
```
Access it basic authentication at   
[http://admin:hello%23@localhost:8190/mappings](http://admin:hello%23@localhost:8190/mappings).

### Web Management endpoints:
ID | Description | Sensitive Default
--- | --- | ---
`actuator` | Provides a hypermedia-based “discovery page” for the other endpoints. Requires Spring HATEOAS to be on the classpath. | true
`auditevents` | Exposes audit events information for the current application. | true
`autoconfig` | Displays an auto-configuration report showing all auto-configuration candidates and the reason why they ‘were’ or ‘were not’ applied. | true
`beans` | Displays a complete list of all the Spring beans in your application. | true
`configprops` | Displays a collated list of all @ConfigurationProperties. | true
`dump` | Performs a thread dump. | true
`env` | Exposes properties from Spring’s ConfigurableEnvironment. | true
`flyway` | Shows any Flyway database migrations that have been applied. | true
`health` | Shows application health information (when the application is secure, a simple ‘status’ when accessed over an unauthenticated connection or full message details when authenticated). | false
`info` | Displays arbitrary application info. | false
`loggers` | Shows and modifies the configuration of loggers in the application. | true
`liquibase` | Shows any Liquibase database migrations that have been applied. | true
`metrics` | Shows ‘metrics’ information for the current application. | true
`mappings` | Displays a collated list of all @RequestMapping paths. | true
`shutdown` | Allows the application to be gracefully shutdown (not enabled by default). | true
`trace` | Displays trace information (by default the last 100 HTTP requests). | true
