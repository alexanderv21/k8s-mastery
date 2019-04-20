# sa-logic

## Building the container

```
$ docker build -f Dockerfile -t gcr.io/[PROJECT_ID]/sa-logic:v1 .
```

## Running the container

```
$ docker run -d -p 5000:5000 gcr.io/[PROJECT_ID]/sa-logic:v1
```

### Verifying that it works
Execute a POST on endpoint: `localhost:5000/analyse/sentiment` with request body below:

```
{
    "sentence": "Have a good day!"
}
```

## Pushing the container

```
$ docker push gcr.io/[PROJECT_ID]/sa-logic:v1
```
