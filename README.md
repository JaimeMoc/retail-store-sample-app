![Banner](./docs/images/banner.png)

<div align="center">
  <div align="center">

[![Stars](https://img.shields.io/github/stars/aws-containers/retail-store-sample-app)](Stars)
![GitHub License](https://img.shields.io/github/license/aws-containers/retail-store-sample-app?color=green)
![Dynamic JSON Badge](https://img.shields.io/badge/dynamic/json?url=https%3A%2F%2Fraw.githubusercontent.com%2Faws-containers%2Fretail-store-sample-app%2Frefs%2Fheads%2Fmain%2F.release-please-manifest.json&query=%24%5B%22.%22%5D&label=release)
![GitHub Release Date](https://img.shields.io/github/release-date/aws-containers/retail-store-sample-app)

  </div>

  <strong>
  <h2>AWS Containers Retail</h2>
  </strong>
</div>

Esta es una aplicación de ejemplo diseñada para ilustrar varios conceptos relacionados con contenedores en AWS. Presenta una aplicación de tienda minorista de ejemplo, que incluye un catálogo de productos, carrito de compras y proceso de pago.

Proporciona:

- Una aplicación de demostración de tienda en línea con temas, páginas para mostrar información de la topología de contenedores y aplicaciones, un chatbot de IA generativa y funciones utilitarias para experimentación y demostraciones.
- Una arquitectura de componentes distribuida opcional usando varios lenguajes y frameworks.
- Una variedad de distintos sistemas de persistencia para los diferentes componentes, como MariaDB (o MySQL), DynamoDB y Redis.
- La capacidad de ejecutarse en distintas tecnologías de orquestación de contenedores, como Docker Compose, Kubernetes, etc.
- Imágenes de contenedor preconstruidas para arquitecturas de CPU x86-64 y ARM64.
- Todos los componentes instrumentados para métricas de Prometheus y trazado OTLP de OpenTelemetry.
- Soporte para Istio en Kubernetes.
- Generador de carga que utiliza toda la infraestructura.

Consulta la [documentacion de funciones](./docs/features.md) para màs informaciòn.

**Este proyecto está destinado únicamente con fines educativos y no para uso en producción.**

![Screenshot](/docs/images/screenshot.png)

## Arquitectura de la Aplicación

La aplicación ha sido deliberadamente sobre-diseñada para generar múltiples componentes desacoplados. Estos componentes generalmente tienen diferentes dependencias de infraestructura y pueden admitir múltiples "backends" (por ejemplo: el servicio de Carritos soporta MongoDB o DynamoDB).

![Arquitectura](/docs/images/architecture.png)

| Componente                 | Lenguaje | Imagen de contenedor                                                           | Helm (Paquete)                                                                        | Descripciòn                             |
| -------------------------- | -------- | --------------------------------------------------------------------------- | --------------------------------------------------------------------------------- | --------------------------------------- |
| [UI](./src/ui/)            | Java     | [Link](https://gallery.ecr.aws/aws-containers/retail-store-sample-ui)       | [Link](https://gallery.ecr.aws/aws-containers/retail-store-sample-ui-chart)       | Interfaz de usuario de la tienda                    |
| [Catalog](./src/catalog/)  | Go       | [Link](https://gallery.ecr.aws/aws-containers/retail-store-sample-catalog)  | [Link](https://gallery.ecr.aws/aws-containers/retail-store-sample-catalog-chart)  | API del catálogo de productos                    |
| [Cart](./src/cart/)        | Java     | [Link](https://gallery.ecr.aws/aws-containers/retail-store-sample-cart)     | [Link](https://gallery.ecr.aws/aws-containers/retail-store-sample-cart-chart)     | API de carritos de compras              |
| [Orders](./src/orders)     | Java     | [Link](https://gallery.ecr.aws/aws-containers/retail-store-sample-orders)   | [Link](https://gallery.ecr.aws/aws-containers/retail-store-sample-orders-chart)   | API de pedidos de usuarios                        |
| [Checkout](./src/checkout) | Node     | [Link](https://gallery.ecr.aws/aws-containers/retail-store-sample-checkout) | [Link](https://gallery.ecr.aws/aws-containers/retail-store-sample-checkout-chart) |API para orquestar el proceso de pago |

### Inicio Rápido

Las siguientes secciones ofrecen instrucciones de inicio rápido para diferentes plataformas.

### Docker

Este método de despliegue ejecutará la aplicación como un único contenedor en tu máquina local utilizando docker.

Requisitos previos:

- Tener Docker instalado localmente

Ejecuta el contenedor:

```
docker run -it --rm -p 8888:8080 public.ecr.aws/aws-containers/retail-store-sample-ui:1.0.0
```

Abre la interfaz de usuario en una ventana del navegador:

```
http://localhost:8888
```

Para detener el contenedor en `docker` usa Ctrl+C.

### Docker Compose

Este método de despliegue ejecutará la aplicación en tu máquina local utilizando `docker-compose`.

Requisitos previos:

- Docker instalado localmente
  
Descarga el archivo más reciente de Docker Compose y utilízalo `docker compose` para ejecutar los contenedores de la aplicación:

```
wget https://github.com/aws-containers/retail-store-sample-app/releases/latest/download/docker-compose.yaml

DB_PASSWORD='<some password>' docker compose --file docker-compose.yaml up
```

Abre la interfaz de usuario en una ventana del navegador:

```
http://localhost:8888
```

Para detener los contenedores en `docker compose` usa Ctrl+C. Para eliminar todos los contenedores y los recursos relacionados, ejecuta:

```
docker compose -f docker-compose.yaml down
```

### Kubernetes

Este método de despliegue ejecutará la aplicación en un clúster de Kubernetes existente.

Requisitos previos:

- cluster de Kubernetes.
- `kubectl` instalado localmente.

Usa `kubectl` para ejecutar la aplicación:

```
kubectl apply -f https://github.com/aws-containers/retail-store-sample-app/releases/latest/download/kubernetes.yaml
kubectl wait --for=condition=available deployments --all
```

Obtén la URL del balanceador de carga del frontend de la siguiente manera:

```
kubectl get svc ui
```

Para eliminar la aplicación, utiliza `kubectl` nuevamente:

```
kubectl delete -f https://github.com/aws-containers/retail-store-sample-app/releases/latest/download/kubernetes.yaml
```

### Terraform

Las siguientes opciones están disponibles para desplegar la aplicación utilizando Terraform:

| Nombre                                           | Descripciòn                                                                                                     |
| ------------------------------------------------ | --------------------------------------------------------------------------------------------------------------- |
| [Amazon EKS](./terraform/eks/default/)           | Despliega la aplicación en Amazon EKS utilizando otros servicios de AWS para las dependencias, como RDS, DynamoDB, etc.   |
| [Amazon EKS (Minimal)](./terraform/eks/minimal/) | Despliega la aplicación en Amazon EKS utilizando dependencias dentro del clúster en lugar de RDS, DynamoDB, etc.           |
| [Amazon ECS](./terraform/ecs/default/)           | Despliega la aplicación en Amazon ECS utilizando otros servicios de AWS para las dependencias, como RDS, DynamoDB, etc.    |
| [AWS App Runner](./terraform/apprunner/)         | Despliega la aplicación en AWS App Runner utilizando otros servicios de AWS para las dependencias, como RDS, DynamoDB, etc. |

## Seguridad

Consulta [CONTRIBUTING](CONTRIBUTING.md#security-issue-notifications) para màs informaciòn.

## Licencia
Este proyecto está licenciado bajo la Licencia MIT-0.

Este paquete depende de, y puede incorporar o recuperar, varios paquetes de software de terceros (como paquetes de código abierto) durante la instalación, compilación o ejecución ("Dependencias Externas"). Las Dependencias Externas están sujetas a términos de licencia que debes aceptar para poder usar este paquete. Si no aceptas todos los términos de licencia aplicables, no debes usar este paquete. Se recomienda consultar la política de aprobación de código abierto de tu empresa antes de continuar. A continuación se proporciona una lista de Dependencias Externas y la identificación de la licencia aplicable según la documentación asociada con dichas dependencias al momento de la revisión más reciente de Amazon.

ESTA INFORMACIÓN SE PROPORCIONA SOLO POR CONVENIENCIA. AMAZON NO GARANTIZA QUE LA LISTA O LOS TÉRMINOS Y CONDICIONES APLICABLES SEAN COMPLETOS, PRECISOS O ACTUALIZADOS, Y AMAZON NO TENDRÁ NINGUNA RESPONSABILIDAD POR INEXACTITUDES. DEBES CONSULTAR LOS SITIOS DE DESCARGA DE LAS DEPENDENCIAS EXTERNAS PARA OBTENER LA INFORMACIÓN DE LICENCIA MÁS COMPLETA Y ACTUALIZADA.

EL USO DE LAS DEPENDENCIAS EXTERNAS ES BAJO TU PROPIO RIESGO. EN NINGÚN CASO AMAZON SERÁ RESPONSABLE DE NINGÚN DAÑO, INCLUYENDO, SIN LIMITACIÓN, DAÑOS DIRECTOS, INDIRECTOS, CONSECUENCIALES, ESPECIALES, INCIDENTALES O PUNITIVOS (INCLUIDOS PÉRDIDA DE CLIENTELA, INTERRUPCIÓN DE NEGOCIOS, PÉRDIDAS DE BENEFICIOS O DATOS, O FALLAS O MALFUNCIONAMIENTOS DE COMPUTADORAS) DERIVADOS O RELACIONADOS CON LAS DEPENDENCIAS EXTERNAS, SIN IMPORTAR LA CAUSA Y AUN CUANDO AMAZON HAYA SIDO ADVERTIDO DE LA POSIBILIDAD DE TALES DAÑOS. ESTAS LIMITACIONES Y DESCARGOS APLICAN SALVO LO PROHIBIDO POR LA LEY APLICABLE.

MariaDB Community License - [LICENSE](https://mariadb.com/kb/en/mariadb-licenses/)
MySQL Community Edition - [LICENSE](https://github.com/mysql/mysql-server/blob/8.0/LICENSE)
