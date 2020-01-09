---
title: Поддержка контейнеров
titleSuffix: Azure Cognitive Services
description: Узнайте, как создать ресурс экземпляра контейнера Azure из Azure CLI.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 01/06/2020
ms.author: dapine
ms.openlocfilehash: 700a04b58c13a9c7fd5301875226ca234cabeb96
ms.sourcegitcommit: 2f8ff235b1456ccfd527e07d55149e0c0f0647cc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/07/2020
ms.locfileid: "75689466"
---
## <a name="create-an-azure-container-instance-resource-from-the-azure-cli"></a>Создание ресурса экземпляра контейнера Azure из Azure CLI

YAML ниже определяет ресурс экземпляра контейнера Azure. Скопируйте и вставьте содержимое в новый файл с именем `my-aci.yaml` и замените значения комментария собственными. См. [Формат шаблона][template-format] для допустимого YAML. Сведения о доступных именах образов и соответствующих репозиториях см. в разделе [репозитории и образы контейнеров][repositories-and-images] . Дополнительные сведения о справочнике по YAML для экземпляров контейнеров см. в разделе [Справочник по YAML: экземпляры контейнеров Azure][aci-yaml-ref].

```YAML
apiVersion: 2018-10-01
location: # < Valid location >
name: # < Container Group name >
imageRegistryCredentials: # This is required when pulling a non-public image
  - server: containerpreview.azurecr.io
    username: # < The username for the preview container registry >
    password: # < The password for the preview container registry >
properties:
  containers:
  - name: # < Container name >
    properties:
      image: # < Repository/Image name >
      environmentVariables: # These env vars are required
        - name: eula
          value: accept
        - name: billing
          value: # < Service specific Endpoint URL >
        - name: apikey
          value: # < Service specific API key >
      resources:
        requests:
          cpu: 4 # Always refer to recommended minimal resources
          memoryInGb: 8 # Always refer to recommended minimal resources
      ports:
        - port: 5000
  osType: Linux
  volumes: # This node, is only required for container instances that pull their model in at runtime, such as LUIS.
  - name: aci-file-share
    azureFile:
      shareName: # < File share name >
      storageAccountName: # < Storage account name>
      storageAccountKey: # < Storage account key >
  restartPolicy: OnFailure
  ipAddress:
    type: Public
    ports:
    - protocol: tcp
      port: 5000
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

> [!NOTE]
> Не все расположения имеют одинаковую доступность ЦП и памяти. Список доступных ресурсов для контейнеров в расположении и ОС см. в таблице " [расположение и ресурсы][location-to-resource] ".

Мы будем полагаться на файл YAML, созданный для команды [`az container create`][azure-container-create] . В Azure CLI выполните команду `az container create` замените `<resource-group>` своим собственным. Кроме того, для защиты значений в развертывании YAML ссылаются на [защищенные значения][secure-values].

```azurecli
az container create -g <resource-group> -f my-aci.yaml
```

Выходные данные команды `Running...`, если это допустимо, после того, как выходные данные изменяются на строку JSON, представляющую созданный ресурс ACI. Образ контейнера, скорее всего, будет недоступен в течение определенного времени, но теперь ресурс развернут.

> [!TIP]
> Обратите особое внимание на местоположения общедоступной предварительной версии предложения службы "Поиск в Azure", так как YAML потребуется соответствующим образом скорректировать их в соответствии с расположением.

[azure-container-create]: https://docs.microsoft.com/cli/azure/container?view=azure-cli-latest#az-container-create
[template-format]: https://docs.microsoft.com/azure/templates/Microsoft.ContainerInstance/2018-10-01/containerGroups#template-format
[aci-yaml-ref]: ../../../container-instances/container-instances-reference-yaml.md
[repositories-and-images]: ../../cognitive-services-container-support.md#container-repositories-and-images
[location-to-resource]: ../../../container-instances/container-instances-region-availability.md#availability---general
[secure-values]: ../../../container-instances/container-instances-environment-variables.md#secure-values
