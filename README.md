# Examen Módulo 2: Integración de Servicios AWS Lambda y EC2

Este repositorio contiene la documentación, código y evidencias del taller práctico sobre la integración de servicios **AWS Lambda**, **Amazon EC2**, **IAM** y **Amazon CloudWatch** para la consulta automatizada de metadatos de infraestructura.

---

## 📄 Información del Proyecto

* **Autor:** Luis Carlos Rodríguez Timote
* **Fecha:** Octubre 2026
* **Institución:** Entrenamiento AWS
* **Servicio Principal:** AWS Lambda & Amazon EC2

---

## 🎯 Objetivo General

Implementar una función Lambda en **Python** que consulte mediante el SDK `boto3` el listado de instancias EC2 en la cuenta, extrayendo su ID, estado y tipo, aplicando el principio de mínimo privilegio en IAM y asegurando la observabilidad a través de CloudWatch Logs.

---

## ⚙️ Arquitectura y Componentes

1. **IAM Role (`luiscarlos-lambda-ec2-role`):**
   * Políticas asociadas: Permisos de lectura sobre EC2 (`AmazonEC2ReadOnlyAccess`) y permisos para escritura de registros en CloudWatch (`AWSLambdaBasicExecutionRole`).
2. **AWS Lambda (`fn-lambda-luiscarlos-consultar-ec2`):**
   * Runtime: Python 3.x
   * Handler: `lambda_function.lambda_handler`
   * Timeout configurado: 10 segundos
3. **Amazon CloudWatch:**
   * Log Group: `/aws/lambda/fn-lambda-luiscarlos-consultar-ec2`

---

## 🛠️ Descripción del Proceso Realizado (Paso a Paso)

1. **Creación del Rol IAM y Permisos:**
   Se creó y configuró el rol de ejecución `luiscarlos-lambda-ec2-role`, asignándole los permisos necesarios de lectura para EC2 y los privilegios básicos para la gestión de registros.

2. **Creación de la Función Lambda:**
   Se creó la función Lambda denominada `fn-lambda-luiscarlos-consultar-ec2`, integrando el código en Python con la librería `boto3` para consultar el ID, el estado y el tipo de las instancias EC2.

3. **Actualización de la Función y Configuración del Timeout:**
   Se actualizó la configuración general de la función Lambda, ampliando el parámetro **Timeout** de 3 a 10 segundos para solucionar y prevenir errores de tiempo de ejecución (`Sandbox.Timedout`).

4. **Ejecución de Pruebas (Test):**
   Se creó y guardó el evento de prueba `test-ec2-luiscarlos`, ejecutando la función de forma exitosa (código de respuesta `200`).

5. **Verificación en Amazon CloudWatch (Logs):**
   Finalmente, se accedió al servicio CloudWatch, ingresando a la sección **Log management** -> **Log groups** para ubicar el grupo de registros de la función y verificar que los datos de las instancias fueron registrados correctamente.

---

## 💻 Código de la Función Lambda (`lambda_function.py`)

```python
import json
import boto3

ec2 = boto3.client('ec2')

def lambda_handler(event, context):
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

---

## 💡 Conclusión

El desarrollo de esta práctica permitió validar con éxito la integración de un modelo de computación *serverless* utilizando **AWS Lambda** para la gestión e inspección automatizada de infraestructura en la nube (**Amazon EC2**). Se reforzaron conceptos fundamentales de seguridad mediante **IAM**, ajuste de parámetros de rendimiento (Timeout) y monitoreo con **Amazon CloudWatch Logs**.