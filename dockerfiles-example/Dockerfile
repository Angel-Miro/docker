FROM ubuntu
RUN apt-get update && \
	apt-get install -y default-jre

WORKDIR /app
COPY docker-ajmg-0.0.1-SNAPSHOT.jar .

ENTRYPOINT ["java", "-jar","docker-ajmg-0.0.1-SNAPSHOT.jar"]
