# ticket

Skill para agentes de código (Claude Code y compatibles) que convierte notas de juntas o una instrucción en un ticket de producto simple y no técnico, revisado contra el código del repositorio donde se ejecuta.

Antes de escribir el ticket, el skill lee el producto y te dice qué choca con lo que existe hoy, qué falta definir y qué otras partes se verían afectadas. Tú respondes unas pocas preguntas y recibes un ticket listo para pegar.

También responde preguntas sobre cómo funciona el producto hoy, sin tecnicismos.

No necesitas saber de código. Habla de pantallas, botones, drawers, tablas y filtros; el skill se encarga de traducirlo.

---

## Qué produce

Un ticket con estas secciones:

| Sección | Contenido |
|---|---|
| **Descripción** | Qué se necesita y por qué, en 2 a 4 frases. |
| **Estado actual** | Cómo funciona hoy. Solo aparece si el ticket cambia algo existente. |
| **Estado esperado** | Cómo debe funcionar después del cambio. Solo junto a Estado actual. |
| **User Journey** | Pasos numerados de lo que hace el usuario y lo que responde el producto, con caminos alternos. |
| **Constraints or Conditions** | Reglas verificables: quién puede, límites, mensajes, qué no debe cambiar y qué queda fuera de alcance. |
| **Pendientes** | Solo si decidiste dejar una pregunta abierta. |

El ticket nunca menciona archivos, componentes ni términos técnicos. Usa los nombres que se ven en pantalla.

---

## Instalación

**Claude Code (plugin)**

```
/plugin marketplace add dadiguz/product-design-skill
/plugin install product-design-skill@product-design-skill
```

**Manual (cualquier agente que lea skills)**

```bash
git clone https://github.com/dadiguz/product-design-skill
cp -r product-design-skill/skills/ticket ~/.claude/skills/ticket
```

Para otros agentes, copia la carpeta `skills/ticket` al directorio de skills que use tu agente.

---

## Uso

1. Abre el agente **en la carpeta del proyecto** sobre el que quieres el ticket. El skill solo conoce el código de la carpeta donde se ejecuta.
2. Escribe `/ticket` y pega lo que tengas: notas de la junta, una transcripción o una frase.

```
/ticket
Junta producto: al cerrar el drawer de la canción se pierde la cola.
Queremos que se guarde y que se pueda reordenar arrastrando.
```

También se activa sin el comando si pides algo como "haz tickets de estas notas" o "convierte esto en un ticket".

### El flujo

**0. Actualización.** Antes de empezar, el skill trae los últimos cambios de la rama principal (`main` o `master`) para revisar la versión más reciente del producto. Si estás en la rama principal sin cambios propios, la actualiza. Si estás en otra rama o tienes cambios sin guardar, no toca nada y revisa la versión remota directamente. Si no hay conexión, te avisa y usa la copia local.

**1. Entrada.** Si las notas traen varios pedidos, el skill los lista y te pregunta cuáles convertir. Ignora lo que no es trabajo de producto.

**2. Revisión del producto.** El skill busca en el código cómo funciona hoy esa parte: reglas, límites, permisos, estados vacíos y de error, y en qué otras pantallas aparece lo mismo. No modifica nada.

**3. Revisión contigo.** Recibes un solo mensaje corto:

```
Entendí: la cola debe conservarse al cerrar el drawer y poder reordenarse arrastrando.
Hoy: la cola se borra al cerrar el drawer. En móvil no se puede reordenar.

Choques
- Arrastrar convive o reemplaza a "Subir" y "Bajar" en web; no está definido.

Huecos
- No se dice cuánto tiempo se conserva la cola.

Impacto
- El drawer se abre desde Inicio, Biblioteca y Búsqueda; el cambio aplica en los tres.

Supuestos
- Aplica en web y móvil.

Preguntas
1. ¿Cuánto tiempo se conserva la cola? (a) hasta cerrar sesión (b) siempre
2. En web, ¿arrastrar reemplaza a "Subir" y "Bajar"? (a) conviven (recomendado) (b) se reemplazan

Responde las preguntas o escribe "ok" para usar los supuestos.
```

| Bloque | Significa |
|---|---|
| Choques | Lo que pides contradice algo que el producto hace hoy. |
| Huecos | Casos que tu pedido no define y alguien tendrá que decidir. |
| Impacto | Otras pantallas o flujos que cambiarían sin que lo hayas pedido. |
| Supuestos | Lo que el skill asumirá si no dices otra cosa. |
| Preguntas | Máximo cinco, numeradas y con opciones. |

**4. Respuesta.** Contesta en corto: `1b, 2a`. O escribe `ok` para aceptar los supuestos. Si algo de tu respuesta genera un nuevo choque, el skill pregunta solo eso.

**5. Ticket.** Recibes el ticket en un bloque listo para copiar. Pide ajustes en lenguaje normal ("quita el paso 4", "agrega que solo aplica a administradores") y se regenera completo.

### Preguntas sobre el producto

El skill también responde preguntas sobre cómo funciona el producto hoy, con el mismo lenguaje no técnico. No necesitas pedir un ticket.

```
/ticket ¿Un usuario gratuito puede descargar canciones?
```

```
No. La opción "Descargar" solo aparece en cuentas Premium.

- En cuentas gratuitas el botón se muestra bloqueado y abre la pantalla de planes.
- En web no existe la descarga para ningún plan.
```

- Responde primero sí, no o qué pasa, y luego las condiciones que importan: rol, plan, plataforma o estado.
- Si algo no existe o no está definido, lo dice. No adivina.
- Si la respuesta depende de algo fuera del código (datos, configuración externa), lo aclara.
- Si la respuesta muestra un hueco o algo que quieres cambiar, ofrece convertirlo en ticket con lo que ya revisó.
- Puedes seguir preguntando; mantiene el contexto.

### Opciones

Pídelas en la conversación cuando las necesites:

- **Guardar:** "guárdalo en `docs/tickets`" escribe el ticket como archivo markdown.
- **Publicar:** "créalo en Linear" (o Jira, GitHub) si tu agente tiene acceso a esa herramienta. El skill confirma el destino antes de crear algo. Si la herramienta no está conectada, el skill te recomienda conectarla por MCP (por ejemplo, el MCP oficial de Linear) y mientras tanto te deja el ticket listo para pegar.
- **Referencias técnicas:** "agrega referencias para desarrollo" añade al final una sección con las ubicaciones en el código, pensada para quien lo va a construir. Nunca se incluye por defecto.
- **Varios tickets:** si las notas tienen varios pedidos, se revisan juntos y se entregan uno tras otro.

---

## Recomendaciones

- **Pega las notas completas.** El skill filtra el ruido; lo que falte no lo puede adivinar.
- **Usa los nombres que ves en pantalla.** "La pestaña Mis pedidos" encuentra el lugar correcto más rápido que "la sección de compras".
- **Responde solo lo que sabes.** Si una pregunta no la puedes contestar, dilo; queda en Pendientes en lugar de inventarse.
- **Si es algo nuevo, dilo.** Si la función no existe, el skill lo indica y busca el patrón más parecido del producto para mantener consistencia.

---

## Estructura

```
skills/ticket/
  SKILL.md               instrucciones del skill
  references/example.md  ejecución completa de ejemplo
.claude-plugin/          manifiestos para instalar como plugin
```

Para cambiar el formato del ticket, el tono o las secciones, edita `skills/ticket/SKILL.md`.
