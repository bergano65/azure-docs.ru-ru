---
title: Развертывание ресурсов с помощью REST API и шаблона
description: Сведения о том, как применить Azure Resource Manager и REST API Resource Manager для развертывания ресурсов в Azure. Эти ресурсы определяются в шаблоне Resource Manager.
ms.topic: conceptual
ms.date: 05/20/2020
ms.openlocfilehash: d7865ac6f9b2bb176ea5308e326dec0741a80962
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/21/2020
ms.locfileid: "83723125"
---
# <a name="deploy-resources-with-arm-templates-and-resource-manager-rest-api"></a>Развертывание ресурсов с использованием шаблонов ARM и REST API Resource Manager.

В этой статье описано, как использовать REST API Resource Manager с шаблонами Azure Resource Manager (ARM) для развертывания ресурсов в Azure.

Вы можете включить шаблон в текст запроса или связать с файлом. Файл может быть локальным или внешним, доступным по универсальному коду ресурса (URI). Если шаблон находится в учетной записи хранения, то во время развертывания можно ограничить доступ к шаблону и предоставить маркер подписанного URL-адреса (SAS).

## <a name="deployment-scope"></a>Область развертывания

Вы можете нацелить развертывание на группу управления, подписку Azure или группу ресурсов. В большинстве случаев в качестве области развертывания используется группа ресурсов. Развертывание в группе управления или подписке удобно для применения политик и назначений ролей в соответствующей области. Также развертывание в подписке используется, чтобы создать группу ресурсов и развернуть в ней ресурсы. В зависимости от области развертывания используются разные команды.

* Чтобы выполнить развертывание в **группе ресурсов**, используйте инструкции из статьи [Развертывания: создание](/rest/api/resources/deployments/createorupdate). Назначение для такого запроса:

  ```HTTP
  PUT https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2019-10-01
  ```

* Чтобы выполнить развертывание в **подписке**, используйте инструкции из статьи [Развертывания: создание в области подписки](/rest/api/resources/deployments/createorupdateatsubscriptionscope). Назначение для такого запроса:

  ```HTTP
  PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2019-10-01
  ```

  Дополнительные сведения о развертываниях на уровне подписки см. в статье [Создание групп ресурсов и ресурсов на уровне подписки](deploy-to-subscription.md).

* Чтобы выполнить развертывание в **группе управления**, используйте инструкции из статьи [Развертывания: создание в области группы управления](/rest/api/resources/deployments/createorupdateatmanagementgroupscope). Назначение для такого запроса:

  ```HTTP
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{groupId}/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2019-10-01
  ```

  Дополнительные сведения о развертываниях на уровне групп управления см. в статье [Создание ресурсов на уровне группы управления](deploy-to-management-group.md).

* Чтобы выполнить развертывание в **клиенте**, используйте инструкции из статьи [Развертывания: создание или обновление в области клиента](/rest/api/resources/deployments/createorupdateattenantscope). Назначение для такого запроса:

  ```HTTP
  PUT https://management.azure.com/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2019-10-01
  ```

  Дополнительные сведения о развертываниях на уровне клиента см. в статье [Создание ресурсов на уровне клиента](deploy-to-tenant.md).

В примерах, приведенных в этой статье, мы используем развертывание в группе ресурсов.

## <a name="deploy-with-the-rest-api"></a>Развертывание с помощью REST API

1. Задайте [общие параметры и заголовки](/rest/api/azure/), включая маркеры аутентификации.

1. Создайте группу ресурсов, если у вас нет существующей группы ресурсов. Укажите идентификатор подписки, имя новой группы ресурсов и расположение, необходимое для решения. Дополнительную информацию см. в разделе [Создание группы ресурсов](/rest/api/resources/resourcegroups/createorupdate).

   ```HTTP
   PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>?api-version=2019-10-01
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

1. Чтобы развернуть шаблон, укажите идентификатор подписки, имя группы ресурсов, имя развертывания и URI запроса.

   ```HTTP
   PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2019-10-01
   ```

   В тексте запроса укажите ссылку на шаблон и файл параметров. Дополнительные сведения о файле параметров см. в статье [Создание файла параметров Resource Manager](parameter-files.md).

   Обратите внимание, что для параметра **mode** выбрано значение **Incremental**. Чтобы выполнить полное развертывание, установите для параметра **mode** значение **Complete**. Будьте внимательны при использовании полного режима, так как вы можете случайно удалить ресурсы, которые находятся не в шаблоне.

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

    Если требуется предоставить конфиденциальное значение для параметра (например, пароль), добавьте это значение в хранилище ключей. Получите хранилище ключей во время развертывания, как показано в предыдущем примере. Дополнительные сведения см. в статье [Передача безопасных значений в процессе развертывания](key-vault-parameter.md).

1. Вместо создания ссылки на файлы для шаблона и параметров их можно включить в тексте запроса. В следующем примере приведен текст запроса, в котором напрямую указаны шаблон и параметр.

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

1. Чтобы получить состояние развертывания шаблона, используйте инструкции из статьи [Развертывания: получение](/rest/api/resources/deployments/get).

   ```HTTP
   GET https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2019-10-01
   ```

## <a name="next-steps"></a>Дальнейшие действия

- Если возникнет ошибка при развертывании, выполните откат по инструкциям из статьи [Откат к работоспособному развертыванию в случае ошибки](rollback-on-error.md).
- Сведения о том, как указать способ обработки ресурсов, которые существуют в группе ресурсов, но не определены в шаблоне, см. в [описании режимов развертывания с помощью Azure Resource Manager](deployment-modes.md).
- Сведения об обработке асинхронных операций REST см. в статье [Track asynchronous Azure operations](../management/async-operations.md) (Отслеживание асинхронных операций Azure).
- Дополнительные сведения о шаблонах см. в статье [Общие сведения о структуре и синтаксисе шаблонов ARM](template-syntax.md).

