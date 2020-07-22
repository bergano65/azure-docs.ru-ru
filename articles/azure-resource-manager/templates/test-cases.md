---
title: Тестовые случаи для набора средств тестирования
description: Описание тестов, выполняемых набором средств тестирования шаблонов ARM.
ms.topic: conceptual
ms.date: 06/19/2020
ms.author: tomfitz
author: tfitzmac
ms.openlocfilehash: 5c18a2658ba1af9370699004860d1743603e8143
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85255844"
---
# <a name="default-test-cases-for-arm-template-test-toolkit"></a>Тестовые случаи по умолчанию для набора средств тестирования шаблонов ARM

В этой статье описываются тесты по умолчанию, которые выполняются с помощью [набора средств тестирования шаблонов](test-toolkit.md). В нем приводятся примеры, которые пройдут тест или не прошли его. Он включает имя каждого теста.

## <a name="use-correct-schema"></a>Использовать правильную схему

Имя теста: **схема Деплойменттемплате правильная**

В шаблоне необходимо указать допустимое значение схемы.

В следующем примере выполняется **Передача** этого теста.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "resources": []
}
```

Свойство схемы в шаблоне должно иметь одну из следующих схем:

* `https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#`
* `https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#`
* `https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#`
* `https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#`
* `https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json`

## <a name="parameters-must-exist"></a>Параметры должны существовать

Имя теста: **Свойства параметров должны существовать**

Шаблон должен иметь элемент Parameters. Параметры являются обязательными для многократного использования шаблонов в разных средах. Добавьте параметры в шаблон для значений, которые изменяются при развертывании в различных средах.

Следующий пример **передает** этот тест:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "vmName": {
          "type": "string",
          "defaultValue": "linux-vm",
          "metadata": {
            "description": "Name for the Virtual Machine."
          }
      }
  },
  ...
```

## <a name="declared-parameters-must-be-used"></a>Необходимо использовать объявленные параметры

Имя теста: **необходимо указать ссылку на параметры**

Чтобы сократить путаницу в шаблоне, удалите все параметры, которые определены, но не используются. Этот тест находит все параметры, которые не используются в любом месте шаблона. Устранение неиспользуемых параметров также упрощает развертывание шаблона, поскольку нет необходимости предоставлять ненужные значения.

## <a name="secure-parameters-cant-have-hardcoded-default"></a>Параметры безопасности не могут иметь жестко заданные значения по умолчанию

Имя теста: **Параметры безопасной строки не могут иметь значение по умолчанию**

Не заполните жестко запрограммированное значение по умолчанию для безопасного параметра в шаблоне. Пустая строка подходит для значения по умолчанию.

Типы **SecureString** или **SecureObject** используются для параметров, содержащих конфиденциальные значения, например пароли. Если параметр использует безопасный тип, значение параметра не записывается в журнал и не сохраняется в журнале развертывания. Это действие не позволит злонамеренному пользователю обнаружить конфиденциальное значение.

Однако при предоставлении значения по умолчанию это значение может быть обнаружено любым пользователем, имеющим доступ к шаблону или журналу развертывания.

Следующий пример приводит к **сбою** теста:

```json
"parameters": {
    "adminPassword": {
        "defaultValue": "HardcodedPassword",
        "type": "SecureString"
    }
}
```

Следующий пример **пройдет** этот тест:

```json
"parameters": {
    "adminPassword": {
        "type": "SecureString"
    }
}
```

## <a name="location-uses-parameter"></a>В расположении используется параметр

Имя теста: **расположение не должно быть жестко запрограммированным**

Пользователям шаблона могут быть доступны ограниченные регионы. Если задать расположение ресурса `"[resourceGroup().location]"` , Группа ресурсов может быть создана в регионе, к которому другие пользователи не могут получить доступ. Эти пользователи не смогут использовать шаблон.

При определении расположения для каждого ресурса используйте параметр, который по умолчанию использует расположение группы ресурсов. Предоставляя этот параметр, пользователи могут использовать значение по умолчанию, если это удобно, но также указывать другое расположение.

Следующий пример **не позволяет выполнить** этот тест, так как расположение в ресурсе имеет значение `resourceGroup().location` .

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "name": "storageaccount1",
            "location": "[resourceGroup().location]",
            "kind": "StorageV2",
            "sku": {
                "name": "Premium_LRS",
                "tier": "Premium"
            }
        }
    ]
}
```

В следующем примере используется параметр location, но этот тест **завершается неудачей** , так как параметр Location по умолчанию использует жестко заданное расположение.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "westus"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "name": "storageaccount1",
            "location": "[parameters('location')]",
            "kind": "StorageV2",
            "sku": {
                "name": "Premium_LRS",
                "tier": "Premium"
            }
        }
    ],
    "outputs": {}
}
```

Вместо этого создайте параметр, который по умолчанию имеет расположение группы ресурсов, но позволяет пользователям указать другое значение. В следующем примере выполняется **Передача** этого теста.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
                "description": "Location for the resources."
            }
        }
     },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "name": "storageaccount1",
            "location": "[parameters('location')]",
            "kind": "StorageV2",
            "sku": {
                "name": "Premium_LRS",
                "tier": "Premium"
            }
        }
    ],
    "outputs": {}
}
```

## <a name="resources-should-have-location"></a>Ресурсы должны иметь расположение

Имя теста: **ресурсы должны иметь расположение**

Для расположения ресурса должно быть задано [выражение шаблона](template-expressions.md) или `global` . В выражении шаблона обычно используется параметр location, описанный в предыдущем тесте.

В следующем примере **происходит сбой** этого теста, так как расположение не является выражением или `global` .

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "functions": [],
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "name": "storageaccount1",
            "location": "westus",
            "kind": "StorageV2",
            "sku": {
                "name": "Premium_LRS",
                "tier": "Premium"
            }
        }
    ],
    "outputs": {}
}
```

В следующем примере выполняется **Передача** этого теста.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "functions": [],
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Maps/accounts",
            "apiVersion": "2020-02-01-preview",
            "name": "demoMap",
            "location": "global",
            "sku": {
                "name": "S0"
            }
        }
    ],
    "outputs": {
    }
}
```

В следующем примере также **продается** этот тест.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
                "description": "Location for the resources."
            }
        }
     },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "name": "storageaccount1",
            "location": "[parameters('location')]",
            "kind": "StorageV2",
            "sku": {
                "name": "Premium_LRS",
                "tier": "Premium"
            }
        }
    ],
    "outputs": {}
}
```

## <a name="vm-size-uses-parameter"></a>Размер виртуальной машины использует параметр

Имя теста: **Размер виртуальной машины должен быть параметром**

Не кодировать размер виртуальной машины. Укажите параметр, чтобы пользователи шаблона могли изменять размер развернутой виртуальной машины.

Следующий пример приводит к **сбою** этого теста.

```json
"hardwareProfile": {
    "vmSize": "Standard_D2_v3"
}
```

Вместо этого укажите параметр.

```json
"vmSize": {
    "type": "string",
    "defaultValue": "Standard_A2_v2",
    "metadata": {
        "description": "Size for the Virtual Machine."
    }
},
```

Затем задайте для виртуальной машины размер, равный этому параметру.

```json
"hardwareProfile": {
    "vmSize": "[parameters('vmSize')]"
},
```

## <a name="min-and-max-values-are-numbers"></a>Значения min и Max являются числами

Имя теста: **минимальное и максимальное значения — числа** .

Если для параметра определены значения min и Max, укажите их как числа.

Следующий пример приводит к **сбою** теста:

```json
"exampleParameter": {
    "type": "int",
    "minValue": "0",
    "maxValue": "10"
},
```

Вместо этого укажите значения в виде чисел. Следующий пример **передает** этот тест:

```json
"exampleParameter": {
    "type": "int",
    "minValue": 0,
    "maxValue": 10
},
```

Это предупреждение также появляется при предоставлении минимального или максимального значения, но не другого.

## <a name="artifacts-parameter-defined-correctly"></a>Параметр артефактов определен правильно

Имя теста: **артефакты — параметр**

При включении параметров для `_artifactsLocation` и `_artifactsLocationSasToken` Используйте правильные значения по умолчанию и типы. Для прохождения этого теста должны выполняться следующие условия.

* Если вы предоставляете один параметр, необходимо указать другой.
* `_artifactsLocation`должен быть **строковым**
* `_artifactsLocation`в основном шаблоне должно быть значение по умолчанию
* `_artifactsLocation`не может иметь значение по умолчанию во вложенном шаблоне 
* `_artifactsLocation`должен иметь `"[deployment().properties.templateLink.uri]"` URL-адрес необработанного репозитория или его значение по умолчанию
* `_artifactsLocationSasToken`должен быть **secureString**
* `_artifactsLocationSasToken`может иметь только пустую строку для значения по умолчанию
* `_artifactsLocationSasToken`не может иметь значение по умолчанию во вложенном шаблоне 

## <a name="declared-variables-must-be-used"></a>Необходимо использовать объявленные переменные

Имя теста: **необходимо указать ссылки на переменные**

Чтобы сократить путаницу в шаблоне, удалите все переменные, которые определены, но не используются. Этот тест находит все переменные, которые нигде не используются в шаблоне.

## <a name="dynamic-variable-should-not-use-concat"></a>Динамическая переменная не должна использовать Concat

Имя теста: **динамические ссылки на переменные не должны использовать Concat**

Иногда необходимо динамически создать переменную на основе значения другой переменной или параметра. Не используйте функцию [concat](template-functions-string.md#concat) при задании значения. Вместо этого следует использовать объект, который включает доступные параметры, и динамически получать одно из свойств объекта во время развертывания.

В следующем примере выполняется **Передача** этого теста. Переменная **куррентимаже** динамически задается во время развертывания.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "osType": {
          "type": "string",
          "allowedValues": [
              "Windows",
              "Linux"
          ]
      }
  },
  "variables": {
    "imageOS": {
        "Windows": {
            "image": "Windows Image"
        },
        "Linux": {
            "image": "Linux Image"
        }
    },
    "currentImage": "[variables('imageOS')[parameters('osType')].image]"
  },
  "resources": [],
  "outputs": {
      "result": {
          "type": "string",
          "value": "[variables('currentImage')]"
      }
  }
}
```

## <a name="use-recent-api-version"></a>Использовать последнюю версию API

Имя теста: **Апиверсионс должно быть последним**

Версия API для каждого ресурса должна использовать последнюю версию. Тест оценивает версию, используемую для версий, доступных для этого типа ресурсов.

## <a name="use-hardcoded-api-version"></a>Использовать жестко закодированную версию API

Имя теста: **поставщики Апиверсионс не разрешены**

Версия API для типа ресурса определяет доступные свойства. Предоставьте жестко запрограммированную версию API в шаблоне. Не извлекайте версию API, определенную во время развертывания. Вы не узнаете, какие свойства доступны.

Следующий пример приводит к **сбою** этого теста.

```json
"resources": [
    {
        "type": "Microsoft.Compute/virtualMachines",
        "apiVersion": "[providers('Microsoft.Compute', 'virtualMachines').apiVersions[0]]",
        ...
    }
]
```

В следующем примере выполняется **Передача** этого теста.

```json
"resources": [
    {
       "type": "Microsoft.Compute/virtualMachines",
       "apiVersion": "2019-12-01",
       ...
    }
]
```

## <a name="properties-cant-be-empty"></a>Свойства не могут быть пустыми

Имя теста: **шаблон не должен содержать пробелы**

Не следует жестко задавать для свойств пустое значение. Пустые значения включают пустые и пустые строки, объекты или массивы. Если свойству было присвоено пустое значение, удалите это свойство из шаблона. Однако можно установить свойство в пустое значение во время развертывания, например с помощью параметра.

## <a name="use-resource-id-functions"></a>Использование функций идентификатора ресурса

Имя теста: **идентификаторы должны быть производными от идентификаторы ресурсов**

При указании идентификатора ресурса используйте одну из функций идентификатора ресурса. Допустимые функции:

* [resourceId](template-functions-resource.md#resourceid)
* [subscriptionResourceId](template-functions-resource.md#subscriptionresourceid)
* [tenantResourceId](template-functions-resource.md#tenantresourceid)
* [extensionResourceId](template-functions-resource.md#extensionresourceid)

Не используйте функцию concat для создания идентификатора ресурса. Следующий пример приводит к **сбою** этого теста.

```json
"networkSecurityGroup": {
    "id": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/networkSecurityGroups/', variables('networkSecurityGroupName'))]"
}
```

Следующий пример **пройдет** этот тест.

```json
"networkSecurityGroup": {
    "id": "[resourceId('Microsoft.Network/networkSecurityGroups', variables('networkSecurityGroupName'))]"
}
```

## <a name="resourceid-function-has-correct-parameters"></a>Функция ResourceId имеет правильные параметры

Имя теста: **идентификаторы ресурсов не должно содержать**

При создании идентификаторов ресурсов не используйте ненужные функции для необязательных параметров. По умолчанию функция [resourceId](template-functions-resource.md#resourceid) использует текущую подписку и группу ресурсов. Эти значения указывать не нужно.  

Следующий пример приводит к **сбою** этого теста, так как вам не нужно указывать идентификатор текущей подписки и имя группы ресурсов.

```json
"networkSecurityGroup": {
    "id": "[resourceId(subscription().subscriptionId, resourceGroup().name, 'Microsoft.Network/networkSecurityGroups', variables('networkSecurityGroupName'))]"
}
```

Следующий пример **пройдет** этот тест.

```json
"networkSecurityGroup": {
    "id": "[resourceId('Microsoft.Network/networkSecurityGroups', variables('networkSecurityGroupName'))]"
}
```

Этот тест применяется к:

* [resourceId](template-functions-resource.md#resourceid)
* [subscriptionResourceId](template-functions-resource.md#subscriptionresourceid)
* [tenantResourceId](template-functions-resource.md#tenantresourceid)
* [extensionResourceId](template-functions-resource.md#extensionresourceid)
* [reference](template-functions-resource.md#reference)
* [list*](template-functions-resource.md#list)

Для `reference` и `list*` Проверка **завершается ошибкой** при использовании `concat` для создания идентификатора ресурса.

## <a name="dependson-cant-be-conditional"></a>dependsOn не может быть условным

Имя теста: **DependsOn не должно быть условным**

При задании зависимостей развертывания не используйте функцию [If](template-functions-logical.md#if) для проверки условия. Если один ресурс зависит от ресурса, который [условно развернут](conditional-resource-deployment.md), установите зависимость так же, как и для любого ресурса. Если условный ресурс не развернут, Azure Resource Manager автоматически удаляет его из необходимых зависимостей.

Следующий пример приводит к **сбою** этого теста.

```json
"dependsOn": [
    "[if(equals(parameters('newOrExisting'),'new'), variables('storageAccountName'), '')]"
]
```

Следующий пример **пройдет** этот тест.

```json
"dependsOn": [
    "[variables('storageAccountName')]"
]
```

## <a name="nested-or-linked-deployments-cant-use-debug"></a>Вложенные или связанные развертывания не могут использовать отладку

Имя теста: **ресурсы развертывания не должны быть отладкой**

При определении [вложенного или связанного шаблона](linked-templates.md) с типом ресурса **Microsoft. Resources/deployments** можно включить отладку для этого шаблона. Отладка подходит, если необходимо протестировать шаблон, но его необходимо включить, когда будете готовы использовать шаблон в рабочей среде.

## <a name="admin-user-names-cant-be-literal-value"></a>Имя пользователя администратора не может быть литеральным значением

Имя теста: **AdminUsername не должен быть литералом**

При задании имени пользователя администратора не используйте литеральное значение.

Следующий пример приводит к **сбою** теста:

```json
"osProfile":  {
    "adminUserName":  "myAdmin"
},
```

Вместо этого используйте параметр. Следующий пример **передает** этот тест:

```json
"osProfile": {
    "adminUsername": "[parameters('adminUsername')]"
}
```

## <a name="use-latest-vm-image"></a>Использовать последнюю версию образа виртуальной машины

Имя теста: в **образах виртуальных машин должна использоваться последняя версия**

Если шаблон содержит виртуальную машину с образом, убедитесь, что она использует последнюю версию образа.

## <a name="use-stable-vm-images"></a>Использование стабильных образов виртуальных машин

Имя теста: " **virtual-machines" — "не требуется — предварительная версия** "

Виртуальные машины не должны использовать предварительные образы.

Следующий пример приводит к **сбою** этого теста.

```json
"imageReference": {
    "publisher": "Canonical",
    "offer": "UbuntuServer",
    "sku": "16.04-LTS",
    "version": "latest-preview"
}
```

В следующем примере выполняется **Передача** этого теста.

```json
"imageReference": {
    "publisher": "Canonical",
    "offer": "UbuntuServer",
    "sku": "16.04-LTS",
    "version": "latest"
},
```

## <a name="dont-use-managedidentity-extension"></a>Не использовать расширение ManagedIdentity

Имя теста: **манажедидентитекстенсион не должно использоваться**

Не применяйте расширение ManagedIdentity к виртуальной машине. Дополнительные сведения см. в статье [как прерывать использование расширения управляемых удостоверений виртуальной машины и как приступить к использованию службы метаданных экземпляра Azure](../../active-directory/managed-identities-azure-resources/howto-migrate-vm-extension.md).

## <a name="outputs-cant-include-secrets"></a>Выходные данные не могут включать секреты

Имя теста: **выходные данные не должны содержать секреты**

Не включайте в раздел Outputs какие бы то ни было значения, которые потенциально предоставляют секреты. Выходные данные шаблона хранятся в журнале развертывания, чтобы злонамеренный пользователь мог найти эту информацию.

Следующий пример приводит к **сбою** теста, так как он включает в себя безопасный параметр в выходном значении.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "secureParam": {
            "type": "securestring"
        }
    },
    "functions": [],
    "variables": {},
    "resources": [],
    "outputs": {
        "badResult": {
            "type": "string",
            "value": "[concat('this is the value ', parameters('secureParam'))]"
        }
    }
}
```

В следующем примере **происходит сбой** , поскольку в выходных данных используется функция [List *](template-functions-resource.md#list) .

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageName": {
            "type": "string"
        }
    },
    "functions": [],
    "variables": {},
    "resources": [],
    "outputs": {
        "badResult": {
            "type": "object",
            "value": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('storageName')), '2019-06-01')]"
        }
    }
}
```

## <a name="next-steps"></a>Дальнейшие шаги

Дополнительные сведения о запуске набора средств тестирования см. в статье [Использование набора средств тестирования ARM](test-toolkit.md).