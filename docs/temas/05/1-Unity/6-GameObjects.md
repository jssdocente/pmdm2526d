# **5.6 GameObjects y sus componentes básicos en Unity**

## **¿Qué es un GameObject?**

Un **GameObject** es un objeto que se puede colocar en una escena de Unity. 

Puede representar cualquier cosa, desde un personaje, un enemigo, un objeto interactivo, una cámara, una luz, un efecto especial, etc.

Los GameObjects son la base de cualquier escena de Unity y se utilizan para construir el mundo del juego.

## **Componentes de un GameObject**

Los GameObjects en Unity están compuestos por una serie de **componentes** que definen su comportamiento y apariencia.

Algunos de los componentes más comunes que se pueden añadir a un GameObject son:

- **Transform**: Define la posición, rotación y escala del GameObject en el espacio 3D.
- **Mesh Renderer**: Renderiza un modelo 3D en la escena.
- **Collider**: Define la forma y tamaño del objeto para detectar colisiones.
- **Rigidbody**: Aplica físicas al objeto para simular su movimiento.
- **Script**: Permite añadir comportamiento programado al GameObject.

## Crear un GameObject en Unity

Para crear un GameObject en Unity, simplemente hay que hacer clic derecho en la jerarquía de la escena y seleccionar la opción "Create Empty".

Esto creará un GameObject vacío en la escena que se puede utilizar como base para añadir componentes y construir el objeto deseado.

También se pueden crear GameObjects a partir de modelos 3D, luces, cámaras, etc., arrastrándolos desde el explorador de assets a la escena.

## **Jerarquía de GameObjects**

Los GameObjects en Unity se organizan en una **jerarquía** que define las relaciones entre ellos.

Un GameObject puede tener **hijos**, que son otros GameObjects que están asociados a él y se mueven y rotan con él.

Esto permite crear estructuras complejas y organizar los elementos de la escena de forma jerárquica.

## Transform Component

El **Transform** es uno de los componentes más importantes de un GameObject en Unity.

Define la **posición**, **rotación** y **escala** del objeto en el espacio 3D.

- **Posición**: Define la posición del objeto en el espacio 3D en coordenadas X, Y, Z.
- **Rotación**: Define la orientación del objeto en el espacio 3D en ángulos de Euler.
- **Escala**: Define el tamaño del objeto en el espacio 3D en coordenadas X, Y, Z.

El Transform es el componente que se utiliza para mover, rotar y escalar un GameObject en la escena.

## **Mesh Renderer Component**

El **Mesh Renderer** es un componente que se utiliza para renderizar un modelo 3D en la escena de Unity.

Se compone de dos partes principales:

- **Mesh**: Define la geometría del objeto en forma de malla 3D.
- **Material**: Define la apariencia visual del objeto, como texturas, colores, brillos, etc.

El Mesh Renderer es el componente que se utiliza para visualizar los objetos 3D en la escena y aplicarles materiales para darles apariencia.

## **Collider Component**

El **Collider** es un componente que se utiliza para definir la forma y tamaño de un objeto en la escena para detectar colisiones.

Existen varios tipos de colliders en Unity, como:

- **Box Collider**: Un cubo 3D.
- **Sphere Collider**: Una esfera 3D.
- **Capsule Collider**: Una cápsula 3D.
- **Mesh Collider**: Un collider basado en la geometría del modelo 3D.

Los colliders se utilizan para detectar colisiones entre objetos y permitir que interactúen entre sí en la escena.

Para juegos 2D, se utilizan colliders 2D en lugar de colliders 3D.

Existen varios tipos de colliders 2D en Unity, como:

- **Box Collider 2D**: Un rectángulo 2D.
- **Circle Collider 2D**: Un círculo 2D.
- **Polygon Collider 2D**: Un polígono 2D.

Los colliders 2D se utilizan para detectar colisiones entre objetos en un entorno 2D.

## **Rigidbody Component**

El **Rigidbody** es un componente que se utiliza para aplicar físicas a un objeto en la escena.

Permite simular el movimiento y la interacción física de un objeto con otros objetos en la escena.

Al añadir un Rigidbody a un GameObject, este se verá afectado por la gravedad, la fricción, la velocidad, la masa, etc.

Los Rigidbody se utilizan para simular el movimiento realista de los objetos en la escena y permitir que interactúen entre sí de forma física.

## **Script Component**

El **Script** es un componente que se utiliza para añadir comportamiento programado a un GameObject en Unity.

Permite definir la lógica y el comportamiento del objeto mediante código en C#.

Los Scripts se utilizan para controlar la interacción del objeto con el jugador, el entorno, otros objetos, etc.

Al añadir un Script a un GameObject, se puede programar su comportamiento y hacer que responda a eventos, colisiones, entradas del jugador, etc.
