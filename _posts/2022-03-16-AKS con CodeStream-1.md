
<h1> Creando objetos no nativos, usando Code Stream</h1>

Quizás soy yo, pero siento que Code Stream es la funcionalidad menos aprovechada de vRealize Automation. Yo diría que es la incomprendida. Pero cuando uno la comienza a usar, es realmente bastante simple.

Si bien su función es ser la madre de las herramientas para CI/CD, no siempre necesitamos irnos "tan allá", y podemos hacer cosas más simples aprovechandonos de esta belleza.


En este caso en particular quiero crear clusters de AKS.
Revisando la documentación de Azure veo que quizás la forma más simple de hacerlo es aprovechando el Azure CLI.

Y entonces, para que quedriamos hacerlo con Code Stream?
La modularidad. Podemos ir agregando pasos como veremos en este ejemplo, integrando con otras herramientas entre muchas otras cosas.

ok, Que necesitamos:
Aqui podriamos tomar dos caminos:

1. Usar un endpoint de Code Stream.
 - Esto sería, obtener una instancia de kubernetes o docker donde, en el momento de ejecucion del pipeline, se cree un contenedor con una imagen que ya contenga el azure CLI instalado, esta ejecute los comandos que necesitamos y desaparezca luego de su ejecucion.
    Esta sin duda me parece la opción más elegante. 

2. Hacer SSH a algún equipo existente, y que este ejecute las lineas de comando por nosotros.


En el fondo las dos opciones son bastante parecidas, y creo que depende mucho de la infraestructura que ya tengamos instalada.

Ahora, en ambos casos debemos poder tener acceso desde vRealize Automation a este intermediario que vamos a usar. Asi que, si estamos usando vRealize Automation Cloud como es mi caso, debemos tener en cuenta que es indispensable que exista primero el cloud proxy y que este sea registrado en Cloud Assembly.

Algo que note es que, por algun motivo, debemos esperar un rato luego de que el cloud proxy es registrado en Cloud Assembly para que este aparezca disponible en Code Stream.

Aqui los caminos se separan, y por tanto vamos a separar los post.

Seguiremos hablando de la opción 2, hacerlo con SSH.

A: Requerimos, desde luego, un equipo al que le podamos hacer SSH. Este debe tener el Azure Cli instalado. 

Va a depender del OS claro, pero en general podemos lograrlo con este comando:

    curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash

o bien con algo mas tradicional
```
    sudo apt-get update
    sudo apt-get install azure-cli
```

B: Bueno tenemos el equipo que va a correr el pipeline y tenemos el AzureCli listo. Qué más?

C: Ahora vamos a Code Stream, Endpoints.
Aquí, al menos en la versión Cloud hacemos algo interesante, debemos crear un endpoint de tipo agente, apuntando a nuestro Cloud Proxy. El nombre, cualquiera que sea significativo.
Seleccionamos primero eso si el proyecto al que vamos a ligar el pipeline.

![image](https://user-images.githubusercontent.com/51407995/163280028-636079bb-de52-43ab-9059-632c4ec088ce.png)

Como se notará no estamos introduciendo la IP o hostname del equipo al que le instalamos el Azure CLi. Aqui solo estamos "mapeando" el cloud proxy para poder comunicarnos al mismo.

D: OK, Vamos a usar el cloud account de Cloud assembly para loguearnos a Azure?
No, para nada. En este caso vamos a establecer una conexión de la siguiente forma:

![image](https://user-images.githubusercontent.com/51407995/163281413-c34ebd49-fed6-4c4a-9e9a-5f99e3359497.png)


Eso significa, que vamos a requerir información para inicio de sesión en Azure.
- Las opciones que nos da Microsoft son usar credenciales, o usar un service principal name. Siendo esta segunda la recomendada cuando estamos usando automatización.

No es como que sea una mejor práctica, pero algo que si podríamos hacer aquí es reutilizar el mismo APP ID y Secret que usamos para Cloud Assembly, si lo tenemos a mano.
O bien, crear uno nuevo.

Cuando lo tengamos, podemos almacenarlo como secreto en Code Stream, para no estarlo solicitando en tiempo de ejecución. Y para esto nos dirigimos en code stream a variables, nueva variable:

Como todo, tenemos que asociarlo a un proyecto y poner un nombre significativo y un valor que será almacenado apropiadamente.

![image](https://user-images.githubusercontent.com/51407995/163281891-b8a72cdc-96a6-4680-9527-e55609c875ee.png)


Ok, entonces Creemos un secreto para el APP ID, el APP Secret y el Tenant ID.

E: Otra cosa que podriamos guardar como un secreto, es el SSH key de nuestro SSH Host. Mismo procedimiento. 

En mi caso quedo así:

![image](https://user-images.githubusercontent.com/51407995/163282027-30f4592a-6411-4931-a539-ebbe0f728086.png)


F: Bueno, listos con los requisitos. Ahora nos vamos a Pipeline y comenzamos a crear nuestro pipeline para esta implementación.


> En realidad, algo que estamos asumiendo en este post es que tenemos muy claro el paso a paso de lo que queremos hacer, y los comandos que debemos ejecutar para lograrlo. Este podria no ser el caso claro, y podriamos requerir a documentacion externa. Algo que yo hice fue probar los comandos primero "manualmente" en el mismo host y luego traerlo a automatizar.


Ok deciamos, nos dirigimos a Pipelines y allí nuevo pipeline y Black Canvas

- Como todo, lo asignamos a un proyecto y le ponemos un nombre.
- Ok, entramos al editor
Podriamos escibir mucho sobre este editor, pero debemos ver lo básico

![image](https://user-images.githubusercontent.com/51407995/163282758-a6b98e1d-3ab3-4112-a9c1-789295c91823.png)

-- En el primer tab tenemos los inputs, aquí debemos considerar que entradas vamos a ir pidiendo de los usuarios de acuerdo a las diferentes etapas de los comandos que vamos a ejecutar. 

En este caso yo cree todos los que se ven a continuación:

![image](https://user-images.githubusercontent.com/51407995/163282887-82c0f930-817f-4fc7-ba5b-1be833b0681b.png)

-- El segundo tab, workspace, no lo vamos a usar en este momento. Si nos hubieramos ido por el la forma #1 de hacerlo, aqui es donde deberiamos introducir la imagen a usar y el ambiente de docker/K8s.

-- El tercer tab es donde hacemos el trabajo como tal. Aqui creamos el modelo.
Ya que esta es una herramienta de CI/CD podemos tener diferentes Stages y pasos simultaneos o paralelos dentro de los mismos. 

El modelo básico al que vamos a llegar es este:


![image](https://user-images.githubusercontent.com/51407995/163283340-97158cb3-9490-4fe8-bd4b-725e32858ee6.png)

Realmente es bastante sencillo y pudimos haberlo hecho en un solo paso, pero quisiera tener granularidad y poder poner controles en el medio.

Pero este post está ya muy largo, así que lo continuaremos en la siguiente entrada.



