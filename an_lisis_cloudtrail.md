# Análisis de Eventos de CloudTrail

## 1. Origen de la Creación de la Instancia EC2

De acuerdo con el registro JSON extraído del evento `RunInstances`:

* **Servicio / Origen de la Creación:** El evento fue invocado de manera automatizada por el servicio **Auto Scaling de AWS** (`autoscaling.amazonaws.com`).
* **Mecanismo de Ejecución:** La acción fue ejecutada a través del rol asumido de servicio `AWSServiceRoleForAutoScaling` (`arn:aws:iam::654654478122:role/aws-service-role/autoscaling.amazonaws.com/AWSServiceRoleForAutoScaling`) utilizando la plantilla de lanzamiento (*Launch Template*) configurada (`lt-0b074d9cb52d7fc40`).
* **Dirección IP de Origen (`sourceIPAddress`):** `autoscaling.amazonaws.com`

---

## 2. Últimas 5 Acciones del Usuario IAM (`LuisCarlosRodriguez`)

A continuación se detallan las últimas 5 acciones registradas en el historial de CloudTrail asociadas al usuario IAM:

1. `RunInstances`
2. `LookupEvents`
3. `ListManagedNotificationEvents`
4. `DescribeAlarms`
5. `ListManagedNotificationEvents`