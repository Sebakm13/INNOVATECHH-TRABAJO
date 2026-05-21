Trabajo E-Commerce Innovatech Chile - EP2
Profe, este es el readme de nosotros para la Evaluación Parcial N°2. Acá dejamos montada toda la arquitectura de contenedores, la automatización con las pipelines y la persistencia que nos pidieron para el proyecto de Innovatech.

Cómo está armada la solución
Separamos la aplicación en 3 servicios clave que corren juntos gracias a Docker Compose:

Frontend (React): Está optimizado en un Dockerfile multi-stage. Corre sobre Nginx y le quitamos los privilegios de root (usa el usuario nginx) para que sea ultra seguro y liviano.

Backend (Spring Boot): También con multi-stage usando Maven para compilar el código a un .jar limpio. Corre con OpenJDK y usa un usuario sin privilegios (devopsuser) para cumplir con las prácticas de seguridad.

Base de Datos: Una instancia de MySQL 8.0 que almacena toda la información del e-commerce.

Explicación de la Persistencia (Por qué usamos Named Volumes)
El encargo nos pedía elegir y justificar cómo salvar los datos si los contenedores se reinician. Nosotros elegimos usar Named Volumes (Volúmenes Nombrados) en vez de Bind Mounts.

¿Por qué? Acá van nuestras razones técnicas:

Seguridad y orden de Docker: Los Named Volumes los maneja Docker internamente en su propia carpeta reservada de la máquina de AWS (/var/lib/docker/volumes/). Así evitamos que si nos metemos a la consola de AWS a mover archivos por error, vayamos a borrar la base de datos sin querer.

Rendimiento: Al correr en una máquina virtual Linux (Ubuntu en AWS EC2), los volúmenes nombrados funcionan mucho más rápido al leer y escribir datos que apuntar a una carpeta local (bind mount). Esto hace que la página responda mejor cuando se consulten productos o usuarios.

Continuidad total: Cuando la pipeline de GitHub Actions se conecta por SSH a desplegar los cambios, ejecuta un docker-compose down --remove-orphans. Si usáramos otra cosa, los datos de los usuarios se borrarían cada vez que subimos código. Con los Named Volumes, la base de datos se mantiene intacta aunque bajemos e iniciemos los contenedores mil veces.

Pipeline de CI/CD (GitHub Actions)
Configuramos el flujo automatizado en .github/workflows/deploy.yml que se activa al hacer push. El pipeline hace la pega completa en dos cajitas (etapas):

build-and-push: Se encarga de revisar que la estructura del código esté en orden para ser desplegada.

deploy-to-aws: Se conecta por SSH a nuestra instancia EC2 usando credenciales seguras (GitHub Secrets). Borra los contenedores viejos con cambios desactualizados, se trae lo nuevo de Git y levanta el stack limpio con un docker-compose up --build -d.

Toda la configuración sensible (IP de AWS, usuario y claves SSH) está protegida en la sección de Secrets del repositorio para no exponer datos públicos.

 Y de final se puede ver un trabajo exitoso ya que se puede ver como el pipeline los docker todo en si las imagenes hacen su funcion y arroja el frontend a la perfeccion al ingresar la ip publica a internet
