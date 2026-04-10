# **Físicas en Unity**

Unity cuenta con un motor de físicas que permite simular el comportamiento de objetos en un entorno 3D. Este motor de físicas es muy potente y permite simular una gran cantidad de efectos físicos, como gravedad, colisiones, fricción, etc.  

El motor interpreta los objetos y es capaz de aplicarles gravedad, fuerzas externas, peso , fricción, detectar colisiones y más. En concreto Unity nos ofrece dos motores independientes, uno pensado para los objetos en 3D y otro pensado para el 2D. 

Empezaremos viendo la configuración global de estas físicas, para acceder a ellas debemos hacer clic en **Edit → Project Settings → Physics/Physics 2D** (para el 3D y el 2D respectivamente). Existe una gran variedad de parámetros a configurar, pero a nivel básico podremos modificar la fuerza de la gravedad (la cuál por defecto es la de La Tierra) y el Layer Collision Matrix o matriz de colisiones. En esta matriz seleccionaremos pues, entre qué capas se detectan colisiones y en cuáles no. 

Para trabajar con físicas en Unity debemos familiarizarnos principalmente con dos componentes, por un lado el Rigidbody o cuerpo rígido, que es el componente encargado de transformar al objeto en una entidad controlada por las físicas o a la que las físicas le afectan por así decirlo.Y por otro lado,  el Collider o colisionador, que se encarga de delimitar las zonas del objeto en las que se pueden detectar las colisiones, existen varios tipos de colliders que mayormente son distintas formas geométricas que seleccionaremos según se adapten mejor al objeto en concreto que estamos tratando. Para la gran mayoría de todos estos componentes existe también su versión en dos dimensiones.  

## **Rigidbody**

El **RigidBody** es un componente que nos permite definir varias propiedades clave para las físicas del juego. En primer lugar nos permite definir la masa del objeto, en kilogramos. La masa afectará sobre todo a las colisiones con otros objetos. También podremos definir el **Drag** o resistencia a las fuerzas, como a la gravitacional por ejemplo, que tendrá el objeto. 

Tenemos también una propiedad booleana para definir si el objeto se verá afectado por la gravedad o no (**Use Gravity**). Tenemos también otro booleano para definir si queremos que el objeto entero se vea afectado por el motor de físicas o no, es la propiedad **Is** **Kinematic** o es cinemático, esta propiedad tiene sentido activarla en un objeto que queremos que permanezca inmóvil aunque otro impacte contra él, es decir, que no se vea afectado por las fuerzas de las colisiones o de la gravedad, etc. 

Por último contamos también con una propiedad llamada **Constraints** que nos servirá para congelar algún eje en concreto de la posición o la rotación del objeto si no queremos permitir que se mueva en alguna dirección concreta. 

### Propiedades más relevantes de un Rigidbody

- **Mass**: La masa del objeto en kilogramos.
- **Drag**: La resistencia a las fuerzas que afectan al objeto.
- **Angular Drag**: La resistencia a las fuerzas de rotación que afectan al objeto.
- **Use Gravity**: Si el objeto se ve afectado por la gravedad.
- **Is Kinematic**: Si el objeto es cinemático y no se ve afectado por las físicas.
- **Constraints**: Las restricciones de movimiento del objeto. Esto nos sirve para congelar algún eje en concreto de la posición o la rotación del objeto si no queremos permitir que se mueva en alguna dirección concreta.
- **Interpolate**: La interpolación de la posición y rotación del objeto.
- **Collision Detection**: La detección de colisiones del objeto. 
    - **Discrete**: La detección de colisiones se realiza de forma discreta.
    - **Continuous**: La detección de colisiones se realiza de forma continua.
    - **Continuous Dynamic**: La detección de colisiones se realiza de forma continua dinámica.
    - **Continuous Speculative**: La detección de colisiones se realiza de forma continua especulativa.

## **Collider**

El **Collider** es un componente que se utiliza para definir la forma y tamaño de un objeto en la escena para detectar colisiones. Existen varios tipos de colliders en Unity, como:    

- **Box Collider**: Un cubo 3D.
- **Sphere Collider**: Una esfera 3D.
- **Capsule Collider**: Una cápsula 3D.
- **Mesh Collider**: Un collider basado en la geometría del modelo 3D.

Los colliders se utilizan para detectar colisiones entre objetos y permitir que interactúen entre sí en la escena. Para juegos 2D, se utilizan colliders 2D en lugar de colliders 3D. Existen varios tipos de colliders 2D en Unity, como:  

- **Box Collider 2D**: Un rectángulo 2D.
- **Circle Collider 2D**: Un círculo 2D.
- **Polygon Collider 2D**: Un polígono 2D.
- **Edge Collider 2D**: Un borde 2D.
- **Capsule Collider 2D**: Una cápsula 2D.

Los colliders 2D se utilizan para detectar colisiones entre objetos en un entorno 2D.

Para que un objeto al que le aplicamos físicas pueda colisionar con otro, ambos deben tener un **Collider**. Por ejemplo, para que el personaje principal de un juego de plataformas pueda colisionar con el suelo y no se hunda en la escena, el suelo debe tener también un Collider con el que impacte el Collider del personaje. 

Todos los Colliders poseen un parámetro llamado **IsTrigger**, que, si está activado, hace que el objeto funcione como un disparador que se lanza cuándo otro collider entra en colisión con él, pero sin ofrecer resistencia, simplemente funciona como disparador, ya no hay colisión si no que hay un aviso del contacto de ambos Colliders. Esto se usa por ejemplo para crear Checkpoints. 

A los Colliders también podemos agregarles materiales de tipo físico o **Physic Material**. Estos materiales podemos crearlos desde **Project → Create → Physic Material** y asignarlos al componente **Collider** en su propiedad **Material**. Estos materiales poseen unos parámetros que pueden dotar a nuestro objeto de más realismo, como son la fricción dinámica y estática o el rebote entre otros. 

También podemos modificar mucho más las físicas desde código, utilizando scripts que se encarguen de ello, para modificar las fuerzas, velocidades, aceleraciones, etc, en tiempo de ejecución. 

### Propiedades más relevantes de un Collider

- **Is Trigger**: Si el collider funciona como un disparador. Esto nos sirve para crear Checkpoints, por ejemplo.
- **Provides Contacts**: Si el collider proporciona contactos. 
- **Material**: El material físico del collider.
- **Center**: El centro del collider en su espacio local. Se especifica en coordenadas X, Y, Z. Es decir, usa un Vector3.
- **Size**: El tamaño del collider en su espacio local. Se especifica en coordenadas X, Y, Z. Es decir, usa un Vector3.

### Matriz de colisiones

La matriz de colisiones es una herramienta que nos permite definir qué capas de colisión interactúan entre sí. Esto es útil para controlar qué objetos colisionan entre sí y cuáles no. 

Para acceder a la matriz de colisiones en Unity, debemos ir a **Edit → Project Settings → Physics** o **Physics 2D** y buscar la sección **Layer Collision Matrix**. Aquí podremos ver todas las capas de colisión y definir si colisionan entre sí o no.   

<figure>
 <img src="img/collision_matrix.png" alt="Matriz de colisiones en Unity">
 <figcaption>Matriz de colisiones en Unity</figcaption>
</figure>

## **Ejemplo de uso de Rigidbody y Collider**

Vamos a ver un ejemplo sencillo de cómo utilizar un **Rigidbody** y un **Collider** en Unity. Para ello, vamos a crear un cubo que caiga por la gravedad y colisione con un suelo.  

1. Crearemos un nuevo proyecto en Unity y crearemos un nuevo GameObject 3D en la escena. Para ello, haremos clic en **GameObject → 3D Object → Cube**.
2. Seleccionaremos el cubo en la jerarquía y añadiremos un componente **Rigidbody**. Para ello, haremos clic en **Add Component → Physics → Rigidbody**.
3. Añadiremos un componente **Box Collider** al cubo. Para ello, haremos clic en **Add Component → Physics → Box Collider**.
4. Crearemos un suelo en la escena. Para ello, haremos clic en **GameObject → 3D Object → Plane**.
5. Añadiremos un componente **Box Collider** al suelo. Para ello, haremos clic en **Add Component → Physics → Box Collider**.
6. Ajustaremos la posición del suelo para que esté debajo del cubo.

Una vez hecho esto, si ejecutamos la escena, veremos cómo el cubo cae por la gravedad y colisiona con el suelo. Esto es debido a que hemos añadido un **Rigidbody** al cubo para que sea afectado por la gravedad y un **Collider** al cubo y al suelo para que colisionen entre sí.    

Este es un ejemplo muy sencillo de cómo utilizar un **Rigidbody** y un **Collider** en Unity. A partir de aquí, podemos crear escenas más complejas con objetos que interactúen entre sí y simulen comportamientos físicos realistas.   

En resumen, las físicas en Unity son un aspecto fundamental a la hora de crear juegos y aplicaciones interactivas. Con los componentes **Rigidbody** y **Collider**, podemos simular el comportamiento de objetos en un entorno 3D y crear experiencias interactivas y realistas para los usuarios. 
