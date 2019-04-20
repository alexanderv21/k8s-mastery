# sa-frontend

## Starting the web app Locally

```
$ yarn start
```

## Building the application

```
$ yarn build
```

## Building the container

```
$ docker build -f Dockerfile -t gcr.io/[PROJECT_ID]/sa-frontend:v1 .
```

## Running the container

```
$ docker run -d -p 80:80 gcr.io/[PROJECT_ID]/sa-frontend:v1
```

## Pushing the container

```
$ docker push gcr.io/[PROJECT_ID]/sa-frontend:v1
```
