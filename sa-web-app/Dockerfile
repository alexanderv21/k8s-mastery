FROM maven:3.6.1-jdk-8-alpine

ENV SA_LOGIC_API_URL http://localhost:5000

COPY . /sa-web-app
WORKDIR /sa-web-app

RUN mvn install && mv target/*.jar .

CMD ["java", "-jar", "sentiment-analysis-web-0.0.1-SNAPSHOT.jar", "--sa.logic.api.url=${SA_LOGIC_API_URL}"]

EXPOSE 8080
