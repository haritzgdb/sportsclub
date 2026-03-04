# docker/app/Dockerfile
FROM python:3.13-slim-trixie
EXPOSE 8080

# Set environment variables
ENV PYTHONDONTWRITEBYTECODE=1
ENV PYTHONUNBUFFERED=1

# Set work directory
WORKDIR /app

# Install system dependencies
RUN apt-get update && apt-get install -y \
    curl \
    && rm -rf /var/lib/apt/lists/*

# --------------------------------------------------- 
# T1: SEGURIDAD (USER HARDENING)
# --------------------------------------------------- 
# 1. Creamos un grupo y un usuario de sistema sin contraseña ni privilegios root
RUN groupadd -r appgroup && useradd -r -g appgroup -s /sbin/nologin appuser

# 2. Copiamos los archivos de dependencias primero (optimización de caché)
COPY requirements.txt .

# 3. Instalamos dependencias como root (necesario para escribir en directorios de sistema de python)
RUN pip install --no-cache-dir -r requirements.txt
RUN pip install --no-cache-dir uvicorn

# 4. Copiamos el código del proyecto
COPY sportsclub/ .

# 5. Cambiamos el propietario de la carpeta de trabajo al nuevo usuario
# Esto permite que el usuario pueda manejar los archivos de la app y los estáticos
RUN chown -R appuser:appgroup /app

# 6. Recolectamos estáticos (aún como root para asegurar permisos, o después como appuser)
RUN python manage.py collectstatic --noinput

# 7. ¡CAMBIO CRUCIAL! A partir de aquí, el contenedor deja de ser root
USER appuser

# Run uvicorn
CMD ["uvicorn", "sportsclub.asgi:application", "--host", "0.0.0.0", "--port", "8080"]