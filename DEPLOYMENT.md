# Despliegue a Producción – Clona Pedido (Order__c)

Este documento describe cómo desplegar la funcionalidad de "Clona Pedido" a Producción con Salesforce CLI (SFDX).

## Alcance del despliegue
- Visualforce: `OrderClone.page`, `OrderClone.page-meta.xml`
- Apex: `OrderCloneController.cls`, `OrderCloneController.cls-meta.xml`
- Objeto: `Order__c.object` (incluye el WebLink `Clonar_Pedido`)
- (Opcional) Flexipage: `Pedido_P_gina_de_registro.flexipage`
- (Opcional) LWC/Quick Action relacionadas si se usan

## Pre-requisitos
- Salesforce CLI instalado y autenticado (`sfdx auth:web:login -a prod`)
- Permisos para desplegar en el org destino
- Perfiles/Permisos con Create en `Order__c` y `OrderItem__c`

## Despliegue con Metadata API (carpeta `deploy/`)
1. Validación (checkOnly):
```bash
sfdx force:mdapi:deploy -d deploy -u prod -w -1 -c -l RunLocalTests
```
2. Despliegue real:
```bash
sfdx force:mdapi:deploy -d deploy -u prod -w -1 -l RunLocalTests
```

## Despliegue selectivo (formato SFDX)
```bash
sfdx force:source:deploy \
  -p force-app/main/default/classes/OrderCloneController.cls,force-app/main/default/pages/OrderClone.page \
  -u prod -w -1
```
Añade `force-app/main/default/objects/Order__c.object` si necesitas instalar el WebLink en prod.

## Verificación post despliegue
- El botón `Clonar Pedido` aparece y abre `/apex/OrderClone?recordId=...`
- Se crea un nuevo pedido con estado `Nuevo`, fecha de hoy, y líneas clonadas
- No hay errores en logs/debug

## Rollback
- Ver `ROLLBACK.md` para revertir a versión anterior o desactivar el botón temporalmente.
