# Examen Práctico Módulo 2: Integración de Servicios AWS Lambda y EC2

**Consulta de Metadatos de Infraestructura y Observabilidad**

* **Autor:** Luis Carlos Rodríguez Timote  
* **Institución:** Bootcamp Betek  
* **Fecha:** 27 de octubre de 2026  

---

## 📋 Descripción del Proyecto

Este proyecto demuestra la integración exitosa entre un modelo de computación serverless (**AWS Lambda**) y la gestión/monitoreo de infraestructura en la nube (**Amazon EC2**). A través de automatización con **Python (`boto3`)**, auditoría con **AWS CloudTrail** y monitoreo/observabilidad con **Amazon CloudWatch**, se implementa una solución completa para inspección de métricas y consulta de metadatos en tiempo real.

---

## 🛠️ Arquitectura y Componentes Desplegados

### Parte 1: Configuración de Escalabilidad (Auto Scaling)
* **Launch Template:** `luiscarlos-ec2-modulo2-examen2-template` (`lt-0b074d9cb52d7fc40`)
  * **AMI:** Amazon Linux 2023 (`ami-04b938437b69a2c9c`)
  * **Tipo de Instancia:** `t2.micro`
* **Auto Scaling Group (ASG):** `luiscarlos-asg-modulo2`
  * **VPC:** `Modulo2-vpc`
  * **Subredes:** `public1-us-east-1a` y `public2-us-east-1b` (Alta disponibilidad)
  * **Capacidad:** `Desired = 1`, `Min = 1`, `Max = 2`

---

### Parte 2: Observabilidad y Monitoreo (Amazon CloudWatch)
Se configuraron métricas y alarmas específicas para la instancia EC2 desplegada por el ASG:
* **Alarmas de CPU:**
  * `luiscarlos-cpu-60-warning`: Alerta de advertencia cuando el uso de CPU supera el **60%**.
  * `luiscarlos-cpu-90-critical`: Alerta crítica cuando el uso de CPU supera el **90%**.
* **Métricas Monitoreadas:**
  * `CPUUtilization`
  * `NetworkIn`
  * `NetworkOut`

---

### Parte 3: Auditoría y Trazabilidad (AWS CloudTrail)
1. **Origen de la Instancia:** Se confirmó a través del registro de eventos JSON que la instancia fue creada automáticamente por el servicio de Auto Scaling (`autoscaling.amazonaws.com`) utilizando el rol asignado (`AWSServiceRoleForAutoScaling`).
2. **Registro de Acciones de Usuario IAM:**
   * `RunInstances`
   * `LookupEvents`
   * `ListManagedNotificationEvents`
   * `DescribeAlarms`
   * `ListManagedNotificationEvents`

---

### Parte 4: Servidor e Inspección de Infraestructura (AWS Lambda)

#### 1. Permisos IAM
* **Rol:** `luiscarlos-lambda-ec2-role`
* Permisos concedidos para lectura de instancias EC2 (`ec2:DescribeInstances`) y escritura de registros en CloudWatch Logs.

#### 2. Función Lambda
* **Nombre:** `fn-lambda-luiscarlos-consultar-ec2`
* **Runtime:** Python 3.x
* **Timeout Ajustado:** 10 segundos (para prevenir timeout por defecto de 3s).

```python
import json
import boto3

def lambda_handler(event, context):
    ec2 = boto3.client('ec2')
    
    # Consultar las instancias EC2
    response = ec2.describe_instances()
    
    # Recorrer las reservas e instancias para extraer la información
    for reservation in response['Reservations']:
        for instance in reservation['Instances']:
            instance_id = instance.get('InstanceId')
            state = instance['State']['Name']
            instance_type = instance.get('InstanceType')
            
            # Imprimir en los logs de CloudWatch
            print(f"ID de la instancia: {instance_id} | Estado: {state} | Tipo: {instance_type}")
            
    return {
        'statusCode': 200,
        'body': json.dumps('Instancias consultadas correctamente')
    }
```

#### 3. Ejecución y Registros (CloudWatch Logs)
* **Evento de Prueba:** `test-ec2-luiscarlos`
* **Grupo de Logs:** `/aws/lambda/fn-lambda-luiscarlos-consultar-ec2`
* **Resultado:** Ejecución exitosa (`statusCode: 200`) imprimiendo en los streams de CloudWatch el ID, estado (`running`/`stopped`) y tipo (`t2.micro`) de todas las instancias en la cuenta.

---

## 🎯 Conclusión

El desarrollo de este examen práctico validó con éxito la integración entre servicios Serverless (**AWS Lambda**) y de infraestructura (**Amazon EC2**), permitiendo una inspección automatizada y un esquema eficiente de observabilidad y trazabilidad en la nube de AWS.