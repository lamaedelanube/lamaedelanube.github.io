<H1>Integrando vRealize Automation Cloud (o on-prem) con Terraform OSS</H1>

<b>La unión hace la fuerza</b>

Hay que admitirlo, Terraform es casi el standard de facto en lo que se refiere a infraestructura como Código. Y es que realmente tiene una librería bastante extensiva de providers (plugins) para las diferentes nubes y otras herramientas usadas normalmente.

En el caso de VMware, tenemos una selección  interesante de providers para diferentes de nuestras herramientas.

Entonces, si tanto vRrealize Automation como Terraform se enfocan en infraestructura como código, son competencia entre si?
<b>No exactamente</b>. Si bien existen muchos casos de uso en los que bien podriamos usar una herramienta o la otra, existen una buena cantidad de razones por las que podríamos querer usarlos combinados, por ejemplo, para aprovechar archivos de configuración de Terraform  que ya tengamos creados, pero sobre los que queramos poner políticas de gobernanza, o bien que queramos presentar de forma amigable a nuestros usuarios finales, habilitando tambien acciones de dia 2 para estos.

Otro caso de uso es la creación de objetos en nubes públicas que actualmente no existan dentro del catálogo de Cloud Assembly. O bien, para hacer una combinación de diferentes cloud templates, incluso combinando objetos de diferentes nubes en un solo template que puedan ser creadas en un mismo deployment.

Existen dos formas en las que podríamos integrar estos productos:



1. Utilizando el  <a href="https://registry.terraform.io/providers/vmware/vra/latest/docs">Terraform provider</a> de vRealize Automation

![diagrama integracion](https://user-images.githubusercontent.com/51407995/163233972-0032b75e-01d3-4add-8e08-6223f27d9615.png)


En este caso, la interfaz por así decirlo será Terraform, y este invocará a vRealize Automation. Esto no solamente para la creación de deployments a partir de cloud templates, sino para realizar acciones de administración como cloud accounts, storage profiles entre otros. 

La verdad que está muy interesante. Se ven bastantes funcionalidades para agregar una nueva infraestructura a aprovisionar, cloud zone, cloud account, network, storage etc. 

Voy a crear un caso de ejemplo y espero compartirlo proximamente.



2. Utilizar vRealize Automation para integrar archivos de configuración de Terraform.

![image](https://user-images.githubusercontent.com/51407995/163234712-5ca19ebf-13f9-4edc-ac70-baff674a8feb.png)

Este es el caso del que vamos a hablar en este momento.

<b>Requerimientos: </b>
1. Un Repositorio Git:
<b>vRealize Automation on-prem: </b>
- GitHub cloud, GitHub Enterprise on-premises
- GitLab cloud, GitLab Enterprise on-premises
- Bitbucket on-premises
<b>vRealize Automation Cloud </b>
- GitHub cloud
- GitLab cloud

Como se puede observar, la opción cloud solamente soporta las versiones cloud de Github y Gitlab, al menos por ahora.

<b>Como configurar este repositorio: </b>

A. Se necesita crear un directorio base, y dentro de ellos se creará un subdirectorio por cada archivo de configuración.

En cada folder iran los archivos .tf:

![image](https://user-images.githubusercontent.com/51407995/163235626-f0f193d2-f59f-4f9d-a05d-4e77a1bc9f67.png)

![image](https://user-images.githubusercontent.com/51407995/163235658-779b0f14-1b6b-4317-8041-4ca883f8e94d.png)

B. Se debe crear el <a href="https://docs.gitlab.com/ee/user/profile/personal_access_tokens.html">Access Token</a>  (en mi caso estoy usando Gitlab)

      
2. En el caso de la versión on-prem, se debe crear un terraform runtime. Esto es, se necesita un cluster de Kubernetes. Este deberá poder ser integrado con vRealize Automation. En el caso de la versión Cloud, no hace falta este cluster, se usa el K8s interno.
  Aqui una entrada aparte sobre como crear este  runtime: 
  
La verdad yo tampoco me explico mucho la razón para esta diferencia. 
  
  
3. En ambos casos, se debe identificar la versión de Terraform a utilizar y ya que vamos a mapear la misma desde su repositorio para linux.
Por defecto utilizarermos los de hashicorp.
Ejemplo: ttps://releases.hashicorp.com/terraform/0.13.6/terraform_0.13.6_linux_amd64.zip

<b>A la acción </b>
1. Integración del repositorio.
A. En Cloud Assembly, vamos a Integraciones - Agregar integracion - Y Seleccionamos la respectiva.


![image](https://user-images.githubusercontent.com/51407995/163239212-019c09da-bf51-45b5-9b20-941c803e854c.png)

Debemos introducir el Token antes generado, y hacer click en Validar. 

B. Ahora en esta misma pantalla vamos a projectos, agregar proyecto el proyecto que va a "hostear" los cloud templates en los que utilizaremos estos repositorios. Click en siguiente

Ahora vemos que tenemos un menu. Al inicio tenemos 3 opciones de tipo:

![image](https://user-images.githubusercontent.com/51407995/163239823-aadd2045-66e3-416c-8140-f6eb38626399.png)


En el mismo repositorio, pero diferente folder, podriamos tener tambien los cloud templates y ABX sincronizados. Pero en este caso vamos a seleccionar Terraform configuration

Llenaremos el resto de información según las caracteristicas de nuestro repositorio
![image](https://user-images.githubusercontent.com/51407995/163240172-fdc8b437-0f7b-4b34-a640-4ef8815c6118.png)


2. Configuración de Terraform version.
Para esto en Cloud Assembly - Infrastructura- Nos dirigimos a Versiones de Terraform.
Si la versión que necesitamos no existe ya, hacemos click en Nueva version.
Ponemos el número de la versión, su origen 

Veamos el repositorio de Hashicorp:

<img width="616" alt="image" src="https://user-images.githubusercontent.com/51407995/163242084-3fa48f14-b00a-484a-9ad0-d505c1f071a0.png">

en el, vamos a seleccionar el archivo para Linux AMD 64 y del archivo SHA256SUMS vamos a obtener el checksum requerido.

Este repositorio no nos pide autenticación, si estuvieramos ante una versión on-prem sin salida a internet, entonces podríamos descargar el .zip, ponerlo en un repositorio local e incluso usar autenticación básica.

No se les olvide habilitar la versión!!!


Y bueno, ya con esto estamos listos para utilizar los terraform configurations en nuestros cloud templates!



  
  
