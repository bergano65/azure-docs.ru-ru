---
title: Поддержка контейнеров
titleSuffix: Azure Cognitive Services
description: Узнайте, как создать ресурс экземпляров ресурсов ресурсов ресурсов azure из Azure CLI.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 01/06/2020
ms.author: dapine
ms.openlocfilehash: 700a04b58c13a9c7fd5301875226ca234cabeb96
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "75689466"
---
## <a name="create-an-azure-container-instance-resource-from-the-azure-cli"></a>Создание ресурса Azure Container Instance из Azure CLI

Ниже YAML определяет ресурс Azure Container Instance. Копировать и вставить содержимое в `my-aci.yaml` новый файл, названный и заменить комментируемые значения с вашим собственным. Обратитесь к [формату шаблона][template-format] для действительного YAML. Обратитесь к [репозиториям контейнеров и изображениям][repositories-and-images] для доступных имен изображений и их соответствующего репозитория. Для получения дополнительной информации о ссылке YAML для экземпляров контейнеров [см.][aci-yaml-ref]

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
> Не все местоположения имеют одинаковую доступность процессора и памяти. Обратитесь к таблице [местоположения и ресурсов][location-to-resource] для перечисления доступных ресурсов для контейнеров на место и ОС.

Мы будем полагаться на файл YAML, созданный [`az container create`][azure-container-create] для команды. Из Azure CLI выполните `az container create` команду, `<resource-group>` заменяя ее собственной. Кроме того, для обеспечения значений в развертывании YAML отсылайте к [защищенным значениям.][secure-values]

```azurecli
az container create -g <resource-group> -f my-aci.yaml
```

Вывод команды действителен, `Running...` через некоторое время вывод изменяется в строку JSON, представляющую вновь созданный ресурс ACI. Изображение контейнера, скорее всего, не доступно некоторое время, но ресурс теперь развернут.

> [!TIP]
> Обратите пристальное внимание на расположение общедоступных предварительных предложений Azure Cognitive Service, так как YAML необходимо будет соответствующим образом скорректировать в соответствии с местоположением.

[azure-container-create]: https://docs.microsoft.com/cli/azure/container?view=azure-cli-latest#az-container-create
[template-format]: https://docs.microsoft.com/azure/templates/Microsoft.ContainerInstance/2018-10-01/containerGroups#template-format
[aci-yaml-ref]: ../../../container-instances/container-instances-reference-yaml.md
[repositories-and-images]: ../../cognitive-services-container-support.md#container-repositories-and-images
[location-to-resource]: ../../../container-instances/container-instances-region-availability.md#availability---general
[secure-values]: ../../../container-instances/container-instances-environment-variables.md#secure-values
