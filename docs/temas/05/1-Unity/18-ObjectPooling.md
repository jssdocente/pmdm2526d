# **Object Pooling**

El Object Pooling es un patrón de diseño que se utiliza para gestionar la creación y destrucción de objetos en un juego. En lugar de crear y destruir objetos constantemente, lo que puede ser costoso en términos de rendimiento, se crean un número fijo de objetos al inicio del juego y se reutilizan a medida que se necesitan.    

Este enfoque es especialmente útil en juegos donde se generan muchos objetos de forma dinámica, como balas, enemigos o efectos visuales.    
El Object Pooling ayuda a reducir la carga en el recolector de basura y mejora el rendimiento general del juego.

## **¿Cómo funciona el Object Pooling?**

El Object Pooling funciona creando un grupo de objetos (pool) que se mantienen en memoria y se reutilizan cuando son necesarios. Cuando un objeto es necesario, se toma del pool y se activa. Cuando ya no se necesita, se desactiva y se devuelve al pool para su reutilización.
Esto evita la sobrecarga de crear y destruir objetos constantemente, lo que puede causar problemas de rendimiento, especialmente en dispositivos móviles o en juegos con muchos objetos en pantalla.

## **Implementación del Object Pooling en Unity**

Para implementar el Object Pooling en Unity, puedes seguir estos pasos: 

1. **Crear una clase de Pool**: Esta clase se encargará de gestionar el pool de objetos. Debe contener métodos para inicializar el pool, obtener un objeto del pool y devolver un objeto al pool.   
2. **Crear un prefab**: Crea un prefab del objeto que deseas gestionar con el pool. Este prefab se instanciará y se reutilizará a lo largo del juego.   
3. **Inicializar el pool**: En el método `Start` de tu script, inicializa el pool creando un número fijo de instancias del prefab y desactivándolas.    
4. **Obtener y devolver objetos**: Cuando necesites un objeto, llama al método del pool para obtener uno. Cuando termines de usarlo, devuélvelo al pool.    
5. **Activar y desactivar objetos**: Asegúrate de activar el objeto cuando lo obtienes del pool y desactivarlo cuando lo devuelves. 
6. **Ejemplo de uso**: Aquí tienes un ejemplo de cómo implementar el Object Pooling en Unity:

```csharp
using System.Collections.Generic;
using UnityEngine;

public class ObjectPool : MonoBehaviour
{
    public GameObject prefab; // Prefab del objeto a gestionar
    public int poolSize = 10; // Tamaño del pool

    private List<GameObject> pool; // Lista que contiene los objetos del pool

    void Start()
    {
        // Inicializar el pool
        pool = new List<GameObject>();
        for (int i = 0; i < poolSize; i++)
        {
            GameObject obj = Instantiate(prefab);
            obj.SetActive(false); // Desactivar el objeto al inicio
            pool.Add(obj); // Añadir el objeto al pool
        }
    }

    // Método para obtener un objeto del pool
    public GameObject GetObject()
    {
        foreach (GameObject obj in pool)
        {
            if (!obj.activeInHierarchy) // Comprobar si el objeto está inactivo
            {
                obj.SetActive(true); // Activar el objeto
                return obj; // Devolver el objeto activo
            }
        }
        return null; // Si no hay objetos disponibles, devolver null
    }

    // Método para devolver un objeto al pool
    public void ReturnObject(GameObject obj)
    {
        obj.SetActive(false); // Desactivar el objeto
    }
}
```

En este ejemplo, la clase `ObjectPool` gestiona un pool de objetos del prefab especificado. Al iniciar el juego, se crean un número fijo de instancias del prefab y se desactivan. Cuando necesitas un objeto, llamas al método `GetObject`, que devuelve un objeto activo del pool. Cuando terminas de usar el objeto, llamas al método `ReturnObject` para devolverlo al pool y desactivarlo.
Puedes usar este patrón para gestionar cualquier tipo de objeto en tu juego, como balas, enemigos o efectos visuales. Simplemente crea un prefab del objeto que deseas gestionar y utiliza la clase `ObjectPool` para obtener y devolver objetos según sea necesario.

## **Otro escenario de ejemplo**

Supongamos que queremos instanicar obstáculos, enemigos o proyectiles cada cierto tiempo. 

En lugar de usar:

```csharp
Instantiate(bulletPrefab);
```

Y después:

```csharp
Destroy(obstacle, 2f);
```

Usaremos un **poll de objetos** que los mantiene inactivos y listos para reutilizarse. 

Estructuraremos la situación con 3 archivos:

- **ObjectPooler.cs** : Clase que gestiona el pool de objetos:

```csharp title=ObjectPooler.cs
using System.Collections.Generic;
using UnityEngine;

public class ObjectPooler : MonoBehaviour
{
    public static ObjectPooler Instance;

    public GameObject objectPrefab;
    public int poolSize = 10;

    private Queue<GameObject> pool = new Queue<GameObject>();

    private void Awake()
    {
        Instance = this;
    }

    private void Start()
    {
        for (int i = 0; i < poolSize; i++)
        {
            GameObject obj = Instantiate(objectPrefab);
            obj.SetActive(false);
            pool.Enqueue(obj);
        }
    }

    public GameObject GetFromPool()
    {
        if (pool.Count > 0)
        {
            GameObject obj = pool.Dequeue();
            obj.SetActive(true);
            return obj;
        }
        else
        {
            // Opcional: crecer dinámicamente
            GameObject obj = Instantiate(objectPrefab);
            obj.SetActive(true);
            return obj;
        }
    }

    public void ReturnToPool(GameObject obj)
    {
        obj.SetActive(false);
        pool.Enqueue(obj);
    }
}
```

- **ObstacleSpawner.cs** : Creador de obstáculos cada ciertos segundos:

```csharp title=ObstacleSpawner.cs
using UnityEngine;

public class ObstacleSpawner : MonoBehaviour
{
    public float spawnInterval = 2f;
    public Transform spawnPoint;

    private void Start()
    {
        InvokeRepeating(nameof(SpawnObstacle), 1f, spawnInterval);
    }

    void SpawnObstacle()
    {
        GameObject obj = ObjectPooler.Instance.GetFromPool();
        obj.transform.position = spawnPoint.position;
    }
}
```

- **PooledObject.cs**: Script para devolver al pool el objeto al colisionar:

```csharp title=PooledObject.cs
using UnityEngine;

public class PooledObject : MonoBehaviour
{
    public float lifetime = 3f;

    private void OnEnable()
    {
        Invoke(nameof(ReturnToPool), lifetime);
    }

    private void ReturnToPool()
    {
        ObjectPooler.Instance.ReturnToPool(gameObject);
    }

    private void OnDisable()
    {
        CancelInvoke(); // Evita llamadas si se desactiva antes
    }
}
```

### ¿Cómo usarlo en la escena?

1. Crea un Prefab del objeto (por ejemplo, un cuadrado con BoxCollider2D).

2. Añádele el script PooledObject.cs.

3. Crea un GameObject vacío llamado ObjectPooler:

    - Añade el script ObjectPooler.cs.

    - Asigna el prefab y tamaño.

4. Crea otro objeto llamado Spawner:

    - Añade el script ObstacleSpawner.cs.

    - Asigna un Transform como punto de aparición.

## **Ventajas del Object Pooling**

- **Rendimiento**: Reduce la sobrecarga de crear y destruir objetos constantemente, lo que mejora el rendimiento del juego. 
- **Menor uso de memoria**: Al reutilizar objetos, se reduce la presión sobre el recolector de basura, lo que puede ayudar a evitar problemas de rendimiento en dispositivos móviles o en juegos con muchos objetos en pantalla.
- **Facilidad de uso**: El Object Pooling es fácil de implementar y puede adaptarse a diferentes tipos de objetos y situaciones en el juego.
- **Flexibilidad**: Puedes ajustar el tamaño del pool según las necesidades de tu juego, lo que te permite equilibrar el rendimiento y el uso de memoria.