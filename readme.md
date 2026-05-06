# Proyecto Login + Tabla A320 (Node.js + Express + MariaDB)
Aplicación web docente para los ciclos de DAW y ASIR que muestra los conceptos básicos de:

* Servidor web con Node.js + Express.
* Plantillas con EJS.
* Persistencia con MariaDB.
* Autenticación con sesiones y contraseñas hasheadas con bcrypt.
* Despliegue tradicional sobre Ubuntu Server y despliegue con Docker Compose.

La aplicación tiene dos páginas:

* Login (/login): formulario de usuario y contraseña.
* Tabla (/tabla): listado del estado de fabricación de aviones Airbus A320 en la Final Assembly Line (FAL), con MSN, estación, fase, planta, responsable y estado.

El ejemplo de los datos está inspirado en el proceso real de ensamblaje del A320 en Hamburgo, Toulouse, Tianjin y Mobile.

Máquina virtual de prácticas
Para los alumnos se proporciona una máquina virtual Ubuntu Server preparada con todo lo necesario:

## Descarga de la máquina virtual

Descarga de la VM: Enlace en Google Drive https://drive.google.com/file/d/1v1vkR05zw6IDBe57Bp_a0G43jmt-XUKT/view?usp=sharing

Importable en VirtualBox. Trae instalado Node.js, Docker, Docker Compose y MariaDB, Nginx.

## Estructura del repositorio
proyecto-login/
│
├── README.md                      ← este archivo
│
├── docker-compose.yml             ← orquestación de contenedores
├── .env                           ← variables de entorno (no subir a GitHub real)
├── .env.example                   ← plantilla de .env para el repositorio
├── .dockerignore
├── .gitignore
│
├── app/                           ← código de la aplicación Node
│   ├── Dockerfile
│   ├── package.json
│   ├── server.js                  ← servidor Express y rutas
│   ├── db.js                      ← pool de conexiones a MariaDB
│   ├── crear_usuario.js           ← script auxiliar para crear el usuario admin
│   │
│   ├── views/
│   │   ├── login.ejs              ← formulario de login
│   │   └── tabla.ejs              ← tabla del ensamblaje A320
│   │
│   └── public/
│       └── style.css              ← estilos
│
└── db/
    └── init.sql                   ← script de inicialización de MariaDB
                                     (crea tablas e inserta datos de ejemplo)
## Despliegue
#### Opción A — Despliegue tradicional en Ubuntu Server

Ubuntu Server 22.04 o 24.04
Acceso sudo

``` bash
npm start
```
Probar

``` bash
Desde el propio servidor:
bashcurl -I http://localhost:3000/login
Desde otro equipo de la red abrir en el navegador:
http://IP_DEL_SERVIDOR:3000
Login con admin / 1234.
5. Apagar
Si está en primer plano: Ctrl + C.
Si lo montaste como servicio systemd:
bashsudo systemctl stop proyecto-login
sudo systemctl disable proyecto-login
```

#### Opción B — Despliegue con Docker Compose


APP_PORT=3000
SESSION_SECRET=cambia_esta_clave_en_produccion
2. Levantar los contenedores
Desde la raíz del proyecto:

```bash
docker compose up -d --build

5. Ver logs
bash# Logs de toda la pila
docker compose logs -f

# Solo de un servicio
docker compose logs -f app
docker compose logs -f db

6. Apagar
Detener sin borrar datos:
docker compose stop
Volver a arrancar:
docker compose start
Detener y eliminar contenedores (los datos persisten en el volumen):
docker compose down
Detener y eliminar TODO, incluidos los datos de la base de datos:
docker compose down -v

```

## Funcionamiento de la aplicación

### Flujo de uso

El usuario entra en http://servidor:3000 o 3010 si es desde el contenedory es redirigido a /login.
Introduce usuario y contraseña.
El servidor consulta la tabla usuarios, compara el hash con bcrypt.compare y, si coincide, crea una sesión.
Redirige a /tabla, que está protegida por el middleware requiereLogin.
La página /tabla consulta ensamblaje_a320 y muestra el estado de cada MSN en su estación.
El enlace Cerrar sesión destruye la sesión y vuelve al login.

Estaciones del A320 representadas en los datos
EstaciónFaseStation 40Unión de las secciones de fuselajeStation 35Cableado eléctrico y sistemas hidráulicosStation 30Montaje de alas al fuselajeStation 18Instalación de motores CFM LEAP-1AStation 17Power-on y pruebas en tierra
Plantas de ensamblaje final del A320: Hamburgo, Toulouse, Tianjin y Mobile.



Licencia y autoría
Material docente elaborado para los ciclos de DAW y ASIR.
Uso académico libre con atribución.
