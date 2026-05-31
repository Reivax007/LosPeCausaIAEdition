# ─── ETAPA 1: Construcción (Build) ───────────────────────
# Usamos una imagen que ya tiene Maven y Java 17 instalados
FROM maven:3.9.6-eclipse-temurin-17-alpine AS builder

WORKDIR /app

# Copiamos solo el pom.xml primero para descargar las dependencias
# Esto aprovecha el caché de Docker y hace que futuras construcciones sean más rápidas
COPY pom.xml .
RUN mvn dependency:go-offline -B

# Ahora copiamos el código fuente
COPY src ./src

# Compilamos el proyecto creando el .jar
# MUY IMPORTANTE: Usamos -DskipTests para evitar el error de conexión a la base de datos que tuviste antes
RUN mvn clean package -DskipTests

# ─── ETAPA 2: Ejecución (Run) ────────────────────────────
# Usamos una imagen mucho más ligera solo con el entorno de ejecución de Java (JRE)
FROM eclipse-temurin:17-jre-alpine

WORKDIR /app

# Copiamos el .jar generado en la Etapa 1 hacia esta nueva imagen limpia
COPY --from=builder /app/target/agrocompliance-0.0.1-SNAPSHOT.jar app.jar

# Exponemos el puerto interno de Spring Boot
EXPOSE 8080

# Comando de inicio de la aplicación
ENTRYPOINT ["java", "-jar", "app.jar"]