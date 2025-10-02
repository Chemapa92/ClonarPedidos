# Comandos útiles (SFDX)

## Autenticación
```bash
sfdx auth:web:login -a prod
```

## Validación (MDAPI)
```bash
sfdx force:mdapi:deploy -d deploy -u prod -w -1 -c -l RunLocalTests
```

## Despliegue (MDAPI)
```bash
sfdx force:mdapi:deploy -d deploy -u prod -w -1 -l RunLocalTests
```

## Despliegue selectivo (Source)
```bash
sfdx force:source:deploy \
  -p force-app/main/default/classes/OrderCloneController.cls,force-app/main/default/pages/OrderClone.page \
  -u prod -w -1
```
