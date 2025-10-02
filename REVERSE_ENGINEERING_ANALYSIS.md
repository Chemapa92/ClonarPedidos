# Análisis de Ingeniería Inversa - Clona Pedido

## 🔍 **Flujo Completo Verificado**

### **Entrada Principal: WebLink "Clonar Pedido"**
```
Order__c Record Page → WebLink "Clonar_Pedido" → /apex/OrderClone?recordId={!Order__c.Id}
```

### **Procesamiento: Visualforce + Apex**
```
OrderClone.page (action="{!cloneOrder}") → OrderCloneController.cloneOrder()
```

### **Lógica de Clonación (OrderCloneController)**
1. **Validación de permisos**: `Schema.sObjectType.Order__c.isCreateable()`
2. **Query del pedido original**: 15 campos específicos con `WITH SECURITY_ENFORCED`
3. **Clonación de cabecera**: `clone(false, true, false, false)`
4. **Normalización de campos**:
   - `OrderNumber__c = null`
   - `OrderDate__c = DateTime.now()` ✅ **CORREGIDO**: Campo es DateTime
   - `Status__c = 'Nuevo'`
   - `A3Status__c = null`
   - `PlantStatus__c = null`
   - `DeliveryNoteNumber__c = null`
   - `InvoiceNumber__c = null`
   - `FreightDate__c = null`
5. **Inserción del pedido clonado**
6. **Clonación de líneas**: `cloneOrderLines(originalOrderId, clonedOrderId)`

### **Clonación de Líneas (OrderItem__c)**
1. **Query de líneas**: `WHERE Pedido__c = :originalOrderId`
2. **Clonación individual**: `clone(false, true, false, false)`
3. **Reasignación**: `clonedLine.Pedido__c = clonedOrderId`
4. **Inserción en bloque**: `insert clonedLines`

### **Redirección**
```
PageReference('/' + clonedOrderId) → Navegación al nuevo pedido
```

## 🏗️ **Arquitectura Multi-Implementación**

### **1. Implementación Principal (Order__c)**
- **WebLink**: `Clonar_Pedido` → VF `OrderClone.page`
- **LWC Button**: `orderCloneButton` (con diagnóstico)
- **LWC Action**: `orderCloneAction`
- **Controlador**: `OrderCloneController`

### **2. Implementación Alternativa (Order__c)**
- **LWC Custom Action**: `cloneOrderCustomAction`
- **Controlador**: `OrderCustomCloneService`
- **Características**: Clonado dinámico, detección de campos, multidivisa

### **3. Implementación Estándar (Order)**
- **LWC Quick Action**: `orderCloneQuickAction`
- **Controlador**: `OrderCloneQuickActionController`
- **Objeto**: `Order` estándar (no `Order__c`)

## ⚠️ **Problemas Detectados**

### **1. Error de Tipo de Dato** ✅ **CORREGIDO**
```apex
clonedOrder.OrderDate__c = DateTime.now(); // ✅ Campo es DateTime
```
**Solución aplicada**: `clonedOrder.OrderDate__c = DateTime.now();`

### **2. Inconsistencia en Manejo de Errores**
- VF: Manejo básico con `errorMessage`
- LWC: Toast notifications con `ShowToastEvent`
- Diferentes mensajes de error entre implementaciones

### **3. Campos No Documentados**
- `Sale15__c` se mantiene (no se resetea)
- Campos de fórmula no se clonan (correcto)
- Campos de resumen se recalculan automáticamente

## 🔧 **Capacidades Adicionales**

### **Método de Diagnóstico**
```apex
@AuraEnabled(cacheable=false)
public static String diagnoseOrderLines(String orderId)
```
- Disponible en `orderCloneButton`
- Útil para debugging de líneas de pedido

### **Método Invocable**
```apex
@InvocableMethod(label='Clonar Pedido')
public static List<String> cloneOrderInvocable(List<String> orderIds)
```
- Disponible para Flows
- No se usa actualmente en el proyecto

### **Método AuraEnabled**
```apex
@AuraEnabled(cacheable=false)
public static String cloneOrder(String originalOrderId)
```
- Disponible para LWC
- Usado por múltiples componentes

## 📊 **Campos Clonados vs Reseteados**

### **Campos Clonados (15 campos)**
- `Account__c`, `Business__c`, `Store__c`, `Platform__c`
- `PaymentMethod__c`, `FinalAmount__c`, `TaxBase__c`, `Margin__c`
- `Carrier__c`, `Proforma__c`, `Serie__c`, `Sale15__c`
- `Name`, `Id` (automático)

### **Campos Reseteados (7 campos)**
- `OrderNumber__c` → null
- `OrderDate__c` → Date.today() ⚠️
- `Status__c` → 'Nuevo'
- `A3Status__c` → null
- `PlantStatus__c` → null
- `DeliveryNoteNumber__c` → null
- `InvoiceNumber__c` → null
- `FreightDate__c` → null

## 🎯 **Recomendaciones**

1. ✅ **Corregir tipo de dato**: `DateTime.now()` en lugar de `Date.today()` - **APLICADO**
2. **Estandarizar mensajes de error** entre implementaciones
3. ✅ **Documentar todas las variantes** de implementación - **COMPLETADO**
4. **Considerar consolidación** de las múltiples implementaciones
5. **Añadir validaciones adicionales** para campos requeridos
