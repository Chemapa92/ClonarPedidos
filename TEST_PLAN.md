# Plan de Pruebas – Clona Pedido

## Casos principales
1. Clonación de pedido con líneas
   - Dado un `Order__c` con 1+ `OrderItem__c`
   - Al pulsar `Clonar Pedido`
   - Entonces se crea un nuevo pedido con estado `Nuevo`, fecha de hoy y mismas líneas

2. Pedido sin líneas
   - Al clonar, se crea cabecera sin líneas y no produce error

3. Permisos insuficientes
   - Usuario sin Create en `Order__c` o `OrderItem__c`
   - Debe mostrar error de permisos (Apex exception controlada)

4. Campos reseteados
   - `OrderNumber__c`, `InvoiceNumber__c`, `DeliveryNoteNumber__c`, `FreightDate__c` deben quedar vacíos
   - `OrderDate__c` = hoy, `Status__c` = `Nuevo`

5. Redirección
   - Tras clonar, navegar al nuevo registro

## Datos de prueba
- Pedido con 2-3 líneas con precios/cantidades variadas
- Pedido sin líneas
- Usuario con y sin permisos de creación
