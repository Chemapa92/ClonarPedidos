# Especificación del Botón "Clonar Pedido"

## 📋 **Configuración del Botón**

### **Propiedades Básicas**
- **Label**: `Clonar Pedido`
- **Name**: `Clonar_Pedido`
- **Object Name**: `Pedido` (Order__c)
- **Link Encoding**: `Unicode (UTF-8)`
- **Display Type**: `Detail Page Button`
- **Behavior**: `Display in existing window without sidebar or header`

### **URL del Botón**
```
/apex/OrderClone?recordId={!Order__c.Id}
```

### **Descripción**
```
Llama a la siguiente clase apex: /apex/OrderClone?recordId={!Order__c.Id}
```

## 🎯 **Ubicaciones del Botón**

### **1. Layout Clásico**
- **Archivo**: `Order__c-Formato Pedido.layout`
- **Línea**: 3
- **Configuración**: `<customButtons>Clonar_Pedido</customButtons>`

### **2. Lightning Record Page**
- **Archivo**: `Pedido_P_gina_de_registro.flexipage`
- **Línea**: 77
- **Configuración**: `<value>CustomButton.Order__c.Clonar_Pedido</value>`
- **Contexto**: Componente `lightning__RecordPage` en la sección de acciones

## 🔄 **Flujo de Ejecución**

1. **Usuario hace clic** en "Clonar Pedido"
2. **Se abre** `/apex/OrderClone?recordId={!Order__c.Id}` en ventana existente
3. **Visualforce page** ejecuta `action="{!cloneOrder}"`
4. **OrderCloneController.cloneOrder()** procesa la clonación
5. **Redirección** al nuevo pedido clonado

## 📁 **Archivos Relacionados**

### **Core Files**
- `Order__c.object` - WebLink definition
- `OrderClone.page` - Visualforce page
- `OrderCloneController.cls` - Apex controller

### **UI Files**
- `Order__c-Formato Pedido.layout` - Classic layout
- `Pedido_P_gina_de_registro.flexipage` - Lightning record page

### **Optional Files**
- `orderCloneButton` (LWC) - Alternative implementation
- `orderCloneAction` (LWC) - Alternative implementation
- `cloneOrderCustomAction` (LWC) - Alternative implementation

## ⚠️ **Consideraciones de Despliegue**

1. **El botón está en ambos layouts** (clásico y Lightning)
2. **Requiere permisos** Create en `Order__c` y `OrderItem__c`
3. **Dependencias**: VF page, Apex controller, y objeto Order__c
4. **Comportamiento**: Se abre en ventana existente (no nueva pestaña)

## 🎨 **Experiencia de Usuario**

- **Ubicación**: Visible en la página de detalle del pedido
- **Acceso**: Un clic desde la interfaz principal
- **Feedback**: Pantalla de carga durante el proceso
- **Resultado**: Navegación automática al pedido clonado
- **Error handling**: Mensajes de error en la misma ventana
