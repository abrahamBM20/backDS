# Build stage
FROM maven:3.9.0-eclipse-temurin-17-focal AS builder

WORKDIR /app

# Copiar archivos POM
COPY pom.xml .

# Descargar dependencias (incluyendo driver MySQL)
RUN mvn dependency:go-offline -B

# Copiar código fuente
COPY src ./src

# Build de la aplicación
RUN mvn clean package -DskipTests -B

# Runtime stage
FROM eclipse-temurin:17-jre-jammy

WORKDIR /app

# Instalar curl para healthcheck y mysql-client para diagnóstico
RUN apt-get update && apt-get install -y curl mysql-client && rm -rf /var/lib/apt/lists/*

# Copiar JAR del stage anterior
COPY --from=builder /app/target/*.jar app.jar

# Crear usuario no-root para seguridad
RUN useradd -m -u 1000 springuser && chown -R springuser:springuser /app
USER springuser

# Exponer puerto
EXPOSE 8080

# Health check - verificar que la BD está disponible
HEALTHCHECK --interval=30s --timeout=10s --start-period=40s --retries=3 \
  CMD curl -f http://localhost:8080/actuator/health || exit 1

# Iniciar aplicación con perfil docker
ENTRYPOINT ["java", "-Dspring.profiles.active=docker", "-jar", "app.jar"]
