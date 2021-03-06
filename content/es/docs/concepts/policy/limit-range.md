---
reviewers:
- raelga
title: Limit Ranges
content_type: concept
weight: 10
---

<!-- overview -->

Por defecto los contenedores se ejecutan sin restricciones de los [recursos informáticos disponibles en un clúster de Kubernetes](/docs/concepts/configuration/manage-resources-containers/). Aplicando cuotas de recursos, los administradores de clústeres pueden restringir la creación de recursos de Kubernetes y el consumo de recursos de systema en base a su  {{< glossary_tooltip text="espacio de nombre" term_id="namespace" >}}.
Dentro de un espacio de nombres, un {{< glossary_tooltip text="Pod" term_id="pod" >}} (o contenedor) puede consumir tanta CPU y memoria como según se haya definido para la cuota de recursos del espacio de nombres.

Un Pod tiene permitido consumir, si el nodo lo dispone, más de la cuota solicitada, pero no más que su propio límte establecido.
Por ejemplo, si un Pod no especifica un valor por defecto para las solicitudes de recursos el nodo puede ofrecerle hasta el límte que ese Pod tiene.
Existe la preocupación de que un Pod pueda monopolizar todos los recursos disponibles. Un LimitRange es una política para limitar las asignaciones de recursos a pods a nivel de espacio de nombres.

<!-- body -->

Un _LimitRange_ puede configurarse para establecer como restricciones los rangos límites con las siguientes virtudes:

- Enumerar las restricciones de requisitos de recursos por espacio de nombres.
- Enumerar las limitaciones de recursos mínimas/máximas para un pod.
- Enumerar las limitaciones de recursos mínimas/máximas para un contenedor.
- Especificar límites de recursos predeterminados para un contenedor.
- Especificar las solicitudes de recursos predeterminados para un contenedor.
- Imponer una relación de proporción entre la solicitud y el límite de un recurso.
- Imponer el cumplimiento de las solicitudes de almacenamiento mínimo/máximo para peticiones de volúmenes persistentes.

## Habilitar el LimitRange

La compatibilidad con LimitRange se ha habilitado de forma predeterminada en Kubernetes desde la versión 1.10.

Para aplicar un LimitRange en un espacio de nombre en particular, el LimitRange debe definirse con el espacio de nombre.

El nombre de recurso de un objeto LimitRange debe ser un
[nombre de subdominio DNS](/docs/concepts/overview/working-with-objects/names#dns-subdomain-names) válido.

### Aplicando rangos límites

- El administrador crea un LimitRange en un espacio de nombres.
- Los usuarios crean recursos como Pods, o Containers de otro tipo de recurso, y PersistentVolumeClaims en el espacio de nombres.
- El controlador de admisión `LimitRanger` aplicará valores predeterminados y límites, para todos los pods y contenedores que no establezcan requisitos de recursos informáticos. Y realiza un seguimiento del uso para garantizar que no excedan el mínimo, el máximo, y la proporción de ningún LimitRange definido en el espacio de nombres.
- Si al crear o actualizar un recurso del ejemplo (Pod, Container, PersistentVolumeClaim) se viola una restricción al LimitRange, la solicitud al servidor API fallará con un código de estado HTTP "403 FORBIDDEN" y un mensaje que explica la restricción que se ha violado.
- En caso de que en se active un LimitRange para recursos de cómputos como `cpu` y `memory`, los usuarios deberán especificar las solicitudes o límites de recursos a dichos valores. De lo contrario, el sistema puede rechazar la creación del {{< glossary_tooltip text="Pod" term_id="pod" >}}.
- Las validaciones de LimitRange ocurren solo en la etapa de Admisión de Pod, no en Pods que ya se han iniciado (Runing Pods).

Algunos ejemplos de políticas que se pueden crear utilizando rangos de límites son:

- En un clúster de 2 nodos con una capacidad de 8 GiB de RAM y 16 núcleos, podría restringirse los Pods en un espacio de nombres a solicitar 100m de CPU con un límite máximo de 500m para CPU y solicitar 200Mi de memoria con un límite máximo de 600Mi de memoria.
- Definir el valor por defecto de límite y solicitúd de CPU a 150m y el valor por defecto de solicitud de memoria a 300Mi para Contenedores que se iniciaron sin solicitudes de CPU y memoria en sus especificaciones.

En el caso de que los límites totales del espacio de nombres sean menores que la suma de los límites de los pods,
puede haber contienda por los recursos. En este caso, los contenedores o pods no se crearán.

Ni la contención ni los cambios en un LimitRange afectarán a los recursos ya creados.

## {{% heading "whatsnext" %}}

Consulte el [LimitRanger design document](https://git.k8s.io/community/contributors/design-proposals/resource-management/admission_control_limit_range.md) para más información.

Para siguientes ejemplos utilizando límites, están pendiendtes de traducción:

- [how to configure minimum and maximum CPU constraints per namespace](/docs/tasks/administer-cluster/manage-resources/cpu-constraint-namespace/).
- [how to configure minimum and maximum Memory constraints per namespace](/docs/tasks/administer-cluster/manage-resources/memory-constraint-namespace/).
- [how to configure default CPU Requests and Limits per namespace](/docs/tasks/administer-cluster/manage-resources/cpu-default-namespace/).
- [how to configure default Memory Requests and Limits per namespace](/docs/tasks/administer-cluster/manage-resources/memory-default-namespace/).
- [how to configure minimum and maximum Storage consumption per namespace](/docs/tasks/administer-cluster/limit-storage-consumption/#limitrange-to-limit-requests-for-storage).
- [a detailed example on configuring quota per namespace](/docs/tasks/administer-cluster/manage-resources/quota-memory-cpu-namespace/).
