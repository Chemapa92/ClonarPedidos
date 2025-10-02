# Clona Pedido – Guía Funcional (Visual)

## ¿Qué hace?
Clona un pedido (`Order__c`) y todas sus líneas, creando un nuevo pedido listo para editar y enviar.

## ¿Dónde está el botón?
- **Label**: "Clonar Pedido"
- **Ubicación**: Página de detalle de `Pedido` (Order__c)
- **Layout clásico**: `Order__c-Formato Pedido.layout`
- **Lightning Record Page**: `Pedido_P_gina_de_registro.flexipage`
- **Comportamiento**: Se abre en ventana existente sin sidebar ni header
- **Al pulsarlo**: Se abre una pantalla intermedia y en segundos verás el nuevo pedido

## Flujo (alto nivel)
```mermaid
flowchart LR
A[Pedido actual] -->|Pulsar 'Clonar Pedido'| B[Procesar clonación]
B --> C[Crear Nuevo Pedido]
C --> D[Copiar líneas]
D --> E[Navegar al nuevo Pedido]
```

## ¿Qué copia y qué resetea?
- **Copia**: cuenta, almacén, forma de pago, transportista, plataforma, proforma, serie, y todas las líneas con cantidades y precios.
- **Resetea automáticamente** en el nuevo pedido:
  - Número de pedido A3 (OrderNumber__c) → null
  - Nº de factura (InvoiceNumber__c) → null
  - Nº de albarán (DeliveryNoteNumber__c) → null
  - Fecha de carga (FreightDate__c) → null
  - Estado A3 (A3Status__c) → null
  - Estado Planta (PlantStatus__c) → null
  - Estado general → "Nuevo"
  - Fecha de pedido → hoy (DateTime)

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
- **Permisos**: "No tiene permisos para crear pedidos" si falta Create en Order__c
- **Pedido no encontrado**: "Error al obtener el pedido" si el ID es inválido
- **Error de creación**: "Error al crear el pedido clonado" si falla la inserción
- **Error de líneas**: "Error al clonar las líneas de pedido" si falla la clonación de líneas
- **Sin líneas**: Si el pedido original no tiene líneas, se clona solo la cabecera sin errores

## Buenas prácticas
- Revisa las fechas y el estado antes de enviarlo a planta.
- Verifica cantidades y precios tras clonar.

## ¿A quién acudir si falla?
- Equipo Salesforce/IT. Indica el enlace del pedido original y la hora en la que intentaste clonar.
