---
title: Развертывание ресурсов с помощью REST API и шаблона
description: Для развертывания ресурсов в Azure и aPI-менеджера ресурсов REST и диспетчера ресурсов. Эти ресурсы определяются в шаблоне Resource Manager.
ms.topic: conceptual
ms.date: 06/04/2019
ms.openlocfilehash: 9cdb7b668e5170917b41ef49639bd9a17e538766
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80153239"
---
# <a name="deploy-resources-with-arm-templates-and-resource-manager-rest-api"></a>Развертывание ресурсов с помощью шаблонов ARM и менеджера ресурсов REST API

В этой статье объясняется, как использовать API-извне управления ресурсами с шаблонами Azure Resource Manager (ARM) для развертывания ресурсов в Azure.

Вы можете включить шаблон в текст запроса или связать с файлом. Файл может быть локальным или внешним, доступным по универсальному коду ресурса (URI). Если шаблон находится в учетной записи хранения, то во время развертывания можно ограничить доступ к шаблону и предоставить маркер подписанного URL-адреса (SAS).

## <a name="deployment-scope"></a>Область развертывания

Развертывание можно настроить на группу управления, подписку Azure или группу ресурсов. В большинстве случаев развертывание будет нацелено на группу ресурсов. Используйте развертывание группы управления или подписки для применения политик и назначений ролей в указанной области. Развертывание подписки также используется для создания группы ресурсов и развертывания ресурсов. В зависимости от сферы развертывания используются различные команды.

Для развертывания в **группе ресурсов**используйте [развертывание - Создание.](/rest/api/resources/deployments/createorupdate) Запрос отправляется по:

```HTTP
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2019-05-01
```

Для развертывания **в подписке**используйте [развертывания - Создайте в области подписки.](/rest/api/resources/deployments/createorupdateatsubscriptionscope) Запрос отправляется по:

```HTTP
PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2019-05-01
```

Для получения дополнительной информации о развертывании уровня подписки [см.](deploy-to-subscription.md)

Для развертывания в **группе управления**используйте [развертывание - Создайте область управления.](/rest/api/resources/deployments/createorupdateatmanagementgroupscope) Запрос отправляется по:

```HTTP
PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{groupId}/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2019-05-01
```

Для получения дополнительной информации о развертываниях группы управления [см.](deploy-to-management-group.md)

Примеры в этой статье используют развертывания групп ресурсов.

## <a name="deploy-with-the-rest-api"></a>Развертывание с помощью REST API

1. Задайте [общие параметры и заголовки](/rest/api/azure/), включая маркеры аутентификации.

1. Создайте группу ресурсов, если у вас нет существующей группы ресурсов. Укажите идентификатор подписки, имя новой группы ресурсов и расположение, необходимое для решения. Дополнительную информацию см. в разделе [Создание группы ресурсов](/rest/api/resources/resourcegroups/createorupdate).

   ```HTTP
   PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>?api-version=2019-05-01
   ```

   С текстом запроса:

   ```json
   {
    "location": "West US",
    "tags": {
      "tagname1": "tagvalue1"
    }
   }
   ```

1. Проверьте развернутую службу перед ее выполнением. Для этого выполните операцию [проверки развертывания шаблонов](/rest/api/resources/deployments/validate). При тестировании развернутой службы укажите точно такие же параметры, как и при ее выполнении (как показано на следующем шаге).

1. Чтобы развернуть шаблон, укажите идентификатор подписки, имя группы ресурсов, имя развертывания в запросе URI.

   ```HTTP
   PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2019-05-01
   ```

   В органе запроса предоставьте ссылку на шаблон и файл параметров. Для получения дополнительной информации о файле параметра [см.](parameter-files.md)

   Обратите внимание, что для параметра **mode** выбрано значение **Incremental**. Для выполнения полного развертывания установите **режим** **для завершения.** Будьте внимательны при использовании полного режима, так как вы можете случайно удалить ресурсы, которые находятся не в шаблоне.

   ```json
   {
    "properties": {
      "templateLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
        "contentVersion": "1.0.0.0"
      },
      "parametersLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
        "contentVersion": "1.0.0.0"
      },
      "mode": "Incremental"
    }
   }
   ```

    Если вы хотите регистрировать в журнале содержимое запроса или содержимое ответа (или и то, и другое), добавьте в запрос параметр **debugSetting** .

   ```json
   {
    "properties": {
      "templateLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
        "contentVersion": "1.0.0.0"
      },
      "parametersLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
        "contentVersion": "1.0.0.0"
      },
      "mode": "Incremental",
      "debugSetting": {
        "detailLevel": "requestContent, responseContent"
      }
    }
   }
   ```

    Можно настроить учетную запись хранения для использования маркера подписанного URL-адреса (SAS). Дополнительные сведения см. в статье [Делегирование доступа с помощью подписанного URL-адреса](/rest/api/storageservices/delegating-access-with-a-shared-access-signature).

    Если требуется предоставить конфиденциальное значение для параметра (например, пароль), добавьте это значение в хранилище ключей. Получите хранилище ключей во время развертывания, как показано в предыдущем примере. Для получения дополнительной информации смотрите [безопасные значения Pass во время развертывания.](key-vault-parameter.md)

1. Вместо создания ссылки на файлы для шаблона и параметров их можно включить в тексте запроса. В следующем примере показан корпус запроса с вставкой шаблона и параметра:

   ```json
   {
      "properties": {
      "mode": "Incremental",
      "template": {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
          "storageAccountType": {
            "type": "string",
            "defaultValue": "Standard_LRS",
            "allowedValues": [
              "Standard_LRS",
              "Standard_GRS",
              "Standard_ZRS",
              "Premium_LRS"
            ],
            "metadata": {
              "description": "Storage Account type"
            }
          },
          "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
              "description": "Location for all resources."
            }
          }
        },
        "variables": {
          "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]"
        },
        "resources": [
          {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2018-02-01",
            "name": "[variables('storageAccountName')]",
            "location": "[parameters('location')]",
            "sku": {
              "name": "[parameters('storageAccountType')]"
            },
            "kind": "StorageV2",
            "properties": {}
          }
        ],
        "outputs": {
          "storageAccountName": {
            "type": "string",
            "value": "[variables('storageAccountName')]"
          }
        }
      },
      "parameters": {
        "location": {
          "value": "eastus2"
        }
      }
    }
   }
   ```

1. Чтобы получить статус развертывания шаблона, используйте [развертывание - Получите.](/rest/api/resources/deployments/get)

   ```HTTP
   GET https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2018-05-01
   ```

## <a name="next-steps"></a>Дальнейшие действия

- Чтобы откатить к успешному развертыванию при ошибке, просмейте [откат по ошибке для успешного развертывания.](rollback-on-error.md)
- Сведения о том, как указать способ обработки ресурсов, которые существуют в группе ресурсов, но не определены в шаблоне, см. в [описании режимов развертывания с помощью Azure Resource Manager](deployment-modes.md).
- Сведения об обработке асинхронных операций REST см. в статье [Track asynchronous Azure operations](../management/async-operations.md) (Отслеживание асинхронных операций Azure).
- Чтобы узнать больше о шаблонах, [см.](template-syntax.md)

