<H1>Como crear objetos utilizando Code Stream Parte 2</H1>
<H2>Desmenuzando el Modelo</H2>

En esta <a href =https://lamaedelanube.github.io/2022/03/15/Como-Crear-objetos-con-VRA.html> serie </a> de entradas estamos conversando como podemos hacer para crear objetos no nativos, especificamente clusters de AKS, usando vRealize Automation. Como vimos tenemos muchisimos caminos para llegar a Roma.

En el post anterior, comenzamos explicando sobre los pasos previos para llegar al modelo. Y en esta entrada solamente vamos a ir hablando de cada uno de los pasos.

Este es el modelo simple al que queremos llegar:

![image](https://user-images.githubusercontent.com/51407995/163284150-fbb531f2-48fe-4284-87ef-b4d95cec49f4.png)


Como se puede ver, tenemos 2 stages y estos con 2 tasks simultaneos:

Por cierto, yo lo cree como código y luego lo importe (también lo pude simplemente sincronizar con GIT) el mismo se encuentra aquí:

<H3>Stage 1. Create AKS </h3>
Aquí simplemente le puse un nombre significativo.

<H4> Az login </H4>

Aquí vamos a iniciar la sesión con Azure, usando las variables que añadimos como secretos anteriormente.

![image](https://user-images.githubusercontent.com/51407995/163284507-5796d1b6-84a3-4d97-8307-df0f99274989.png)

En el lado derecho de la pantalla pasa toda la accion:
- Tipo: Seleccionamos SSH.
  Bonus: Notan que existe powershell? Si, tambien podríamos haber usado un host windows. 
  
- Precondición: Si quisieramos revisar el estado de una variable o paso anterior. En este caso nada.

- SSH, ah en esta secciòn si es donde vamos a poner la IP de nuestro SSH host asi como la información de inicio de sesión, noten que estoy haciendo referencia a la variable con el SSH key. 

Y ahora el script

```
az login --service-principal --username ${var.aks_app_id} --password ${var.aks_app_password} --tenant ${var.aks_tennant_id}
az group create --name ${input.Resource_Group} --location ${input.Region}
az aks create --resource-group ${input.Resource_Group} --name ${input.ClusterName} --node-count ${input.Node_Count} --node-vm-size ${input.Node_InstanceSize} --generate-ssh-keys --enable-addons monitoring

```
<br> </br>

Tengo que admitir que acabo de notar que fui un poquito perezosa y en el mismo taks hice la creación del resource group y el cluster de AKS. 

<b>Hagan lo que digo no lo que hago.</b>

Pero, este es un buen ejemplo para mostrar que podríamos ir ejecutando muchos comandos a la vez.
Como pueden ver, hicimos referencia tanto a variables como  ${var.aks_app_id}, como a inputs como ${input.Node_Count}

<H4> Install Application </H4>

Mi cliente me pidió tambien que implementara una aplicacion demo en este AKS Cluster. 


![image](https://user-images.githubusercontent.com/51407995/163285412-7f24534c-c9e0-4891-be09-07d27178c6f0.png)


En este caso si hice uso de las precondiciones, es decir este paso solamente se ejcuta si el output del paso anterior es completed.

Por lo demás, estoy haciendo login en el cluster recien creado y le estoy enviando la creacion de una aplicacion predefinida que esta publicada, pero bien esta podria ser una entrada más segun nuestro caso.

