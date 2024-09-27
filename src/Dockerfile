# Utiliser une image de base Maven pour builder l'application
FROM maven:3.8.7-openjdk-17 AS build

# Définir le répertoire de travail dans le conteneur
WORKDIR /app

# Copier le fichier de configuration Maven (pom.xml)
COPY pom.xml .

# Télécharger les dépendances Maven (cela permet de les cacher dans le cache Docker si rien n'a changé)
RUN mvn dependency:go-offline -B

# Copier le reste des fichiers du projet
COPY src ./src

# Builder l'application
RUN mvn clean package -DskipTests

# Utiliser une image Java plus légère pour exécuter l'application
FROM openjdk:17-jdk-slim

# Définir le répertoire de travail dans le conteneur
WORKDIR /app

# Copier le fichier JAR généré par l'étape de build Maven
COPY --from=build /app/target/demo-enset-student-0.0.1-SNAPSHOT.jar app.jar

# Exposer le port que l'application utilise
EXPOSE 8021

# Commande pour exécuter l'application
ENTRYPOINT ["java", "-jar", "app.jar"]
