---
title: Реестр, избыточный в зонах, для обеспечения высокого уровня доступности
description: Сведения о включении избыточности зоны в реестре контейнеров Azure путем создания реестра контейнеров или репликации в зоне доступности Azure. Избыточность зоны — это функция уровня служб Premium.
ms.topic: article
ms.date: 12/11/2020
ms.openlocfilehash: 1553beef47a3d493f066e47cd39751093d83fc24
ms.sourcegitcommit: 7e97ae405c1c6c8ac63850e1b88cf9c9c82372da
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/29/2020
ms.locfileid: "97803516"
---
# <a name="enable-zone-redundancy-in-azure-container-registry-for-resiliency-and-high-availability"></a>Включение избыточности зоны в реестре контейнеров Azure для обеспечения устойчивости и высокой доступности

Помимо [георепликации](container-registry-geo-replication.md), которая реплицирует данные реестра в одном или нескольких регионах Azure для обеспечения доступности и сокращения задержки для региональных операций, реестр контейнеров Azure поддерживает дополнительную *избыточность зоны*. [Избыточность зоны](../availability-zones/az-overview.md#availability-zones) обеспечивает устойчивость и высокую доступность для реестра или ресурса репликации (реплики) в определенном регионе.

В этой статье показано, как настроить избыточный в зоне реестр контейнеров или реплику, избыточную в зоне, с помощью портал Azure или шаблона Azure Resource Manager. 

Избыточность зоны — это **Предварительная версия** функции уровня службы реестра для контейнеров Premium. Сведения об уровнях служб реестра и ограничениях см. в статье [Уровни служб Реестра контейнеров Azure](container-registry-skus.md).

## <a name="preview-limitations"></a>Ограничения предварительной версии

* Сейчас поддерживается в следующих регионах: Восточная часть США, Восточная часть США 2 и Западная часть США 2.
* Преобразования регионов в зоны доступности в настоящее время не поддерживаются. Чтобы включить поддержку зон доступности в регионе, необходимо либо создать реестр в нужном регионе с включенной поддержкой зоны доступности, либо добавить реплицированный регион с включенной поддержкой зоны доступности.
* Избыточность зоны не может быть отключена в регионе.
* [Задачи записи контроля](container-registry-tasks-overview.md) доступа пока не поддерживают зоны доступности.
* В настоящее время поддерживается с помощью шаблонов Azure Resource Manager или портал Azure. Поддержка Azure CLI будет включена в следующем выпуске.

## <a name="about-zone-redundancy"></a>Сведения о избыточности зоны

Используйте [зоны доступности](../availability-zones/az-overview.md) Azure, чтобы создать отказоустойчивый и высокий уровень доступности реестра контейнеров Azure в регионе Azure. Например, организации могут настроить избыточный в пределах зоны реестр контейнеров Azure с другими [поддерживаемыми ресурсами Azure](../availability-zones/az-region.md) для удовлетворения местонахождение данных или других требований соответствия требованиям, обеспечивая высокий уровень доступности в рамках региона.

Реестр контейнеров Azure также поддерживает [георепликацию](container-registry-geo-replication.md), которая реплицирует службу в нескольких регионах, обеспечивая избыточность и расположение для вычислений ресурсов в других местах. Сочетание зон доступности для обеспечения избыточности в пределах региона и георепликации в нескольких регионах повышает надежность и производительность реестра.

Зоны доступности — уникальные физические расположения в пределах одного региона Azure. Чтобы обеспечить устойчивость, во всех включенных областях используются минимум три отдельные зоны. В каждой зоне есть один или несколько центров обработки данных, оснащенных независимым питанием, охлаждением и сетью. При настройке избыточности зоны реестр (или реплика реестра в другом регионе) реплицируется во всех зонах доступности в регионе, что обеспечивает доступ к ним при сбоях центра обработки данных.

## <a name="create-a-zone-redundant-registry---portal"></a>Создание избыточного в зоне реестра — портал

1. Войдите на портал Azure по адресу [https://portal.azure.com](https://portal.azure.com).
1. Последовательно выберите **Создать ресурс** > **Контейнеры** > **Реестр контейнеров**.
1. На вкладке **Основные сведения** выберите или создайте группу ресурсов и введите уникальное имя реестра. 
1. В разделе **Расположение** выберите регион, поддерживающий избыточность зоны для реестра контейнеров Azure, например *Восточная часть США*.
1. В поле **SKU** выберите пункт **Премиум**.
1. В списке **зоны доступности** выберите **включено**.
1. При необходимости настройте дополнительные параметры реестра, а затем выберите **проверить и создать**.
1. Нажмите кнопку **Создать**, чтобы активировать реестр.

    :::image type="content" source="media/zone-redundancy/enable-availability-zones-portal.png" alt-text="Включение избыточности зоны в портал Azure":::

Чтобы создать репликацию, избыточную в виде зоны, выполните следующие действия.

1. Перейдите к реестру контейнеров уровня Premium и выберите **репликация**.
1. На появившейся карте выберите зеленый шестиугольник в регионе, который поддерживает избыточность зоны для реестра контейнеров Azure, например " **Западная часть США 2**". Щелкните **Создать**.
1. В окне **Создание репликации** в области **зоны доступности** выберите **включено** и нажмите кнопку **создать**.

## <a name="create-a-zone-redundant-registry---template"></a>Создание избыточного в зоне реестра — шаблон

### <a name="create-a-resource-group"></a>Создание группы ресурсов

При необходимости выполните команду [AZ Group Create](/cli/azure/group) , чтобы создать группу ресурсов для реестра в регионе, который [поддерживает зоны доступности](../availability-zones/az-region.md) для реестра контейнеров Azure, например *eastus*.

```azurecli
az group create --name <resource-group-name> --location <location>
```

### <a name="deploy-the-template"></a>Развертывание шаблона 

Вы можете использовать следующий шаблон диспетчер ресурсов, чтобы создать геореплицированный реестр с георепликацией в виде зоны. Шаблон по умолчанию включает избыточность зоны в реестре и дополнительную региональную реплику. 

Скопируйте следующее содержимое в новый файл и сохраните его, используя имя файла вида `registryZone.json`.

```JSON
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "acrName": {
        "type": "string",
        "defaultValue": "[concat('acr', uniqueString(resourceGroup().id))]",
        "minLength": 5,
        "maxLength": 50,
        "metadata": {
          "description": "Globally unique name of your Azure Container Registry"
        }
      },
      "location": {
        "type": "string",
        "defaultValue": "[resourceGroup().location]",
        "metadata": {
          "description": "Location for registry home replica."
        }
      },
      "acrSku": {
        "type": "string",
        "defaultValue": "Premium",
        "allowedValues": [
          "Premium"
        ],
        "metadata": {
          "description": "Tier of your Azure Container Registry. Geo-replication and zone redundancy require Premium SKU."
        }
      },
      "acrZoneRedundancy": {
        "type": "string",
        "defaultValue": "Enabled",
        "metadata": {
          "description": "Enable zone redundancy of registry's home replica. Requires registry location to support availability zones."
        }
      },
      "acrReplicaLocation": {
        "type": "string",
        "metadata": {
          "description": "Short name for registry replica location."
        }
      },
      "acrReplicaZoneRedundancy": {
        "type": "string",
        "defaultValue": "Enabled",
        "metadata": {
          "description": "Enable zone redundancy of registry replica. Requires replica location to support availability zones."
        }
      }
    },
    "resources": [
      {
        "comments": "Container registry for storing docker images",
        "type": "Microsoft.ContainerRegistry/registries",
        "apiVersion": "2020-11-01-preview",
        "name": "[parameters('acrName')]",
        "location": "[parameters('location')]",
        "sku": {
          "name": "[parameters('acrSku')]",
          "tier": "[parameters('acrSku')]"
        },
        "tags": {
          "displayName": "Container Registry",
          "container.registry": "[parameters('acrName')]"
        },
        "properties": {
          "adminUserEnabled": "[parameters('acrAdminUserEnabled')]",
          "zoneRedundancy": "[parameters('acrZoneRedundancy')]"
        }
      },
      {
        "type": "Microsoft.ContainerRegistry/registries/replications",
        "apiVersion": "2020-11-01-preview",
        "name": "[concat(parameters('acrName'), '/', parameters('acrReplicaLocation'))]",
        "location": "[parameters('acrReplicaLocation')]",
          "dependsOn": [
          "[resourceId('Microsoft.ContainerRegistry/registries/', parameters('acrName'))]"
        ],
        "properties": {
          "zoneRedundancy": "[parameters('acrReplicaZoneRedundancy')]"
        }
      }
    ],
    "outputs": {
      "acrLoginServer": {
        "value": "[reference(resourceId('Microsoft.ContainerRegistry/registries',parameters('acrName')),'2019-12-01-preview').loginServer]",
        "type": "string"
      }
    }
  }
```

Выполните следующую команду [AZ Deployment Group Create](/cli/azure/deployment?view=azure-cli-latest) , чтобы создать реестр с помощью предыдущего файла шаблона. Где указано, укажите:

* уникальное имя реестра или развертывание шаблона без параметров, а также создание уникального имени
* расположение реплики, которая поддерживает зоны доступности, например *westus2*

```azurecli
az deployment group create \
  --resource-group <resource-group-name> \
  --template-file registryZone.json \
  --parameters acrName=<registry-name> acrReplicaLocation=<replica-location>
```

В выходных данных команды Обратите внимание на `zoneRedundancy` свойство реестра и реплики. Если этот флажок включен, каждый ресурс является избыточным в зоне.

```JSON
{
 [...]
"zoneRedundancy": "Enabled",
}
```

## <a name="next-steps"></a>Дальнейшие действия

* Дополнительные сведения о [регионах, поддерживающих зоны доступности](../availability-zones/az-region.md).
* Узнайте больше о создании для обеспечения [надежности](/azure/architecture/framework/resiliency/overview) в Azure.
