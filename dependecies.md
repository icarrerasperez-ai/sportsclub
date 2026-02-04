## 5. Github incluye herramintas de escaneo de vulnerabilidades conocidas, encuentralas y utilizalas en tu repositorio.

### Descubrimiento de la herramienta Dependabot
Github incluye herramientas de escaneo de vulnerabilidades en su propio repositorio, lo que por defecto estan desabilitadas por lo que hay que encontrarlas y habilitarlas
Estas se encuantran en *Settings* > *Advanced Security* > *dependabot*
![[Pasted image 20260203172144.png]]

**¿Que estamos habilitando?**
- *Dependabot alerts*: Avisa cuando encuentra un CVE en alguna version espeifica de tu `requirements.txt`.
- *Dependabot security updates*: Crea automaticamente un Pull Request para actualizar una libreria vulnerable.
- *Grouped seurity updates*: Junta las 2 anteriores en un solo Pull Request.

Las alertas podemos verlas en *Security* > *Dependabot*
![[Pasted image 20260203173307.png]]
### Uso dependabot
Esta herramienta lo que hace es escanear depenndencias de manera automatica. 
Se utiliza habilitandolo. Pero su metodo de funcionamiento es algo peculia, este consiste en diferentes ciclos:
- *Ciclo de Escaneo*: Aqui es cuando lee el fichero requirements.txt, consulta las versiones y se asegura de que no tienen ninguna vuln conocida.
- *Ciclo de propuesta*: Esta parte es el momento el el que esta herramienta crea un pull request con la version corregida de la dependencia que tiene un CVE asociado. Luego ya es cuestion del usuario aplicarla o no
- *Ciclo de Integracion*: En este ciclo es el momento en el que el usuario lo implementa dentro del CI con una serie de parametros que le interesen. Hay diferentes tipos de integracion como: *Security*, *job* o de *integration*

**¿Que beneficios tiene esto?**
Dependabot automatiza la actualizacion de dependencia que solia ser una tarea tediosa el cual solia ser propensa a posponerse y con el tiempo volverse un problema de seguridad.

### Resultado en el fork
En el fork nos aparece en la seccion: *Security* > *Dependabot*. Un resumen de las alertas.
![[Pasted image 20260203181337.png]]
En mi caso las dependencias no tienen CVE conocidos por lo que no aparece nada.

Lo he vuelto a revisar un dia despues y resulta que si que ha aparecido contenido dentro de esta ruta:
![[Pasted image 20260204185517.png]]

Como se ve aparecen diferentes vulnerabilidades de *SQL Injection* a las cuales si accedemos podemos visualizar que le pasa.
![[Pasted image 20260204185816.png]]

Esta alerta nos informa de que tenemos una version de *django* vulnerable `(6.0.1)` y nos sugiere que la cambiemos a `6.0.2` que ya tiene la Vuln parcheada.

### ¿Hace falta cambiar algo?
Como el fichero requirements.txt ya tiene las versiones escritas y los hashes creados no creo oportuno añadir nada mas, como mucho añadirlo en el CI pero este ya se lanza automaticamente asique lo veo innecesario.

---
## 6. Investiga 3 herramientas que consigan hacer lo mismo que hasta ahora, numera sus pros y contras, la comunidad que tienen detras y como se utiliza
Investigando he encontrado barias herramientas, las que me han parecido mas interesantes han sido: 
- OWASP Dependency
- Aikido Sast
- Azure Migrate

### OWASP Dependency
OWASP es una herramienta gratuita basada en firmas.
- *Pros*
	- Tiene una base de datos propia, que tiene mas vulns que las publicas
	- Genera un Pull Request automatico para actualizar las librerias a versiones mas seguras
	- Se puede añadir facilmente al pipeline
- *Contras*
	- La version gratuita es muy limitada
	- Se sube a la nube
Esta herramienta tiene una comunidad gigante ya que es una de las herramientas mas queridas por los desarrolladores.
Se instala mediante CLI `npm install -g snyk`, se autentica con `snyk auth` y se ejecuta `snyk test` en la raíz del proyecto para ver las vulnerabilidades.

### Aikido Sast
Es una herramienta que se basa en SonarQube, es decir un todo en uno 
- *Pros*
	- No solo busca vulnerabilidades
	- Soporta mas de 30 lenguajes
	- Permite definir las puertas de calidad que analiza codigo inseguro
- *Contras*
	- Dificil de configurar
	- Suelen saltarle falsos positivos
Su comunidad es bastante industrial lo que quiere decir que es facil encontrar soluciones al problema de manera profesional.
Se integra como un paso en el pipeline (Jenkins, GitHub Actions). El escáner analiza el código fuente, envía los datos al servidor de Sonar y este devuelve un reporte con los fallos encontrados.

### Azure Migrate
Migrate esta pensado para utilizarse con Azure, este funciona mejor con Amazon Web Service
- *Pros*
	- Permite que el servidor original siga funcionando durante el escaneo 
	- Simplifica la conversion de servidores fisicos a instancias EC2
	- Suele ser gratuito durante 90 dias
- *Contras*
	- Esta diseñado para AWS
	- Tiene una curba de aprendizaje alta
Tiene una amplia comunidad dentro del desarrollo en la nube
Se instala un agente de replicación en el servidor de origen (Windows o Linux). Este sincroniza los datos con AWS. Desde la consola de AWS, se lanzan instancias de prueba y, una vez validadas, se realiza el "cutover" definitivo.

---
## 7. Instala una de estas 3 herramientas anteriores ejecutala en local y añadela al pipeline si es posible.

### OWASP Dependency

**Instalacion**
Primero vamos a la web oficial (https://owasp.org/www-project-dependency-check/) y nos instalamos el *comprimido Comand Line*.
![[Pasted image 20260204162142.png]]

Una vez descargado lo descomprimimos y accedemos
```sh
unzip dependency-check
```
![[Pasted image 20260204162424.png]]
**Uso**
Una vez descomprimido desde nuestro proyecto lanzamos el siguiente comando:
```sh
 bash ~/Baixades/dependency-check/bin/dependency-check.sh --scan 
```
![[Pasted image 20260204173448.png]]![[Pasted image 20260204180313.png]]
![[Pasted image 20260204180329.png]]

### Pipeline
Para añadirlo en la Pipeline tendremos que añadir dentro del fichero *ci.yml*
```yml
# Extraido del repositorio oficial de la herramienta (https://github.com/dependency-check/Dependency-Check_Action)
jobs:
  depchecktest:
    runs-on: ubuntu-latest
    name: depecheck_test
    steps:
      - name: Checkout
        uses: actions/checkout@v2
      - name: Build project with Maven
        run: mvn clean install
      - name: Depcheck
        uses: dependency-check/Dependency-Check_Action@main
        id: Depcheck
        with:
          project: 'SportClub'
          path: '.'
          format: 'HTML'
          out: 'reports' # this is the default, no need to specify unless you wish to override it
          args: >
            --failOnCVSS 7
            --enableRetired
      - name: Upload Test results
        uses: actions/upload-artifact@master
        with:
           name: Depcheck report
           path: ${{github.workspace}}/reports

```

Al lanzar el commit si vamos a *Actions* > *Owasp v2* (Es decir en nombre del commit que hemos puesto) podemos ver como carga el actions.
![[Pasted image 20260204194054.png]]

En mi caso aparece el Job Lint que falla por alguna cosa del ruff, mientras que el que hemos creado nosotros *depecheck_test* nos aparece como completado con exito.
Si entramos veremos:
![[Pasted image 20260204194315.png]]
Esta imagen muestra el WorkFlow que ha seguido el Job para ejecutarse.

Ahora el *report* se genera en el apartado donde aparece el esquema con los jobs scroleando hasta encontrar el apartado *artifacts*.
![[Pasted image 20260204195601.png]]

Esto nos descargara un `.zip` que si lo descomprimimos nos dara el fichero `.html` con el contenido de la pagina del *report*.
![[Pasted image 20260204195809.png]]