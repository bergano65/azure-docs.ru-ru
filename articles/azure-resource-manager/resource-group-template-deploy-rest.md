---
title: Развертывание ресурсов с помощью REST API и шаблона | Документация Майкрософт
description: Используйте Azure Resource Manager и REST API Resource Manager для развертывания ресурсов в Azure. Эти ресурсы определяются в шаблоне Resource Manager.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.assetid: 1d8fbd4c-78b0-425b-ba76-f2b7fd260b45
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/28/2019
ms.author: tomfitz
ms.openlocfilehash: 15e4a7058dc1e74c726644e86c58381003eee937
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60782979"
---
# <a name="deploy-resources-with-resource-manager-templates-and-resource-manager-rest-api"></a>Развертывание ресурсов с использованием шаблонов и REST API Resource Manager

В этом разделе объясняется, как использовать шаблоны и REST API Resource Manager для развертывания ресурсов в Azure.  

Вы можете включить шаблон в текст запроса или связать с файлом. Файл может быть локальным или внешним, доступным по универсальному коду ресурса (URI). Если шаблон находится в учетной записи хранения, то во время развертывания можно ограничить доступ к шаблону и предоставить маркер подписанного URL-адреса (SAS).

## <a name="deployment-scope"></a>Область развертывания

Можно создавать решения развертывания для подписки Azure или группу ресурсов в подписке. В большинстве случаев его ориентации на развертывание в группе ресурсов. Используйте развертывания подписки для применения политик и назначения ролей для подписки. Развертывания подписки также использовать для создания группы ресурсов и развернуть в ней ресурсы. В зависимости от области развертывания использовать другие команды.

Для развертывания на **группы ресурсов**, использовать [создания развертываний -](/rest/api/resources/deployments/createorupdate).

Для развертывания на **подписки**, использовать [развертываний — создать в пределах подписки](/rest/api/resources/deployments/createorupdateatsubscriptionscope).

В примерах в этой статье используется развертываний групп ресурсов. Дополнительные сведения о развертываниях подписки см. в разделе [создания группы ресурсов и ресурсов на уровне подписки](deploy-to-subscription.md).

## <a name="deploy-with-the-rest-api"></a>Развертывание с помощью REST API

1. Задайте [общие параметры и заголовки](/rest/api/azure/), включая маркеры аутентификации.

1. Создайте группу ресурсов, если у вас нет существующей группы ресурсов. Укажите идентификатор подписки, имя новой группы ресурсов и расположение, необходимое для решения. Дополнительную информацию см. в разделе [Создание группы ресурсов](/rest/api/resources/resourcegroups/createorupdate).

   ```HTTP
   PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>?api-version=2018-05-01
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

1. Создайте развертывание. Укажите идентификатор подписки, имя группы ресурсов, имя развертывания и ссылку на шаблон. Дополнительную информацию о файле шаблона см. в разделе [Файл параметров](#parameter-file). Чтобы больше узнать о REST API для создания группы ресурсов, ознакомьтесь с [созданием развертывания на основе шаблона](/rest/api/resources/deployments/createorupdate). Обратите внимание, что для параметра **mode** выбрано значение **Incremental**. Чтобы выполнить полное развертывание, установите для параметра **mode** значение **Complete**. Будьте внимательны при использовании полного режима, так как вы можете случайно удалить ресурсы, которые находятся не в шаблоне.

   ```HTTP
   PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2018-05-01
   ```

   С текстом запроса:

   ```json
   {
    "properties": {
      "templateLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
        "contentVersion": "1.0.0.0"
      },
      "mode": "Incremental",
      "parametersLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
        "contentVersion": "1.0.0.0"
      }
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
      "mode": "Incremental",
      "parametersLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
        "contentVersion": "1.0.0.0"
      },
      "debugSetting": {
        "detailLevel": "requestContent, responseContent"
      }
    }
   }
   ```

    Можно настроить учетную запись хранения для использования маркера подписанного URL-адреса (SAS). Дополнительные сведения см. в статье [Делегирование доступа с помощью подписанного URL-адреса](https://docs.microsoft.com/rest/api/storageservices/delegating-access-with-a-shared-access-signature).

1. Вместо создания ссылки на файлы для шаблона и параметров их можно включить в тексте запроса.

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
            "name": "[variables('storageAccountName')]",
            "apiVersion": "2018-02-01",
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

1. Получите состояние развертывания шаблона. Чтобы узнать больше, ознакомьтесь с [получением сведений о развертывании на основе шаблона](/rest/api/resources/deployments/get).

   ```HTTP
   GET https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2018-05-01
   ```

## <a name="redeploy-when-deployment-fails"></a>Повторное развертывание при сбое развертывания

Эта функция также называется *отката в случае ошибки*. Если развертывание завершается ошибкой, вы можете автоматически повторно развернуть ресурс, успешно развернутый ранее, из журнала развертывания. Чтобы задать повторное развертывание, укажите свойство `onErrorDeployment` в тексте запроса. Эта функция полезна в том случае, если установлена ли у известного рабочего состояния для развертывания инфраструктуры и это должно быть возвращен в категорию. Существует несколько предостережений, а также ограничения:

- Повторное развертывание выполняется так же, как она была запущена ранее, с теми же параметрами. Не может изменять параметры.
- Предыдущее развертывание выполняется с использованием [полный режим](./deployment-modes.md#complete-mode). Будут удалены все ресурсы, не включены в предыдущем развертывании, а все конфигурации ресурсов задаются к предыдущим значениям. Убедитесь, что вы полностью понимаете [режимов развертывания](./deployment-modes.md).
- Повторное развертывание влияет только на ресурсы, любые изменения данных не изменяются.
- Эта функция поддерживается только на развертывания группы ресурсов, подписки уровня развертывания. Дополнительные сведения о развертывании уровня подписки, см. в разделе [создания группы ресурсов и ресурсов на уровне подписки](./deploy-to-subscription.md).

Для этого развертывания должны иметь уникальные имена, чтобы их можно было идентифицировать в журнале. Если у вас нет уникальных имен, текущее неудачное развертывание может перезаписать более раннее успешное развертывание, зафиксированное в журнале. Этот параметр можно использовать только с развертываниями корневого уровня. Повторное развертывание из вложенных шаблонов не поддерживается.

Чтобы повторно выполнить последнее успешное развертывание, если текущее развертывание завершается сбоем, используйте следующий код:

```json
{
  "properties": {
    "templateLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
      "contentVersion": "1.0.0.0"
    },
    "mode": "Incremental",
    "parametersLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
      "contentVersion": "1.0.0.0"
    },
    "onErrorDeployment": {
      "type": "LastSuccessful",
    }
  }
}
```

Чтобы повторно выполнить определенное развертывание, если текущее развертывание завершается сбоем, используйте следующий код:

```json
{
  "properties": {
    "templateLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
      "contentVersion": "1.0.0.0"
    },
    "mode": "Incremental",
    "parametersLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
      "contentVersion": "1.0.0.0"
    },
    "onErrorDeployment": {
      "type": "SpecificDeployment",
      "deploymentName": "<deploymentname>"
    }
  }
}
```

Указанное развертывание должно быть успешным.

## <a name="parameter-file"></a>Файл параметров

Если вы используете файл параметров для передачи значений параметров во время развертывания, то необходимо создать JSON-файл с форматом, как в примере ниже.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "webSiteName": {
            "value": "ExampleSite"
        },
        "webSiteHostingPlanName": {
            "value": "DefaultPlan"
        },
        "webSiteLocation": {
            "value": "West US"
        },
        "adminPassword": {
            "reference": {
               "keyVault": {
                  "id": "/subscriptions/{guid}/resourceGroups/{group-name}/providers/Microsoft.KeyVault/vaults/{vault-name}"
               },
               "secretName": "sqlAdminPassword"
            }
        }
   }
}
```

Размер файла параметров не может быть более 64 КБ.

Если требуется предоставить конфиденциальное значение для параметра (например, пароль), добавьте это значение в хранилище ключей. Получите хранилище ключей во время развертывания, как показано в предыдущем примере. Дополнительные сведения см. в статье [Передача безопасных значений в процессе развертывания](resource-manager-keyvault-parameter.md). 

## <a name="next-steps"></a>Дальнейшие действия

- Сведения о том, как указать способ обработки ресурсов, которые существуют в группе ресурсов, но не определены в шаблоне, см. в [описании режимов развертывания с помощью Azure Resource Manager](deployment-modes.md).
- Сведения об обработке асинхронных операций REST см. в статье [Track asynchronous Azure operations](resource-manager-async-operations.md) (Отслеживание асинхронных операций Azure).
- Пример развертывания ресурсов с помощью клиентской библиотеки .NET см. в статье [Развертывание виртуальной машины Azure с помощью C# и шаблона Resource Manager](../virtual-machines/windows/csharp-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
- Сведения об определении параметров в шаблоне см. в разделе [Создание шаблонов](resource-group-authoring-templates.md#parameters).
- Инструкции по использованию Resource Manager для эффективного управления подписками в организациях см. в статье [Корпоративный каркас Azure: рекомендуемая система управления подписками](/azure/architecture/cloud-adoption-guide/subscription-governance).
