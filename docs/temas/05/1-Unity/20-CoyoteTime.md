# **El Coyote Time**

**Coyote Time**  es una técnica usada en juegos de plataformas para hacer más tolerante el salto. Permite al jugador saltar durante un breve instante después de haber salido de una plataforma, como si aún estuviera “en el aire”, como el Coyote de los Looney Tunes justo antes de caer.

## **¿Por qué usarlo?**

Sin esta técnica, el salto se siente rígido y frustrante. Con ella, el juego se siente más responsivo sin cambiar la lógica básica del salto.

## **Implementación**

Script de ejemplo:

```csharp title=PlayerControllerWithCoyote.cs	
using UnityEngine;

public class PlayerControllerWithCoyote : MonoBehaviour
{
    public float moveSpeed = 5f;
    public float jumpForce = 7f;
    public Transform groundCheck;
    public LayerMask groundLayer;
    public float coyoteTime = 0.2f;

    private Rigidbody2D rb;
    private bool isGrounded;
    private float coyoteTimer;

    void Awake()
    {
        rb = GetComponent<Rigidbody2D>();
    }

    void Update()
    {
        // Movimiento horizontal
        float moveInput = Input.GetAxisRaw("Horizontal");
        rb.velocity = new Vector2(moveInput * moveSpeed, rb.velocity.y);

        // Verifica si está en el suelo
        isGrounded = Physics2D.OverlapCircle(groundCheck.position, 0.1f, groundLayer);

        // Actualiza el temporizador de coyote
        if (isGrounded)
        {
            coyoteTimer = coyoteTime; // reinicia el tiempo si está en el suelo
        }
        else
        {
            coyoteTimer -= Time.deltaTime;
        }

        // Salto (se permite si aún está dentro del tiempo de coyote)
        if (Input.GetButtonDown("Jump") && coyoteTimer > 0)
        {
            rb.velocity = new Vector2(rb.velocity.x, jumpForce);
            coyoteTimer = 0f; // para que no pueda volver a saltar
        }
    }

    void OnDrawGizmosSelected()
    {
        // Dibuja una esfera para mostrar el punto de groundCheck
        if (groundCheck != null)
        {
            Gizmos.color = Color.yellow;
            Gizmos.DrawWireSphere(groundCheck.position, 0.1f);
        }
    }
}
```

## **¿Cómo prepararlo en la escena?**

1. Crea un GameObject con Rigidbody2D y BoxCollider2D.

    - Añade el script PlayerControllerWithCoyote.

2. Crea un hijo vacío llamado GroundCheck (posición justo debajo del personaje).

    - Así el raycast detecta el suelo.

3. Crea un suelo con BoxCollider2D y ponle el tag Ground o asígnalo a un Layer llamado "Ground".

4. En el Inspector del PlayerControllerWithCoyote:

    - Asigna el Transform del hijo GroundCheck.

    - Asigna el LayerMask al layer "Ground".

## **Resultado**

Cuando el jugador corre y cae de una plataforma, puede aún saltar durante 0.2 segundos. Esto mejora:

- Jugabilidad

- Sensación de control

- Frustración por errores milimétricos

