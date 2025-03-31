# JBoss EAP 8.0 deployment on OpenShift

## JBoss EAP Maven plugin

```bash
mvn clean install -Popenshift
```

## JBoss EAP Docker build

Maven build WAR
```bash
mvn clean package
```

Build docker image
```bash
docker build -f src/docker/Dockerfile -t helloworld-eap8:1.0 .
```

Run docker image
```bash
docker run -p 8090:8080 helloworld-eap8:1.0
```

## JBoss EAP Docker build with OCP BuildConfig



## Links

