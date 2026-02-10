# Prompts utilizados para generar el pipeline

## Prompt 1: Tests de backend

```
Crea un job de GitHub Actions que ejecute los tests del backend de un proyecto Node.js/TypeScript.
El backend usa Jest como framework de testing, ts-jest como preset, y Prisma como ORM.
El job debe:
- Ejecutarse en ubuntu-latest con Node.js 18
- Hacer checkout del código
- Instalar dependencias con npm ci (usando cache de npm)
- Generar el Prisma Client con npx prisma generate
- Ejecutar los tests con npm test
El working directory del backend es ./backend.
```

## Prompt 2: Generación del build del backend

```
Crea un job de GitHub Actions que genere el build del backend de un proyecto Node.js/TypeScript.
Este job debe depender del job de tests (solo se ejecuta si los tests pasan).
El backend compila TypeScript a JavaScript con el comando npm run build, generando la carpeta dist/.
El job debe:
- Ejecutarse en ubuntu-latest con Node.js 18
- Depender del job test-backend (needs)
- Hacer checkout del código
- Instalar dependencias con npm ci
- Generar el Prisma Client
- Compilar TypeScript con npm run build
- Subir el artefacto backend/dist con actions/upload-artifact@v4 (retención de 1 día)
```

## Prompt 3: Despliegue del backend en EC2

```
Crea un job de GitHub Actions que despliegue el backend compilado en una instancia EC2 de AWS.
Este job debe depender del job de build (solo se ejecuta si el build fue exitoso).
El despliegue debe:
- Descargar el artefacto del build del backend
- Configurar credenciales AWS usando secrets (AWS_ACCESS_KEY_ID, AWS_SECRET_ACCESS_KEY)
- Crear un paquete de despliegue con el dist compilado, los archivos de Prisma, y package.json/package-lock.json
- Copiar el paquete al EC2 usando SCP (appleboy/scp-action)
- Conectarse al EC2 por SSH (appleboy/ssh-action) y ejecutar:
  - Extraer el paquete
  - Instalar dependencias de producción
  - Generar Prisma Client
  - Reiniciar el proceso del backend con PM2
  - Limpiar archivos temporales
Los secrets necesarios son: EC2_HOST, EC2_SSH_KEY, AWS_ACCESS_KEY_ID, AWS_SECRET_ACCESS_KEY.
```

## Prompt general: Trigger del pipeline

```
El pipeline debe dispararse cuando se hace push a una rama que tiene un Pull Request abierto.
En GitHub Actions esto se logra usando el evento pull_request con los tipos:
- opened: cuando se abre un nuevo PR
- synchronize: cuando se hace push a la rama del PR (nuevos commits)
- reopened: cuando se reabre un PR cerrado
Esto garantiza que cada push a una rama con PR abierto ejecute el pipeline completo.
```
