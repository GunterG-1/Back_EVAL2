# InnovatechChile - Backend API

API REST desarrollada con Node.js + Express para el proyecto de InnovatechChile. Gestiona usuarios y conecta con la base de datos MySQL.

## 📋 Requisitos Previos
- Docker y Docker Compose
- Node.js 18+ (para desarrollo local)
- MySQL 8.0+
## 🚀 Inicio Rápido

### Opción 1: Con Docker Compose (Recomendado)
```bash
cd ..
docker-compose up --build
```
El API estará disponible en: `http://localhost:3000`
### Opción 2: Desarrollo Local
```bash
# Instalar dependencias
npm install

# Configurar variables de entorno
cp .env.example .env

# Ejecutar en modo desarrollo
npm run dev
```

## 🐳 Docker

### Construir imagen

```bash
docker build -t innovatech-backend:latest .
```

### Ejecutar contenedor

```bash
docker run -d \
  --name innovatech-backend \
  -e DB_HOST=db \
  -e DB_USER=appuser \
  -e DB_PASSWORD=appuser_password \
  -e DB_NAME=innovatech_db \
  -p 3000:3000 \
  innovatech-backend:latest
```

## 📁 Estructura del Dockerfile

El Dockerfile utiliza **multi-stage build** para optimizar:

1. **Stage 1 (Build)**: 
   - Usa `node:18-alpine` como base
   - Instala dependencias con `npm ci`

2. **Stage 2 (Runtime)**:
   - Imagen Alpine optimizada
   - Usuario no-root (nodejs:1001) por seguridad
   - Health check cada 30 segundos
   - Expone puerto 3000

## 🔐 Variables de Entorno

```env
NODE_ENV=production
DB_HOST=db
DB_PORT=3306
DB_USER=appuser
DB_PASSWORD=appuser_password
DB_NAME=innovatech_db
PORT=3000
```

## 📦 Persistencia de Datos

Los datos se persisten mediante volúmenes Docker:
- **db_data**: Almacena la base de datos MySQL
- Los cambios persisten incluso después de reiniciar contenedores

## 🔄 Pipeline CI/CD

El proyecto incluye un workflow GitHub Actions que:

1. **Build**: Construye la imagen Docker
2. **Push**: Envía a Docker Hub
3. **Deploy**: Descarga e inicia el contenedor en EC2

### Configuración necesaria (GitHub Secrets)

```
DOCKERHUB_USERNAME    → Tu usuario de Docker Hub
DOCKERHUB_TOKEN       → Token de Docker Hub
EC2_HOST              → IP pública de la instancia EC2
EC2_USER              → Usuario SSH (ec2-user o ubuntu)
EC2_SSH_KEY           → Contenido completo de la llave .pem
DB_HOST               → Host de la BD en EC2
DB_PORT               → Puerto de la BD
DB_USER               → Usuario de la BD
DB_PASSWORD           → Contraseña de la BD
DB_NAME               → Nombre de la base de datos
```

## 📝 Endpoints API

| Método | Endpoint | Descripción |
|--------|----------|-------------|
| GET | `/api/usuarios` | Obtener todos los usuarios |
| GET | `/api/usuarios/:id` | Obtener usuario por ID |
| POST | `/api/usuarios` | Crear nuevo usuario |
| PUT | `/api/usuarios/:id` | Actualizar usuario |
| DELETE | `/api/usuarios/:id` | Eliminar usuario |

## 🧪 Verificar Salud del Contenedor

```bash
docker ps                              # Ver contenedores activos
docker logs innovatech-backend         # Ver logs
docker logs -f innovatech-backend      # Ver logs en tiempo real
docker exec innovatech-backend npm --version  # Verificar instalación
```

## 🔄 Actualizar y Redeploy

1. **Hacer cambios en el código**
2. **Commit y push a rama `deploy`**
3. **GitHub Actions dispara automáticamente**
4. **El contenedor se actualiza en EC2**

```bash
git add .
git commit -m "feat: agregar nuevo endpoint"
git push origin deploy
```

## 🛠️ Desarrollo Local

```bash
# Instalar dependencias
npm install

# Modo desarrollo con nodemon
npm run dev

# Modo producción
npm start
```

## 📚 Buenas Prácticas Implementadas

✅ Multi-stage build para reducir tamaño  
✅ Usuario no-root por seguridad  
✅ Health checks automáticos  
✅ Capas limpias y optimizadas  
✅ Secrets seguros en GitHub  
✅ Red privada entre servicios  
✅ Persistencia de datos con volúmenes  

## 🐛 Solucionar Problemas

### Contenedor no inicia
```bash
docker logs innovatech-backend
# Verificar BD está disponible
docker exec innovatech-db mysqladmin ping
```

### Error de conexión a BD
- Verificar `DB_HOST` sea `db` (nombre del servicio en docker-compose)
- Esperar a que DB esté lista (healthcheck)

### Puerto 3000 en uso
```bash
# Buscar qué usa el puerto
lsof -i :3000
# O cambiar puerto en docker-compose.yml
```

## 📄 Licencia

MIT
