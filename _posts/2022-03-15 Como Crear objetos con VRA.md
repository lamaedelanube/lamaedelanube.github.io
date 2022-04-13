<H1>Como crear objetos varios con vRealize Automation </H1>

<h2>Múltiples caminos, un mismo objetivo</h2>

La idea de esta entrada va a ser un caso de estudio donde iremos desarrollando las diferentes formas que tenemos para lograr un mismo objetivo usando todaaaas las herramientas que nos ofrece vRealize Automation y sus integraciones.
Iremos editando esta entrada conforme vayamos desarrollando cada una de las formas.

<h3>El caso</h3>
Mi cliente me solicita lo siguiente: Creación de clusters de AKS, en un resource group nuevo, en una subred nueva creada para al momento. 
No hay Tanzu involucrado. Por ahora. 

<br/><br/>

1. Primera opción: Recursos por defecto de cloud assembly.


Vemos que aunque tenemos recursos para las redes y los resources groups, no tenemos uno para los clusters de AKS, así que esta no es una opción.
                
<br/><br/>

2. Segunda opción: Code Stream.



|  Ventajas |  Desventajas     |
|-----------|------------------|
| Facil de  implementar| Require un motor de ejeucion |
|Permite integraciones más complejas| No tiene acciones de dia 2| 

<br/><br/>

3. Usar la integracion de Cloud Assembly con Terraform.

|  Ventajas |  Desventajas     |
|-----------|------------------|
| Facil de  implementar| * Podria requerir un motor de ejeucion |
|Permite integraciones más complejas| Hay que conocer de Terraform| 
|Permite algunas acciones de dia 2 | Require de un repositorio GIT |
|Permite algunas acciones de dia 2 | Se depende que exista el provider |

En <a href= https://lamaedelanube.github.io/2022/04/10/Terraform-VRA-Parte1.html>esta</a> serie de post empezamos a desarrollar este caso

<br/><br/>

4. Crear custom resources


|  Ventajas |  Desventajas     |
|-----------|------------------|
|El recurso creado será como uno nativo de VRA| La más compleja de implementar |
|| Se requiere de VRO |
|| Se depende del API expuesto | 
|| o de un motor de ejecución | 
          
<br/><br/>
5. Usar SaltStack  Config

Investigando
