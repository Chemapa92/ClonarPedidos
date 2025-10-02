# Clona Pedido (Order__c) - Resumen funcional y técnico

## Qué es
Acción en la Lightning Record Page de `Order__c` que clona un pedido junto con todas sus líneas y redirige al nuevo registro. Está expuesta como botón personalizado (Web Link) y también hay variantes en LWC/Quick Action.

## Componentes principales
- Botón personalizado en `Order__c` (WebLink): apunta a VF `OrderClone` pasando `recordId`.
- Visualforce `OrderClone.page`: ejecuta `action` en el controlador y muestra mensajes mientras redirige.
- Apex `OrderCloneController` (objeto custom `Order__c` / `OrderItem__c`):
  - Método `cloneOrder(originalOrderId)` clona cabecera y líneas.
  - Método privado `cloneOrderLines(originalOrderId, clonedOrderId)` inserta copias de `OrderItem__c` apuntando al nuevo pedido.
  - `cloneOrder()` como Action VF: toma `recordId` de la URL, clona y hace redirect al nuevo Id.
  - Utilidades: `getOrderDetails`, diagnóstico `diagnoseOrderLines`, e invocable `cloneOrderInvocable`.
- LWC opcionales (interfaces modernas para accionar la clonación):
  - `orderCloneAction`, `orderCloneButton`: llaman a `OrderCloneController.cloneOrder` y navegan al nuevo registro.
  - `orderCloneQuickAction`: Quick Action modal que usa `OrderCloneQuickActionController.cloneOrderWithLines` (versión para objeto estándar `Order`).
- Servicios adicionales
  - `OrderCustomCloneService`: servicio genérico para `Order__c`/`OrderItem__c` que copia campos de forma segura (multidivisa, campos opcionales), usado por `cloneOrderCustomAction` (LWC) en una alternativa de acción.

## Flujo principal (botón Clonar Pedido en Order__c)
1) Usuario pulsa el botón "Clonar Pedido" en la página de `Order__c`.
2) WebLink abre VF `/apex/OrderClone?recordId=<Id>`.
3) VF `OrderClone.page` ejecuta el método `action` `cloneOrder()` del controlador `OrderCloneController`.
4) `OrderCloneController.cloneOrder(originalOrderId)`:
   - Verifica permiso de creación en `Order__c`.
   - Consulta la cabecera del pedido original con los campos necesarios.
   - Clona el SObject (`original.clone(false, true, false, false)`), normaliza campos: 
     - `OrderNumber__c = null`, `OrderDate__c = hoy`, `Status__c = 'Nuevo'`, limpia `A3Status__c`, `PlantStatus__c`, `DeliveryNoteNumber__c`, `InvoiceNumber__c`, `FreightDate__c`.
   - Inserta el nuevo `Order__c` y llama a `cloneOrderLines`.
5) `cloneOrderLines`:
   - Consulta `OrderItem__c` por `Pedido__c = originalOrderId`.
   - Clona cada línea, reasigna `Pedido__c = clonedOrderId`, inserta en bloque.
6) Devuelve el `Id` clonado y la página VF redirige a `/${clonedOrderId}`.

## Ubicaciones relevantes (referencias a código)

Botón en `Order__c`:
```2064:2073:force-app/main/default/objects/Order__c.object
        <fullName>Clonar_Pedido</fullName>
        <displayType>button</displayType>
        <linkType>url</linkType>
        <masterLabel>Clonar Pedido</masterLabel>
        <url>/apex/OrderClone?recordId={!Order__c.Id}</url>
```

Visualforce:
```1:6:force-app/main/default/pages/OrderClone.page
<apex:page controller="OrderCloneController" action="{!cloneOrder}">
    <apex:pageMessages />
    <div style="text-align: center; padding: 50px;">
        <h2>Clonando Pedido...</h2>
```

Controlador Apex (cabecera):
```6:21:force-app/main/default/classes/OrderCloneController.cls
public with sharing class OrderCloneController {
    @AuraEnabled(cacheable=false)
    public static String cloneOrder(String originalOrderId) {
        // permisos y query del pedido original
```

Normalización de campos y alta del nuevo pedido:
```35:49:force-app/main/default/classes/OrderCloneController.cls
Order__c clonedOrder = originalOrder.clone(false, true, false, false);
clonedOrder.OrderNumber__c = null;
clonedOrder.OrderDate__c = Date.today();
clonedOrder.Status__c = 'Nuevo';
insert clonedOrder;
```

Clonado de líneas:
```76:99:force-app/main/default/classes/OrderCloneController.cls
List<OrderItem__c> originalLines = [ ... WHERE Pedido__c = :originalOrderId ... ];
for (OrderItem__c originalLine : originalLines) {
    OrderItem__c clonedLine = originalLine.clone(false, true, false, false);
    clonedLine.Pedido__c = clonedOrderId;
}
insert clonedLines;
```

Variantes LWC (opcional, misma lógica de negocio):
```1:16:force-app/main/default/lwc/orderCloneAction/orderCloneAction.js
import cloneOrder from '@salesforce/apex/OrderCloneController.cloneOrder';
```

Quick Action para estándar `Order` (no `Order__c`):
```13:31:force-app/main/default/classes/OrderCloneQuickActionController.cls
public static CloneResult cloneOrderWithLines(String originalOrderId) { ... }
```

Servicio alternativo genérico:
```1:18:force-app/main/default/classes/OrderCustomCloneService.cls
public with sharing class OrderCustomCloneService {
    @AuraEnabled
    public static Id cloneOrder(Id recordId) { ... }
```

## Notas de re-implementación en producción
- Desplegar: `OrderClone.page`, `OrderClone.page-meta.xml`, `OrderCloneController.cls` (+ meta), y el WebLink `Clonar_Pedido` en `Order__c`.
- Asegurar que la Flexipage de `Order__c` muestre el botón `CustomButton.Order__c.Clonar_Pedido`.
- Permisos: los perfiles/PS que usarán la acción deben tener Create en `Order__c` y `OrderItem__c`.
- Si se usan las variantes LWC/Quick Action, desplegar los LWC y los controladores asociados y añadir la acción a la página.

## Consideraciones
- Seguridad: uso de `WITH SECURITY_ENFORCED` en consultas y verificación de `isCreateable`.
- Multidivisa y campos opcionales: el servicio `OrderCustomCloneService` maneja copias seguras si se requiere más flexibilidad.


