# Checklist de Despliegue – Clona Pedido

- [ ] Confirmar autenticación SFDX al org destino (`prod`)
- [ ] Validar permisos Create en `Order__c` y `OrderItem__c`
- [ ] Revisar `deploy/package.xml` incluye VF, Apex y `Order__c.object`
- [ ] Ejecutar validación (`mdapi:deploy -c` con `RunLocalTests`)
- [ ] Ejecutar despliegue real (`mdapi:deploy` con `RunLocalTests`)
- [ ] Verificar que el botón `Clonar Pedido` aparece en `Order__c`
- [ ] Probar clonación en un pedido con líneas
- [ ] Revisar logs y errores Apex
- [ ] Comunicar a usuarios el cambio
