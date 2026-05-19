# Laboratorio Jenkins

# 📋 Task 1. Creación del repositorio

1. Como en el nuevo repositorio sólo queremos una parte del original, inicialmente no descargamos el contenido de los archivos (*blobs*), sólo la estructura y el historial mínimo y con `--sparse` indicamos que sólo queremos una parte

        git clone --filter=blob:none --sparse https://github.com/Lemoncode/CEP-Introduccion-Devops

2. Indicamos que sólo estamos interesados en la carpeta `backend`

    ```bash
    cd CEP-Introduccion-Devops
    git sparse-checkout set 04-jenkins/backend
    ```

3. Preparamos la carpeta raíz del nuevo repo y copiamos el contenido de `04-jenkins/backend`

    ```bash
    cd ..
    mkdir jenkins-lab
    cp -r CEP-Introduccion-Devops/04-jenkins/backend/* jenkins-lab
    ```

4. Añadimos el archivo con el enunciado de la práctica, el Diario del Lab (este archivo) y una carpeta para las capturas.

    ```bash
    cp CEP-Introduccion-Devops/04-jenkins/enunciado.md jenkins-lab
    touch jenkins-lab/DiarioJenkinsLab.md
    mkdir jenkins-lab/capturas
    ```

5. Creamos el nuevo repo

    ```bash
    cd jenkins-lab
    git init
    git add .
    git commit -m "Initial commit"
    ```

6. Creamos un nuevo repositorio público en Github, sin `README.md` para evitar conflictos

7. Vinculamos el repo local con el remoto y subimos el contenido

    ```bash
    git remote add origin https://github.com/juanraprofe/cep-devops-jenkins-lab.git
    git branch -M main
    git push -u origin main
    ```


---


# 📋 Task 2. Validación del proyecto

Antes de empezar a elaborar el `Jenkinsfile` vamos a comprobar que el proyecto funciona correctamente en local. Para ello vamos a ejecutar uno a uno los siguientes comandos:

```bash
npm install
npm run format:check
npm run lint
npm run type-check
npm run test
npm run build
```

Todo estará correcto si no falla `npm install` ni ninguno de los `npm run` (porque no existe el script asociado) y si se genera el archivo `dist/server.mjs`.

CAPTURAAAAAAAAAAAAAAAAAAAAAAAAAAAA

<figure>
    <img src="capturas/task10-3.png" alt="Despliegue y ejecución de la aplicacion reader/writer" width="60%">
    <figcaption>Fig. Despliegue y ejecución de la aplicacion reader/writer</figcaption>
</figure>


---


# 📋 Task 3. Elaboración del Jenkinsfile

## 🤵‍♂️ Jenkinsfile base

```groovy
pipeline {
    agent any

    options {
        disableConcurrentBuilds()
        timestamps()
        timeout(time: 5, unit: 'MINUTES')
    }

    environment {
        FORCE_COLOR = '0'
        NO_COLOR = 'true'
    }

    stages {

    }

    post {
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed. Review logs.'
        }
        always {
            cleanWs()
        }
    }
}
```


TEEEEEEEEEEEEEEEEEEEMP

Bloque base del Jenkinsfile

Se comienza definiendo la estructura base del pipeline antes de implementar las etapas. Esto permite asegurar que la configuración global del job está correctamente definida y alineada con el enunciado antes de añadir lógica de ejecución.

Justificación técnica

Se parte de este bloque porque:

Define el marco de ejecución completo del pipeline (pipeline {}).
Permite configurar elementos globales y transversales que afectan a todas las etapas.
Evita introducir errores al mezclar configuración global con lógica de stages.
Facilita un desarrollo incremental y verificable.
Correspondencia con el enunciado
1. Opciones del job → options {}

Se configuran:

ejecución no concurrente
marcas de tiempo
timeout

Esto afecta al comportamiento global del pipeline, por lo que debe definirse antes de cualquier stage.

2. Variables de entorno → environment {}

Se definen variables globales:

disponibles en todas las etapas
no dependen de la ejecución de ningún stage

Por tanto, deben declararse a nivel superior.

10. Etapas finales → post {}

Se configuran:

comportamiento en éxito
comportamiento en error
limpieza del workspace

Esto no forma parte del flujo de ejecución principal, sino del ciclo de vida del pipeline, por lo que se define fuera de stages.

Por qué no se implementan aún las stages

Las stages (apartados 3 a 9 del enunciado) se implementan después porque:

dependen del contexto de ejecución ya definido
contienen lógica secuencial que es más fácil construir paso a paso
añadirlas todas de golpe dificulta la validación y el control de errores
Conclusión

Este bloque base permite:

cubrir parcialmente el enunciado desde el inicio
establecer una estructura correcta
construir el pipeline de forma incremental, clara y controlada

===========================
