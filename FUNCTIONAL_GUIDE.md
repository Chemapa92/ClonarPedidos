# Clona Pedido – Guía Funcional (Visual)

## ¿Qué hace?
Clona un pedido (`Order__c`) y todas sus líneas, creando un nuevo pedido listo para editar y enviar.

## ¿Dónde está el botón?
- En la página de detalle de `Pedido` (Order__c), botón: "Clonar Pedido".
- Al pulsarlo, se abre una pantalla intermedia y en segundos verás el nuevo pedido.

## Flujo (alto nivel)
```mermaid
flowchart LR
A[Pedido actual] -->|Pulsar 'Clonar Pedido'| B[Procesar clonación]
B --> C[Crear Nuevo Pedido]
C --> D[Copiar líneas]
D --> E[Navegar al nuevo Pedido]
```

## ¿Qué copia y qué resetea?
- Copia: cuenta, almacén, forma de pago, transportista, y todas las líneas con cantidades y precios.
- Resetea automáticamente en el nuevo pedido:
  - Número de pedido A3 (OrderNumber__c)
  - Nº de factura y Nº de albarán
  - Fecha de carga
  - Estado: pasa a "Nuevo"
  - Fecha de pedido: hoy

## Casos típicos de uso
- Repetir un pedido habitual para el mismo cliente, ajustando solo cantidades/fechas.
- Evitar crear el pedido desde cero cuando solo cambian detalles menores.

## Ejemplo (antes/después)
| Concepto | Pedido original | Pedido clonado |
|---|---|---|
| Estado | Enviado a Planta | Nuevo |
| Fecha pedido | 2025-09-15 | Hoy |
| Nº Factura | F-12345 | (vacío) |
| Líneas | 3 líneas | 3 líneas |

## Mensajes y errores
- Si te falta permiso para crear pedidos o líneas, verás un aviso de error.
- Si el pedido original no tiene líneas, se clona la cabecera sin errores.

## Buenas prácticas
- Revisa las fechas y el estado antes de enviarlo a planta.
- Verifica cantidades y precios tras clonar.

## ¿A quién acudir si falla?
- Equipo Salesforce/IT. Indica el enlace del pedido original y la hora en la que intentaste clonar.
