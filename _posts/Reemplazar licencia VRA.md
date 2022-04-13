
<H1>Reemplazar la licencia de VRA </h1>


Típico que tienes que hacer algo en VRA y te encuentras el siguiente mensaje.

![image](https://user-images.githubusercontent.com/51407995/163228905-14b4d8ff-e5ae-4f84-bce1-6e52148194b1.png)


Bueno, realmente es algo que no deberia ser tan común, y por lo mismo no vamos a tener tan presente como cambiar la licencia en VRA. Que, a diferencia de la mayoria de productos, no tiene una opción en su consola para realizar esta tarea.

Recordemos TODA tarea de administración de vRealize Automation debería hacerse por medio de Lifecycle Manager. Para esto está y es la forma simple de hacerlo

A. Agregar la licencia en locker

1. Iniciar sesión en lifecycle manager
2. Ir a locker – licencias 

![image](https://user-images.githubusercontent.com/51407995/163228999-82c32f40-85dc-410f-b639-79760753eef9.png)

3. Agregar licencia manualmente y asignar la nueva licencia que tengamos. click en validar y luego en add.

B. Asignar la licencia al producto

1. Seleccionar el tab de ambientes y alli ver detalles.
2. Buscar el producto vRealize Automation.
3. Alli ir al (….) y hacer click en agregar licencia.
4. Cuando nos muestra la licencia anterior damos click en siguiente y a continuación, seleccionamos la nueva licencia.

5. De nuevo nos va a pedir marcar la licencia que deseamos reemplazar.

Pero, Y si no hay LCM?

Este no debería ser un caso productivo y realmente recomandaria instalar el LCM e importar el ambiente existe manualmente. Pero bueno, digamos que es el caso. Tambien podemos entonces agregar la licencia directamente desde la linea de comando de VRA.

1. Acceder por SSH a un appliance de VRA.

3. Revisar la licencia que existe actualmente: 

```
vracli license current
```    
    ![image](https://user-images.githubusercontent.com/51407995/163229110-d63d54b6-85e9-4357-af03-01d0c0a7a96d.png)


3. Agregar la nueva licencia: 
```
vracli license add {licencia}
```
4. Y Borrar la anterior: 

```
vra license remove {licencia}
```
![image](https://user-images.githubusercontent.com/51407995/163229157-9bc03408-e111-4108-be5e-c7a9b976c7b6.png)

5. Reiniciar el appliance. 
<a href="https://docs.vmware.com/en/vRealize-Automation/8.6/Administering/GUID-99D06124-13F8-489A-B43C-EAEC3F4FE582.html"> Correctamente</a>

Y luego de esperar una media hora para que regrese, ya estará.


