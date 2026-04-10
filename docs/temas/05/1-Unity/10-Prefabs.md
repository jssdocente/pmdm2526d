# **Prefabs, ¿Qué son y cómo se usan?**

## **¿Qué es un prefab?**

Según vamos creando objetos, componentes y valores, llega un momento en el que es necesario crear varios idénticos. Por ejemplo, cuándo queremos crear una serie de enemigos iguales, o unos objetos que el personaje principal debe recolectar para ganar puntos, etc. Para ello, no tendría mucho sentido estar creando objetos a los que ir añadiendo los mismos componentes y sus propiedades iguales una y otra vez. Para esto existen los Prefabs, que, como su propio nombre indica, son elementos prefabricados, para poder reutilizarlos. 

Un prefab nos permite almacenar un GameObject con todos sus componentes y valores de sus propiedades y convertirlo en un Asset. Este Asset funcionará como una plantilla que podemos copiar de nuevo en cualquier momento o lugar de nuestra escena, creando una instancia del mismo. 

## **Crear un prefab**

Para crear un prefab, lo más sencillo que podemos hacer es generar un GameObject tal y como queremos que sea el prefab y, una vez lo tenemos configurado del todo, arrastrarlo desde la pestaña de la jerarquía a la pestaña de proyecto. Esta acción lo convierte automáticamente en un Asset. 

Una vez creado el Asset podemos editarlo a dos niveles, o bien editando el Asset, lo cuál hará que los cambios se vean reflejados en todas las instancias del mismo, o a nivel de la instancia en concreto, lo cuál modificará solamente la instancia editada.

Por ejemplo, si queremos crear un prefab con un enemigo, simplemente tenemos que seleccionar el enemigo en la escena y arrastrarlo a nuestra carpeta de Assets. 

## **Usar un prefab**

Para usar un prefab, simplemente tenemos que arrastrarlo desde nuestra carpeta de Assets a la escena.

Por ejemplo, si queremos añadir un enemigo a nuestra escena, simplemente tenemos que arrastrar el prefab del enemigo desde nuestra carpeta de Assets a la escena.

## **Editar un prefab**

Para modificar el Asset debemos seleccionar la opción **Prefab → Open** desde el inspector (teniendo seleccionada una instancia o el asset), o desde la jerarquía, haciendo clic en la pequeña flecha a la derecha de la instancia del mismo. Esto hace que entremos en el modo de edición. Cómo ya dijimos, si modificamos aquí los valores, estos cambios se verán afectados en todas las instancias de este prefab. 

Es posible que haya ocasiones en las que estemos modificando una instancia y nos demos cuenta de que los cambios que hemos hecho queremos que se apliquen a todas las instancias de ese prefab, para hacer esto podemos seleccionar desde el inspector la opción Prefab → Override y, tras abrirse el desplegable, seleccionar la opción Apply All, para que los cambios se apliquen a todas las instancias que se corresponden al mismo prefab que la que estábamos editando. También podemos seleccionar la opción Revert All en caso de haber hecho muchos cambios y habernos equivocado, si queremos deshacer todos estos cambios, con Revert All volveremos al estado original del Prefab en dicha instancia.

Otro de los principales usos que podemos darle a los prefabs es cuándo necesitamos agregar nuevos objetos durante la ejecución del juego que no estaban previamente colocados en la escena, como por ejemplo pueden ser trozos de madera al destruir un barril, la aparición de un enemigo aleatorio o las balas de un cañón o una pistola. Estos tan solo podemos agregarlos a la escena mediante código en un script, si previamente están almacenados como un prefab. 
