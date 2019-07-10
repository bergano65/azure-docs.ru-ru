---
title: Поддержка контейнеров
titleSuffix: Azure Cognitive Services
description: Узнайте, как создать ресурс экземпляра контейнеров Azure с помощью Azure CLI.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 7/5/2019
ms.author: dapine
ms.openlocfilehash: 5e7a3d849f726ae4dbbd559d541464404e427775
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67711739"
---
## <a name="create-an-azure-container-instance-resource-from-the-azure-cli"></a>Создайте ресурс экземпляра контейнера Azure с помощью Azure CLI

Ниже YAML определяет ресурсов экземпляра контейнера Azure. Скопируйте и вставьте содержимое в новый файл с именем `my-aci.yaml` и замените комментарии значения собственными. См. в [формате шаблона] [шаблона форматированию] для допустимых YAML. Ссылаться на [репозиториев контейнера и образов][repositories-and-images] имена доступных образов и их соответствующие репозитории.

```YAML
apiVersion: 2018-10-01
location: # < Valid location >
name: # < Container Group name >
imageRegistryCredentials:
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
> Не все расположения иметь же доступность ресурсов ЦП и памяти. Ссылаться на [расположения и ресурсы][location-to-resource] таблицы для перечня доступных ресурсов для контейнеров на расположение и ОС.

Мы полагаемся на yaml-файл, созданный для [ `az container create` ][azure-container-create] команды. С помощью Azure CLI выполните `az container create` команды, заменив `<resource-group>` на собственные. Кроме того, защита значений в YAML развертывания см. в разделе [защитить значения][secure-values].

```azurecli
az container create -g <resource-group> -f my-aci.yaml
```

Выходные данные команды `Running...` Если допустимый, через некоторое время тип выходных данных изменился в строку JSON, представляющий только что созданный ресурс ACI. Образ контейнера — более чем вероятно, недоступен на некоторое время, но теперь развернут ресурс.

> [!TIP]
> Заслуживает внимания к местоположениям предложений Azure Cognitive Service общедоступной предварительной версии, при необходимости YAML будет необходимо настроить соответствующим образом, чтобы соответствовать расположению.

[azure-container-create]: https://docs.microsoft.com/cli/azure/container?view=azure-cli-latest#az-container-create
[template-format]: https://docs.microsoft.com/azure/templates/Microsoft.ContainerInstance/2018-10-01/containerGroups#template-format

[repositories-and-images]: ../../cognitive-services-container-support.md#container-repositories-and-images
[location-to-resource]: ../../../container-instances/container-instances-region-availability.md#availability---general
[secure-values]: ../../../container-instances/container-instances-environment-variables.md#secure-values