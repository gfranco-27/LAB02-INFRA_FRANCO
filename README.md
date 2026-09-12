# Laboratorio 02 franco rodriguez - API con 3 replicas y BD

## Descripción
Se desplegaron 3 instancias de api mas una bd POSTGRE usando docker compose columenes y variables de entorno

## Estructura
- `api ` codigo de la api
-`docker-compose.yml` orquestación de servicios
-`.env.example` plantilla de variables de entorno
-`.gitignore` archivos excluidos

## Uso
```bash
cp .env.example .env
docker compose up -d --build
```

Probar:
```bash
curl http://localhost:3001/
curl http://localhost:3002/
curl http://localhost:3003/
```

Apagar: 
```bash
docker compose down
```

## Variables de entorno
| Variable       | Descripción                     |
|----------------|----------------------------------|
| DB_USER        | Usuario de PostgreSQL             |
| DB_PASSWORD    | Contraseña de PostgreSQL          |
| DB_NAME        | Nombre de la base de datos        |
| API_MESSAGE    | Mensaje devuelto por la API       |
| API_PORT       | Puerto interno de la API          |

## Volúmenes usados
- **Volumen nombrado** (`db_data`): usado en el servicio `db` para persistir
  los datos de PostgreSQL aunque el contenedor se elimine.
```yaml
services:
  db:
    volumes:
      - db_data:/var/lib/postgresql/data

volumes:
  db_data:
```

**¿Por qué este tipo de volumen y no otro?**
Se usó este volumen ya que es gestionado por docker, este funcionaria en cualquier maquina donde se clone el proyecto

Se puede confirmar que el volumen existe y sigue ahí con:
```bash
docker volume ls
docker volume inspect lab02-infra_franco_db_data
```

## PREGUNTAS : Tipos de redes y volumenes en Docker
- Tipos de redes

  Bridge: ES el driver por defecto, se usa cuando la app corre en un contenedor que necesita comunicarse con otros del mismo host.

  Host: Elimina el aislamiento de red entre el contenedor y el host de Docker.

  None: Aisla completamente a un contenedor del host y de otros contenedores.

  Overlay: Conecta multiples demonios docker entre si y permite que los servicios de Swarm y los contenedores se comuniquen a través de distintos nodos, eliminando la necesidad de hacer ruteo al nivel de sistema operativo.

  Ipvlan: Da control sobre el direccionamiento IPv4 e IPv6

  Macvlan: Permite asignar una dirección MAC a un contenedor haciendo que aparezca como un dispositivo físico en la red.

- Tipos de Volumenes

  Volumes: Son almacenes de datos persistentes creados y gestionados por docker. Es ideal para persistencia de datos generados y usados por contenedores docker.

  Bind mounts: Enlaza una ruta específica del sistema de archivos del host con una ruta dentro del contenedor. Se usa cuando se necesita acceder a archivos o directorios tanto desde el contenedor como desde el host.

  Tmpfs mounnts: Este es temporal y solo persiste en la memoria del host, cuando el contenedor se detiene el tmpfs mount se elimina. 

## Guía de ejecución

### 1. Clonar el repositorio
```bash
git clone https://github.com/TU_USUARIO/LAB02-INFRA_FRANCO.git
cd LAB02-INFRA_FRANCO
```

### 2. Configurar variables de entorno
Copiar la plantilla y ajustar los valores si se desea:
```bash
cp .env.example .env
```

### 3. Levantar los servicios
```bash
docker compose up -d --build
```
Este comando construye localmente las 3 imágenes de la API (`api1`, `api2`,
`api3`) y descarga la imagen oficial `postgres:16-alpine` para la base de
datos.

### 4. Verificar que todo esté corriendo
```bash
docker compose ps
```
Deben aparecer 4 contenedores activos: `lab_db`, `lab_api1`, `lab_api2`,
`lab_api3`.

### 5. Probar las APIs
```bash
curl http://localhost:3001/
curl http://localhost:3002/
curl http://localhost:3003/
```
Cada una debe responder con el mensaje configurado en `API_MESSAGE`.

### 6. Verificar la base de datos
```bash
docker exec -it lab_db psql -U <DB_USER> -d <DB_NAME>
```
(usar los valores definidos en el `.env`)

### 7. Verificar persistencia del volumen (opcional pero recomendado)
Dentro de `psql`:
```sql
CREATE TABLE prueba (id SERIAL PRIMARY KEY, nombre TEXT);
INSERT INTO prueba (nombre) VALUES ('dato de persistencia');
\q
```
Reiniciar sin borrar volúmenes:
```bash
docker compose down
docker compose up -d
docker exec -it lab_db psql -U <DB_USER> -d <DB_NAME> -c "SELECT * FROM prueba;"
```
Si el dato sigue apareciendo, el volumen nombrado `db_data` está persistiendo
correctamente la información.

### 8. Detener el proyecto
```bash
docker compose down
```


  