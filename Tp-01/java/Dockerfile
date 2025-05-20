# image Java JRE légère
FROM openjdk:21-jdk-slim

# Crée un répertoire de travail dans le conteneur
WORKDIR /app

# pourcopier le fichier compilé (Main.class) dans le conteneur
COPY Main.class .

# Commande pour lancer l'application
CMD ["java", "Main"]
