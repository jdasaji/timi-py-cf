# 🚀 CI/CD con AWS CodePipeline

Este repositorio implementa un flujo de integración y despliegue continuo (CI/CD) en AWS utilizando CodePipeline, CodeBuild, ECR, Lambda y CloudFormation.

## 📌 Flujo del CI/CD

### 1️⃣ CodePipeline detecta cambios en GitHub
Cuando realizas un `git push`, AWS CodePipeline detecta el cambio en la rama `master` gracias a la configuración en `ci-cd.yml`.

### 2️⃣ Descarga del código desde GitHub
📌 **Archivo:** `cloudformation/ci-cd.yml`
- Descarga el código desde el repositorio de GitHub.
- Almacena el código en un **S3 Artifact Bucket**.
- Pasa el código como entrada al siguiente paso (**CodeBuild**).

### 3️⃣ CodeBuild construye la imagen Docker y la sube a ECR
📌 **Archivo:** `pipeline/buildspec.yml`
- Instala dependencias con `pip install` en `src/`.
- Carga variables de entorno desde `config/dev.env`.
- Construye la imagen Docker (`docker build`).
- Se autentica en AWS ECR y sube la imagen.
- **Actualiza la Lambda** con la nueva imagen (`aws lambda update-function-code`).

### 4️⃣ CloudFormation crea/actualiza la infraestructura
📌 **Archivo:** `cloudformation/infraestructura.yml`
- Crea el **ECR Repository** para la imagen.
- Crea el **IAM Role** para Lambda.
- Crea la **Lambda Function** usando la imagen de ECR.
- Configura **EventBridge** para ejecutar la Lambda cada minuto.

💡 **Nota:** Si los recursos ya existen, CloudFormation solo actualizará lo necesario.

### 5️⃣ La Lambda se ejecuta automáticamente
📌 **Servicio:** AWS Lambda
- La Lambda ya está actualizada con la nueva imagen de **ECR**.
- **EventBridge** la ejecuta automáticamente cada 1 minuto.
- La Lambda obtiene secretos de **Secrets Manager** y sube archivos a **S3**.

## 🔹 📌 Resumen del Flujo Completo
1️⃣ **GitHub → CodePipeline** (Detecta cambios en `main`).
2️⃣ **CodePipeline → CodeBuild** (`buildspec.yml` instala dependencias, crea imagen y la sube a ECR).
3️⃣ **CodePipeline → CloudFormation** (`infraestructura.yml` crea/actualiza recursos como Lambda y EventBridge).
4️⃣ **Lambda ejecuta el código y sube archivos a S3**.

---

✅ **Con este flujo, los cambios en el código se despliegan automáticamente en AWS.** 🎯
