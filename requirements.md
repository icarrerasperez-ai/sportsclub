# Version pinning and security checks of dependencies

#### Objetivos
- [ ] *requirements.txt* mejorado
- [ ] *requirements.dm* con las peguntas respondidas
- [ ] *dependencias.md* con las preguntas respondidas sobre las vulnerabilidades del proyecto
- [ ] *Links* a los ficheros en nuestro fork.

---

## 1. Que problemas encuentras en terminos de seguridad y como los resolverias? (Version pinning VS hashing)
#### Detección del problema
El principal problema de tener el versionado tal cual muestra el documento es que este solo depende de *Versiones por Pinning*, es decir que utiliza dos iguales para especificar la version. 
![[Pasted image 20260202175019.png]]
Esto esta bien hasta cierto punto pues si que es verdad que por lo menos si que estas cogiendo una version concreta, lo cual en caso de actualizacion o de nuevo deploy no se rompera. Pero este metodo no nos garantiza que el contenido dentro de la dependencia que estamos descargando sea el original, por lo que estamos en peligro nuevamente.
#### Solución
Para solucionar el problema anterior del *Version Pinning* utilizaremos *hashes criptograficos*. Esto quiere decir que a cada modulo le calculamos su respectivo hash de contenido. 
**Que ganamos con esto?**
Con esto conseguimos asegurarnos de que el contenido que se instala en los equipos es exactamente el mismo que se a instalado en el nuestro pues si el hash no coincide no se esta haciendo bien. 
### Opcional: Busca Lock files vs Input Files

---

## 2. Modifica el fichero y crea uno nuevo, mejora las versiones mediante tu fork de Git. Como llegas a ese resultado? Entrega los dos ficheros
Para implementar los hashes criptograficos en el fichero requirements.txt, lo primero que haremos es hacer una "copia" de este a modo de copia de seguridad a la cual llamaremos `requirements_old.txt` y crearemos un nuevo fichero con el mismo contenido y con el mismo nombre requirements.txt. La herramienta que utilizaremos sera: 

## 3. Describe el procedimiento que recomendarias en un escenario de produccion para hacer mas modificaciones

## 4. Que escenario estas intentando evitar con estas versiones nuevas?

## 5. Github incluye herramintas de escaneo de vulnerabilidades conocidas, encientralas y utilizalas en tu repositorio.

