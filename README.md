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





  