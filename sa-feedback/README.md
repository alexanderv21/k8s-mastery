# sa-feedback

## Setting up local development
To create the DB, run the command below (and you can exit immediately after it successfully executed):
```
$ dotnet run
```

## Building the Docker Container

```
$ docker build -t gcr.io/[PROJECT_ID]/sa-feedback:v1 .
```

## Running the Docker Container

```
$ docker run -it -p 9000:80 --name feedback \
    -v ${PWD}/Database:/usr/db \
    -e DATABASE_DIR=/usr/db \
       gcr.io/[PROJECT_ID]/sa-feedback:v1
```

### Verifying that it works

Add a feedback by executing the command below:

```
$ curl -i -X POST http://localhost:9000/feedback \
              -H "Content-type: application/json" \
              -d "{'sentence': 'I like', 'polarity': 0, 'correct': false}"
```

And get the feedbacks by executing:

```
$ curl -X GET http://localhost:9000/feedback
```

## Pushing the container

```
$ docker push gcr.io/[PROJECT_ID]/sa-feedback:v1
```
