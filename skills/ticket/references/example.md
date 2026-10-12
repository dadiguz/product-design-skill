# Example run

A music app. The user pastes notes from a product meeting.

## Input

```
/ticket
Junta producto 10/oct
- Ana: los usuarios se quejan de que al cerrar el drawer de la canción se
  pierde la cola. Queremos que la cola se guarde.
- Luis: y que se pueda reordenar arrastrando.
- Se habló de cambiar el logo, lo ve marketing.
```

## Step 1. Intake

The logo item is not product work for this repo. Two requests remain and
they touch the same area, so they stay in one ticket.

## Step 2. Investigate (internal, never shown)

Found in the code: the queue lives only while the player drawer is open
and is cleared on close. The same drawer opens from Inicio, Biblioteca and
Búsqueda. Free accounts have a limit of 50 songs in the queue. Reordering
exists on web only, through "Subir" and "Bajar" buttons. The queue has a "Vaciar cola" button. The mobile app
has no reorder option.

## Step 3. Review (what the user sees)

```
Entendí: la cola de reproducción debe conservarse al cerrar el drawer y poder reordenarse arrastrando.
Hoy: la cola se borra al cerrar el drawer. En web se reordena con los botones "Subir" y "Bajar"; en móvil no se puede reordenar.

Choques
- Arrastrar convive o reemplaza a "Subir" y "Bajar" en web; no está definido.

Huecos
- No se dice cuánto tiempo se conserva la cola (sesión, días, siempre).
- Cuentas gratuitas tienen límite de 50 canciones en la cola; no se dice si cambia.

Impacto
- El drawer se abre desde Inicio, Biblioteca y Búsqueda; el cambio aplica en los tres.

Supuestos
- Aplica en web y móvil.
- El límite de 50 canciones para cuentas gratuitas se mantiene.

Preguntas
1. ¿Cuánto tiempo se conserva la cola? (a) hasta cerrar sesión (b) siempre, hasta que el usuario la vacíe
2. En web, ¿arrastrar reemplaza a "Subir" y "Bajar"? (a) conviven (recomendado, accesibilidad) (b) se reemplazan

Responde las preguntas o escribe "ok" para usar los supuestos.
```

User answers: `1b, 2a`

## Step 4. Ticket

```markdown
# Conservar y reordenar la cola de reproducción

## Descripción
Los usuarios pierden su cola de reproducción cada vez que cierran el drawer de la canción y tienen que armarla de nuevo. Se necesita que la cola se conserve hasta que el usuario la vacíe y que pueda reordenarse arrastrando las canciones, en web y móvil.

## Estado actual
- Al cerrar el drawer de la canción, la cola se borra.
- En web, la cola se reordena solo con los botones "Subir" y "Bajar".
- En móvil no es posible reordenar la cola.

## Estado esperado
- La cola se conserva al cerrar el drawer, al cerrar la app y al cerrar sesión.
- En web y móvil, las canciones de la cola se pueden reordenar arrastrando.
- En web, "Subir" y "Bajar" siguen disponibles.

## User Journey
1. El usuario agrega canciones a la cola desde Inicio, Biblioteca o Búsqueda.
2. El usuario cierra el drawer de la canción.
3. El usuario vuelve a abrir el drawer; la cola aparece igual que la dejó.
4. El usuario mantiene presionada una canción de la cola y la arrastra a otra posición.
5. La cola muestra el nuevo orden y la reproducción sigue ese orden.
   - Si suelta la canción fuera de la cola: la canción regresa a su posición original.
6. El usuario toca "Vaciar cola"; la cola queda vacía.

## Constraints or Conditions
- La cola solo se borra cuando el usuario toca "Vaciar cola".
- El comportamiento es igual al abrir el drawer desde Inicio, Biblioteca o Búsqueda.
- Cuentas gratuitas mantienen el límite de 50 canciones en la cola.
- La canción que se está reproduciendo no se interrumpe al reordenar.
- En web, "Subir" y "Bajar" mantienen su comportamiento actual.
- Fuera de alcance: compartir colas entre dispositivos.
```

¿Lo ajusto o lo damos por bueno?
