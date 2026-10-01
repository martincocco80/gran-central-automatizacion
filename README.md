# Gran Central – Sistema de Presupuestos y Reclamos

Ecosistema de automatización con IA para una mueblería (ficticia). Trabajo final del curso de automatizaciones.

El sistema lee los mails de los clientes, los clasifica con IA, calcula presupuestos o valida reclamos, pide aprobación a una persona y recién ahí responde al cliente en el mismo hilo.

## Stack

| Categoría | Herramienta |
|---|---|
| Orquestador | n8n |
| Base de datos | Notion (8 tablas vinculadas) |
| IA | OpenAI gpt-oss-20b vía Groq |
| Canal de salida | Gmail (respuesta en el hilo y aprobación humana) |

## Archivos

- `Gran_Central_workflow.json`: blueprint del flujo de n8n.
- `Diagrama_Arquitectura_Gran_Central.pdf`: diagrama de arquitectura.
- `diagrama_arquitectura.png`: el mismo diagrama en imagen.

## Cómo funciona

1. **Entrada:** el trigger de Gmail toma solo los mails a `+grancentral` y descarta los que manda el propio sistema (filtro anti-bucle).
2. **Clasificación:** la IA devuelve un JSON validado con el tipo (Presupuesto, Reclamo o Sin clasificar) y los datos extraídos.
3. **Ruteo:** el router manda cada consulta a presupuestos, reclamos o revisión manual.
4. **Presupuestos:** si faltan datos va a revisión manual. Si no, se calcula el precio con código (no con IA), la IA redacta el texto y se guarda en Notion.
5. **Reclamos:** se valida el número de pedido. Si existe, se crea el reclamo con motivo y gravedad. Si no existe, va a revisión manual.
6. **Aprobación humana (HITL):** se envía un mail al responsable y la ejecución queda en pausa hasta que aprueba o rechaza (máximo 48 h).
7. **Respuesta:** si se aprueba, se responde al cliente en el mismo hilo (Thread ID). Si se rechaza, no se envía nada.
8. **Errores:** si falla la IA (después de 3 reintentos), si el material no existe o si falla Notion, se registra en el Log de errores.

## Cómo importarlo

1. En n8n: **Workflows → Import from file** y elegir `Gran_Central_workflow.json`.
2. Asignar las credenciales de Gmail, Notion y Groq.
3. En el nodo **Config – Parámetros**, cambiar el mail del aprobador y las horas de espera.

## Documentación completa

[PEGAR ENLACE DE LA PÁGINA PÚBLICA DE NOTION]
