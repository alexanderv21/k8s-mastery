# sa-web-app

## Packaging the application

```
$ mvn install
```

## Running the application

```
$ java -jar sentiment-analysis-web-0.0.1-SNAPSHOT.jar --sa.logic.api.url=http://localhost:5000
```

## Building the container

```
$ docker build -f Dockerfile -t gcr.io/[PROJECT_ID]/sa-web-app:v1 .
```

## Running the container

```
$ docker run -d -p 8080:8080 -e SA_LOGIC_API_URL='http://localhost:5000' gcr.io/[PROJECT_ID]/sa-web-app:v1
```

## Pushing the container

```
$ docker push gcr.io/[PROJECT_ID]/sa-web-app:v1
```
