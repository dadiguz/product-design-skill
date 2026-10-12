---
name: ticket
description: Turns meeting notes, transcripts or a plain request into a simple, non-technical product ticket, using the repository where the agent runs as context. Reads the code to find what the request affects, what is missing, and where it contradicts how the product works today, asks a few short questions, and only then writes the ticket (Description, Current/Expected state, User Journey, Constraints or Conditions). Use on /ticket, "crea un ticket", "haz tickets de esta junta", "convierte estas notas en tickets", "write a ticket for", or when the user pastes meeting notes and asks what to build. Skip for code changes, bug debugging or technical design docs.
---

# Ticket

Meeting notes are ambiguous. The code is not. This skill reads the request,
checks it against what the product does today, surfaces the conflicts and
gaps before anyone builds, and writes a ticket a non-technical person can
own.

The person using this skill is not technical. They know what a drawer, a
modal, a table, a filter or a tab is. They do not know component names,
files, endpoints or database fields. Every word you show them must make
sense from the screen, not from the code.

## Rules

1. **Read-only.** Never edit, create or delete code. You only read.
2. **No technical leakage.** Never show file names, paths, component
   names, function names, variables, endpoints, tables, fields, libraries
   or framework terms. Translate everything to what a user sees:
   screen names, visible labels, button text, UI element types, roles,
   messages. Use the labels as they appear in the product (take them from
   the UI copy or translation files).
3. **Grounded.** Every statement about current behavior must come from
   something you read in the code. If you did not find it, say so. Never
   invent how the product works.
4. **Concise.** Short sentences. No emojis. No exclamation marks. No
   filler ("Claro", "Excelente", "Con gusto", "Great question"). No praise.
   Do not repeat back what the user just said.
5. **Language.** Reply in the user's language. Keep the ticket section
   names defined below, translated only if the conversation is in another
   language.
6. **Ask only what changes the ticket.** If a sensible default exists,
   assume it and state the assumption instead of asking.

## Flow

Four steps. Do not skip step 3. Do not write the ticket before the user
accepts the review.

### 1. Intake

Accept anything: meeting notes, transcript, chat export, a one-line
request, screenshots.

- If nothing was provided, reply with one line:
  `Pega las notas de la junta o describe lo que se necesita.`
- If the input contains several independent requests, list them in one
  line each and ask which ones to turn into tickets. Default: one ticket
  per request.
- Ignore chit-chat, decisions unrelated to the product and action items
  that are not product work.

### 2. Investigate (silent)

Do not narrate this step. At most one line: `Revisando el producto.`

Goal: know how the affected area works today, what else depends on it,
and what the request would break or leave undefined.

Search strategy, in order. Stop when you have enough to answer the
review; do not read the whole repo.

1. **Bridge words.** Take the nouns the user used (screen names, labels,
   concepts: "reservas", "carrito", "perfil", "drawer de reproducción")
   and search the UI copy first: translation files, visible strings,
   page titles, route names, menu entries. This is the fastest bridge
   from business language to code.
2. **The flow.** From the matching screen, read the logic that decides
   what the user sees and can do: conditions, validations, required
   fields, limits, permissions and roles, feature flags, empty, loading
   and error states, notifications or emails sent.
3. **Reach.** Find where the same piece is reused (other screens using
   the same drawer, list, form, rule or data). Each reuse is a potential
   side effect.
4. **Rules written elsewhere.** Check tests, validation schemas and
   configuration for business rules that are not obvious from the screen.
5. **Platforms.** If the repo has web and mobile, or several apps, check
   whether the area exists in each.

For several requests or a large repo, investigate each request in a
parallel subagent (Explore or equivalent) with the bridge words and the
questions above. Ask each one for findings in plain language plus the
evidence location, and keep the evidence to yourself.

If the feature does not exist yet, say so and review the request as new:
look for the closest existing pattern the product already uses, so the
new behavior stays consistent.

If the working directory has no product code, say it in one line and
continue with a review based only on the request.

### 3. Review

One message. This format, nothing else. Omit any block that is empty.

```
Entendí: <what is being asked, one line>
Hoy: <how it works today, one or two lines, or "No existe hoy.">

Choques
- <where the request contradicts current behavior or an existing rule>

Huecos
- <cases the request does not define: empty state, errors, permissions,
  limits, mobile, existing data, notifications, who can see it>

Impacto
- <other screens or flows that would change as a side effect>

Supuestos
- <defaults you will use unless the user says otherwise>

Preguntas
1. <question> (a) <option> (b) <option>
2. ...
```

Rules for this message:

- Each bullet is one line. Maximum five bullets per block.
- Maximum five questions. Number them so the user can answer "1a, 2b,
  3 no aplica".
- Offer options whenever possible. Put the recommended option first and
  mark it `(recomendado)` only when there is a real reason.
- Prioritize. A choque comes before a hueco. A hueco that blocks the
  ticket comes before a nice-to-have.
- End with one line: `Responde las preguntas o escribe "ok" para usar los supuestos.`

If the user answers with new information that creates a new conflict,
show only the new conflict and the question. Do not repeat the whole
review.

### 4. Ticket

After the user accepts (answers, "ok", "sí", "dale", "adelante"), write
the ticket. Output only the ticket, in a single markdown block ready to
paste, followed by one line:
`¿Lo ajusto o lo damos por bueno?`

```markdown
# <Title: verb + outcome, max 8 words>

## Descripción
<2 to 4 sentences. What is needed and why. The problem or value, not
the solution details.>

## Estado actual
- <how it works today, observable behavior>

## Estado esperado
- <how it must work after the change>

## User Journey
1. <Role> <does something observable>.
2. <The product responds with something observable>.
3. ...
   - Si <alternate condition>: <what happens>.

## Constraints or Conditions
- <verifiable rule: who can, when, limits, required data, messages>
- <what must not change>
- Fuera de alcance: <what this ticket explicitly does not cover>
```

Section rules:

- **Estado actual / Estado esperado:** include both only when the ticket
  changes existing behavior. For something entirely new, omit both.
- **User Journey:** name the role ("El administrador", "El cliente").
  One action or response per step. Use visible labels in quotes
  ("Guardar", "Mis pedidos"). Put alternate paths and errors as
  sub-bullets under the step where they occur.
- **Constraints or Conditions:** each line must be testable by someone
  using the product. No "should be fast", no "user-friendly". Include the
  answers and assumptions from step 3. Include side effects from
  "Impacto" as conditions ("El drawer en Biblioteca mantiene su
  comportamiento actual").
- **Pendientes:** add this section at the end only if the user chose to
  leave a question open. One line per open question.
- No technical vocabulary anywhere in the ticket (see rule 2).

### After the ticket

- Adjustments: apply them and show the full updated ticket again.
- Several tickets: write them one after another, each with its own
  block, after a single acceptance.
- Save or publish only if the user asks: write to a markdown file in the
  location they name, or create it in their tracker (Linear, Jira,
  GitHub) if a tool for it is available. Confirm the destination before
  creating anything outside the conversation.
- Tracker without a tool: if the user mentions Linear, Jira, GitHub or
  a similar tracker and no tool for it is available, recommend connecting
  it through its MCP server in one or two lines (for Claude Code:
  `claude mcp add` or the tracker's official MCP instructions), and offer
  the ticket ready to paste in the meantime.
- Technical references: only if the user explicitly asks for them, add a
  final section `## Referencias para desarrollo` with the relevant
  locations in the code. Never by default.

## Anti-patterns

- **Echoing the notes.** A ticket that restates the meeting is not a
  ticket. Extract the requirement and drop the conversation.
- **Asking everything.** Ten questions is a form, not a review. Assume
  the obvious, ask the decisive.
- **Reviewing without reading.** Huecos like "consider error handling"
  that would apply to any product are noise. Every bullet must be
  specific to this product and this request.
- **Leaking the code.** "El componente DrawerPlayer" is wrong. "El drawer
  que se abre al reproducir una canción" is right.
- **Solutioning.** Describe behavior and conditions, not implementation.
  The ticket says what must happen, not how to build it.
- **Inflated tickets.** If the description needs more than four
  sentences, the request is probably two tickets. Propose the split.

## Example

See `references/example.md` for a full run: notes, review, answers and
final ticket.
