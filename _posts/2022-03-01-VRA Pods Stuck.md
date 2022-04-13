
<H1>Pods stuck en estado terminating </h1>


Hoy iba a preparar una entrada sobre otro tema, pero me encontré mi laboratorio inservible.

Como cualquiera que use vRrealize Automation podrá haber aprendido de mala manera, no hay nada peor que reiniciar los appliances sin haber seguido el procedimiento recomendado de apagado del mismo, es decir:

``` 
/opt/scripts/svc-stop.sh
sleep 120
/opt/scripts/deploy.sh --onlyClean 
```

Pero bueno. Ya pasó. Ahora a resolverlo. Traté de ejecutar el deploy.sh pero no tuve éxito. Así que procedí a revisar el estado de los pods, y me encontré con solamente dos de ellos; y trabados en estado terminating:

La solución en realidad es bastante simple. Detener forzosamente los pods que nos están dando problema. Para ello ejecutamos:

```
kubectl -n prelude delete pod nombre-del-pod-a-borrar --force
```

Por supuesto, repetimos esto por todos los pods que necesitemos eliminar y verificamos que ya no queden pods pendientes con

```
kubectl get pods -n prelude
```
Y una vez con esto, ya podemos proceder a ejecutar el reinicio limpio de nuestro ambiente

```
/opt/scripts/deploy.sh
```
Nos disponemos a esperar una media hora y tendremos nuestro ambiente de vuelta.
