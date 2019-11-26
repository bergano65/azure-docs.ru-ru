---
title: Introduction to container groups
description: Learn about container groups in Azure Container Instances, a collection of instances that share a lifecycle and resources such as storage and network
ms.topic: article
ms.date: 11/01/2019
ms.custom: mvc
ms.openlocfilehash: 9fbf9fea7da0896ee6c0e248d18e18d52798fbd7
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/25/2019
ms.locfileid: "74482105"
---
# <a name="container-groups-in-azure-container-instances"></a>Группы контейнеров в службе "Экземпляры контейнеров Azure"

Ресурс верхнего уровня в службе "Экземпляры контейнеров Azure" — это *группа контейнеров*. В этой статье представлены сведения о группах контейнеров и описаны сценарии, которые можно реализовать с их помощью.

## <a name="what-is-a-container-group"></a>What is a container group?

Группа контейнеров — это набор контейнеров, которые можно планировать на одном хост-компьютере. The containers in a container group share a lifecycle, resources, local network, and storage volumes. It's similar in concept to a *pod* in [Kubernetes][kubernetes-pod].

На схеме ниже показан пример группы контейнеров, включающей несколько контейнеров:

![Диаграмма групп контейнеров][container-groups-example]

Группа контейнеров в этом примере:

* Планируется на одном хост-компьютере.
* Принимает назначенную метку DNS-имени.
* Предоставляет один общедоступный IP-адрес с одним предоставленным портом.
* Состоит из двух частей. Один контейнер ожидает передачи данных на порте 80, а другой — на порте 5000.
* Включает два общих файловых ресурса Azure в качестве подключенных томов. При этом к каждому контейнеру локально подключен один из общих ресурсов.

> [!NOTE]
> Multi-container groups currently support only Linux containers. For Windows containers, Azure Container Instances only supports deployment of a single instance. While we are working to bring all features to Windows containers, you can find current platform differences in the service [Overview](container-instances-overview.md#linux-and-windows-containers).

## <a name="deployment"></a>Развертывание.

Here are two common ways to deploy a multi-container group: use a [Resource Manager template][resource-manager template] or a [YAML file][yaml-file]. A Resource Manager template is recommended when you need to deploy additional Azure service resources (for example, an [Azure Files share][azure-files]) when you deploy the container instances. Due to the YAML format's more concise nature, a YAML file is recommended when your deployment includes only container instances. For details on properties you can set, see the [Resource Manager template reference](/azure/templates/microsoft.containerinstance/containergroups) or [YAML reference](container-instances-reference-yaml.md) documentation.

To preserve a container group's configuration, you can export the configuration to a YAML file by using the Azure CLI command [az container export][az-container-export]. Export allows you to store your container group configurations in version control for "configuration as code." Или вы можете использовать экспортированный файл в качестве отправной точки при разработке новой конфигурации в YAML-файле.



## <a name="resource-allocation"></a>Выделение ресурсов

Azure Container Instances allocates resources such as CPUs, memory, and optionally [GPUs][gpus] (preview) to a container group by adding the [resource requests][resource-requests] of the instances in the group. Taking CPU resources as an example, if you create a container group with two instances, each requesting 1 CPU, then the container group is allocated 2 CPUs.

### <a name="resource-usage-by-instances"></a>Resource usage by instances

Each container instance is allocated the resources specified in its resource request. However, the resource usage by a container instance in a group depends on how you configure its optional [resource limit][resource-limits] property.

* If you don't specify a resource limit, the instance's maximum resource usage is the same as its resource request.

* If you specify a resource limit for an instance, you can adjust the instance's resource usage for its workload, either reducing or increasing usage relative to the resource request. The maximum resource limit you can set is the total resources allocated to the group.
    
    For example, in a group with two instances requesting 1 CPU, one of your containers might run a workload that requires more CPUs to run than the other.

    In this scenario, you could set a resource limit of 0.5 CPU for one instance, and a limit of 2 CPUs for the second. This configuration limits the first container's resource usage to 0.5 CPU, allowing the second container to use up to the full 2 CPUs if available.

For more information, see the [ResourceRequirements][resource-requirements] property in the container groups REST API.

### <a name="minimum-and-maximum-allocation"></a>Minimum and maximum allocation

* Allocate a **minimum** of 1 CPU and 1 GB of memory to a container group. Individual container instances within a group can be provisioned with less than 1 CPU and 1 GB of memory. 

* For the **maximum** resources in a container group, see the [resource availability][region-availability] for Azure Container Instances in the deployment region.

## <a name="networking"></a>Работа в сети

Группы контейнеров совместно используют IP-адрес и пространство имен порта для этого IP-адреса. Чтобы внешние клиенты могли получить доступ к контейнеру в группе, необходимо предоставить порт по IP-адресу и из контейнера. Because containers within the group share a port namespace, port mapping isn't supported. Containers within a group can reach each other via localhost on the ports that they have exposed, even if those ports aren't exposed externally on the group's IP address.

Optionally deploy container groups into an [Azure virtual network][virtual-network] (preview) to allow containers to communicate securely with other resources in the virtual network.

## <a name="storage"></a>Storage

Вы можете указать внешние тома для подключения в пределах группы контейнеров. Эти тома можно сопоставить с конкретными путями в пределах отдельных контейнеров в группе.

## <a name="common-scenarios"></a>Стандартные сценарии

Многоконтейнерные группы удобны в случаях, когда нужно разделить одну функциональную задачу на небольшое число образов контейнеров. Эти образы затем могут доставляться различными командами сотрудников и иметь отдельные требования к ресурсам.

Пример использования может включать следующее:

* Контейнер, обслуживающий веб-приложение, и контейнер, извлекающий последнее содержимое из системы управления версиями.
* Контейнер приложения и журнала. Контейнер журнала собирает журналы и выходные данные метрик для основного приложения и записывает их в хранилище для долговременного хранения.
* Контейнер приложения и мониторинга. Контейнер мониторинга периодически выполняет запрос к приложению, чтобы убедиться, что оно правильно работает и отвечает, и выдает предупреждение, если это не так.
* A front-end container and a back-end container. The front end might serve a web application, with the back end running a service to retrieve data. 

## <a name="next-steps"></a>Дальнейшие действия

Узнайте, как развертывать группу контейнеров с несколькими контейнерами с использованием шаблона Azure Resource Manager:

> [!div class="nextstepaction"]
> [Развертывание группы контейнеров][resource-manager template]

<!-- IMAGES -->
[container-groups-example]: ./media/container-instances-container-groups/container-groups-example.png

<!-- LINKS - External -->
[dcos-pod]: https://dcos.io/docs/1.10/deploying-services/pods/
[kubernetes-pod]: https://kubernetes.io/docs/concepts/workloads/pods/pod/

<!-- LINKS - Internal -->
[resource-manager template]: container-instances-multi-container-group.md
[yaml-file]: container-instances-multi-container-yaml.md
[region-availability]: container-instances-region-availability.md
[resource-requests]: /rest/api/container-instances/containergroups/createorupdate#resourcerequests
[resource-limits]: /rest/api/container-instances/containergroups/createorupdate#resourcelimits
[resource-requirements]: /rest/api/container-instances/containergroups/createorupdate#resourcerequirements
[azure-files]: container-instances-volume-azure-files.md
[virtual-network]: container-instances-vnet.md
[gpus]: container-instances-gpu.md
[az-container-export]: /cli/azure/container#az-container-export
