FROM openjdk:17-jdk-alpine

WORKDIR /app

COPY quotes.txt /app

COPY src /app/

RUN javac Main.java

CMD ["java","Main"]


