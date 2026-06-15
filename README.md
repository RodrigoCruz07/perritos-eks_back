# Tienda de Perritos - Capa Backend (REST API)

Este repositorio aloja la lógica de negocio y la API REST de la plataforma "Tienda de Perritos". Se encarga del procesamiento de datos, la gestión de peticiones desde la capa cliente (Frontend) y la comunicación con la capa de persistencia (Base de Datos).

## Contenido del Repositorio
* `/src`: Lógica de negocio, controladores de la API y modelos de datos.
* `Dockerfile`: Configuración para la contenerización del entorno de ejecución de la API.
* `/.github/workflows/deploy.yml`: Configuración del pipeline de CI/CD.
* `/k8s`: Manifiestos de recursos de Kubernetes (`deployment.yaml`, `service.yaml`, `hpa.yaml`).

## Funcionamiento Arquitectónico
El servicio Backend opera en la capa intermedia de la arquitectura de tres capas, ejecutándose exclusivamente en las **subredes privadas** (Private Subnet 1) del entorno de red de AWS.

* **Interconexión:** Expone sus endpoints internamente a través del puerto `8080` para ser consumidos por el Frontend.
* **Persistencia:** Establece conexiones TCP hacia la capa de datos (MySQL) en el puerto `3306` utilizando la resolución de nombres interna de Kubernetes (ClusterIP Service).

## Seguridad y Gestión de Credenciales
El acceso a recursos sensibles (como cadenas de conexión e identificadores) se gestiona de forma segura:
* Las credenciales de infraestructura se inyectan en el entorno de despliegue mediante **GitHub Secrets**.
* Los parámetros de conexión a la base de datos se consumen dinámicamente en el clúster mediante objetos **Secret de Kubernetes**, evitando la exposición de datos sensibles en texto plano dentro del código fuente.

## Automatización CI/CD
El pipeline de GitHub Actions automatiza el ciclo de vida del código:
1. Valida y compila los artefactos de la aplicación.
2. Construye la imagen de contenedor y la publica en el registro privado **Amazon ECR**.
3. Aplica los cambios en el clúster **AWS EKS**, forzando el despliegue de los nuevos Pods bajo el control del HPA.
