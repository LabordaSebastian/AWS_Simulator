# ☁️ AWS Serverless User Onboarding (Simulación Local con LocalStack)

Este proyecto implementa un flujo completo de **Onboarding de Usuarios** utilizando una arquitectura **Serverless** orquestada por **AWS Step Functions**. 

Todo el entorno se ejecuta localmente utilizando **LocalStack** y **Docker**, lo que permite simular servicios de AWS (Lambda, DynamoDB, IAM, Step Functions) sin costos de nube y en un entorno controlado para desarrollo y pruebas.

---

## 🏗️ Arquitectura del Proyecto

El flujo de trabajo es orquestado por una **Step Function** que coordina los siguientes servicios:

1.  **Start Execution**: Recibe un JSON con `username` y `email`.
2.  **Parallel State (Procesamiento en Paralelo)**:
    * **Lambda (`ValidateEmail`)**: Valida el formato del correo electrónico usando Regex.
    * **Lambda (`LogUser`)**: Registra el intento de creación y la fecha en una tabla de **DynamoDB** (`UserLogs`).
3.  **IAM Integration**: Si ambos procesos paralelos son exitosos, utiliza una integración directa con SDK (`arn:aws:states:::iam:createUser`) para crear el usuario en **IAM**.

---

## 🛠️ Tech Stack & Herramientas

* **AWS Services (Simulados):** Step Functions, Lambda, DynamoDB, IAM.
* **Infrastructure:** Docker & Docker Compose.
* **Emulación Cloud:** LocalStack.
* **Lenguaje:** Python 3.x (Boto3).
* **IaC / Configuración:** AWS CLI, ASL (Amazon States Language).
* **IDE:** VS Code + AWS Toolkit Extension.

---

## 🚀 Instalación y Despliegue

### Prerrequisitos
* Docker Desktop corriendo.
* AWS CLI instalado.
* Python 3.x.

### 1. Levantar la Infraestructura (LocalStack)
Ejecutar el contenedor de Docker que simula la nube de AWS en el puerto `4566`:

```bash
docker-compose up -d

```

### 2. Configuración de Entorno

Se utilizan credenciales de prueba ("test") configuradas en el archivo `.env` y un perfil de AWS CLI específico para apuntar al entorno local:

```bash
# Verificar que el perfil localstack existe
aws configure list --profile localstack

```

### 3. Despliegue de Recursos

El script `deploy.py` se encarga de crear la tabla de DynamoDB, empaquetar las funciones Lambda y definir la Máquina de Estado (Step Function).

```bash
python deploy.py

```

---

## 🧪 Ejecución y Pruebas

Para probar el flujo, ejecutamos el script `main.py` que inicia la Step Function con datos aleatorios:

```bash
python main.py

```

*Output esperado:*

```text
> Starting workflow for user: user_778 (user_778@example.com)
> Workflow completed with status: SUCCEEDED

```
## 🖼️ Diagrama del Step Function

```md
![Flujo Step Function](/step_function.png)

### Validación de Resultados (AWS CLI)

Podemos verificar que los recursos se crearon y los datos persistieron utilizando la CLI apuntando al endpoint local.

**Ver usuarios creados en IAM:**

```bash
aws iam list-users --profile localstack

```

**Consultar logs en DynamoDB:**

```bash
aws dynamodb scan --table-name UserLogs --profile localstack

```

**Listar funciones Lambda activas:**

```bash
aws lambda list-functions --profile localstack

```

---

## 💡 Aprendizajes Clave

Este proyecto demuestra competencias en:

* **Cloud Development:** Comprensión de arquitecturas orientadas a eventos y serverless.
* **Testing & Debugging:** Capacidad de levantar entornos locales ("mocks") para pruebas rápidas sin latencia ni costos.
* **Orquestación:** Uso de Step Functions para manejar flujos complejos y paralelismo.
* **DevOps Basics:** Uso de Docker para contenerización de servicios de infraestructura.

---

