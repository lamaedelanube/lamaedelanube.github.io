<h1>Integrando Teraform y vRealize Automation</h1>

<h2>Parte 2: Cloud templates</h2>


En la entrada <a href='https://lamaedelanube.github.io/2022/04/10/Terraform-VRA Parte1.html'> anterior </a>, hablamos sobre las opciones para integrar vRealize automation con Terraform.

También, llevamos el paso a paso de como establecer la integración desde vRealize Automation.
Realmente decimos hacer integración, pero yo realmente lo vemo más como "enseñarle" a vRealize Automation a "hablar" lenguaje Terraform y hacer uso de sus providers. 

Por qué lo veo así?

Recordemos este paso:

![image](https://user-images.githubusercontent.com/51407995/163243995-4086bee1-da8a-481b-8259-501426ad1495.png)


En él, le estamos diciendo a vRealize Automation que utilice las credenciales de los Cloud Zones que ya tiene creados y habilitados para el respectivo proyecto, y sea esta misma conexión sobre la que se realice el aprovisionamiento. 
Para efectos, es exactamente lo mismo si estamos usando los recursos de fábrica de cloud assembly o bien estos "importados" de Terraform.

bueno a los que vinimos, a utilizar estos terraform configurations en nuestros cloud templates.

Nos vamos a la vista de diseño y le vamos a decir nuevo desde - Terraform
NOTA: Realmente también podriamos hacerlo desde un blank canvas, y a provisionar el objeto terraform configuration manualmente.

![image](https://user-images.githubusercontent.com/51407995/163244818-7f92b588-19f6-4223-bec7-d2a96ddb658b.png)

En la primera parte no hay novedad, ponemos un nombre y asociamos este cloud template a un proyecto

IMPORTANTE: Que sea el mismo proyecto que "ligamos" cuando hicimos la integración del repositorio GIT.

En la siguiente pantalla en Repositorios nos va a listar los que tengamos disponibles para ese proyecto desde la integración, normalmente tendremos uno.
Luego, el nos va autopopular el úlitmo commit de dicho repositorio/branch, pero si queremos usar un commit anterior solo lo seleccionamos de la lista

![image](https://user-images.githubusercontent.com/51407995/163245476-75c20171-a0d5-4bb0-b097-568ac70f4a05.png)


Y ahora seleccionaremos el directorio en el que está el terraform config que queremos.
Por esto es indispensable que haya solamente un archivo .conf (y un archivo de variables de ser necesario) por folder.

Pasamos a la siguiente pantalla

![image](https://user-images.githubusercontent.com/51407995/163247262-e57a3192-677f-495b-a461-3e2e6487f59e.png)

Aqui seleccionamos la versión de Terraform que añadimos en pasos anteriores.
También, como se puede notar, si tenemos un campo variables estas serán mapeadas como input del cloud template, y como tal en el editor podremos manipularlas como cualquier otro input.
Podriamos adicionalmente tener algun tipo de output que sea usado por ejemplo, por alguna subscripción de ABX. 

en fin, hacemos click en crear y tendremos el objeto listo.

<b>Analizando lo que tenemos ahora </b>

![image](https://user-images.githubusercontent.com/51407995/163263820-d257644a-9c06-44b6-8b2b-e6d2230e5c10.png)

En el centro de la pantalla tenemos un único objeto, terraform configuration. significa esto que no podriamos conectarlos a otros objetos? En lo absoluto. 

Como podremos ver en la primera sección, automáticamente se crean los inputs basados en las variables. Y aquí podriamos hacer cosas como crear workflows para obtenerlos dinámicamente, crear listas, ponerlos como secretos y cualquier cosa que hariamos con un input.
Apartir de la linea 8, en este caso, empieza el objeto propiamente.

Vemos que estamos usando como provider AzureRM. Bueno, en este caso lo estoy usando para crear un cluster de AKS, cosa que no viene por defecto, aún, en VRA. Básicamente, podriamos usar cualquier provider. Aquí aplica lo que digo que es como enseñarle otro idioma a VRA.

En la línea 18 vemos el cloudZone a usar, que en este caso es el unico cloud zone que tiene mi proyecto. Naturalmente, podemos hacer esto una lista seleccionable, aplicar IFs, etc.

Finalmente, en la línea 25 vemos los valores que seleccionamos al inicio.

Estarán notando en este momento algo que yo considero un pero. Si aplicamos algún cambio a los archivos de configuración en el repositorio, cambiará el commit ID. Para que esto se vea reflejado en nuestro cloud template debemos venir aquí y editarlo manualmente.
Leī en estos días sobre la posibilidad de automatizar esto con Cloud Stream y el API de VRA y claro.... lo voy a probar.


Por ahora dejemos el cloud template sencillo, solamente con un terraform configuration. Ya lo que sigue lo conocemos, creamos una versión de nuestro cloud template o bien desde aquí hacemos click en deploy para enviar una prueba.


![image](https://user-images.githubusercontent.com/51407995/163267207-b3ace15d-31d8-4d16-b021-0f564907821d.png)


Notarán que si le aplique el cambio para solicitar el cloud zone.

Y ya una vez probado, podemos hacerlo más complejo o publicarlo en el sevice broker para el consumo de nuestros usuarios finales.

Vamos a lo primero en una próxima entrada.


