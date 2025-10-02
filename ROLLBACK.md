# Plan de Rollback – Clona Pedido

## Opciones
1. Revertir despliegue de VF y Apex
   - Desplegar las versiones previas (tag/commit anterior) o eliminar `OrderClone.page` y `OrderCloneController.cls` si no existían

2. Ocultar el botón
   - Quitar el WebLink `Clonar_Pedido` de la Flexipage/Layout de `Order__c`

3. Hotfix rápido
   - Desactivar temporalmente permisos de creación sobre `Order__c` mientras se corrige el problema

## Recomendación
- Mantener un tag de versión previo a cada despliegue
- Validar en sandbox UAT antes de producción
