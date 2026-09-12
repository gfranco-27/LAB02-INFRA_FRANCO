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
- **Bind mount** (`./api/logs/apiX:/app/logs`): usado en cada API para
  mapear una carpeta del host dentro del contenedor.

  