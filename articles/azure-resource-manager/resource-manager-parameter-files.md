---
title: Создание файла параметров
description: Создание файла параметров для передачи значений во время развертывания шаблона Azure Resource Manager
ms.topic: conceptual
ms.date: 08/21/2019
ms.openlocfilehash: 7333e33af90ff7883b53a24bacdc63b42bf107ee
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/17/2019
ms.locfileid: "74149453"
---
# <a name="create-resource-manager-parameter-file"></a>Создание файла параметров диспетчер ресурсов

Вместо передачи параметров в виде встроенных значений в сценарии вам может быть проще использовать JSON-файл, содержащий значения параметров. В этой статье показано, как создать файл параметров.

## <a name="parameter-file"></a>Файл параметров

Файл параметров имеет следующий формат:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "<first-parameter-name>": {
            "value": "<first-value>"
        },
        "<second-parameter-name>": {
            "value": "<second-value>"
        }
    }
}
```

Обратите внимание, что значения параметров хранятся в файле параметров в виде обычного текста. Этот подход работает для нечувствительных значений, например для указания номера SKU для ресурса. Он не работает для конфиденциальных значений, таких как пароли. Если необходимо передать конфиденциальное значение в качестве параметра, сохраните значение в хранилище ключей и укажите ссылку на хранилище ключей в файле параметров. Конфиденциальное значение безопасно извлекается во время развертывания.

Следующий файл параметров включает в себя текстовое значение и значение, которое хранится в хранилище ключей.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "<first-parameter-name>": {
            "value": "<first-value>"
        },
        "<second-parameter-name>": {
            "reference": {
                "keyVault": {
                    "id": "<resource-id-key-vault>"
                },
                "secretName": "<secret-name>"
            }
        }
    }
}
```

Дополнительные сведения об использовании значений из хранилища ключей см. в разделе [использование Azure Key Vault для передачи безопасного значения параметра во время развертывания](resource-manager-keyvault-parameter.md).

## <a name="define-parameter-values"></a>Определение значений параметров

Чтобы выяснить, как определить значения параметров, откройте развертываемый шаблон. Взгляните на раздел Parameters шаблона. В следующем примере показаны параметры из шаблона.

```json
"parameters": {
    "storagePrefix": {
      "type": "string",
      "maxLength": 11
    },
    "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS",
        "Premium_LRS"
      ]
    }
}
```

Первым заметьтем является имя каждого параметра. Значения в файле параметров должны совпадать с именами.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storagePrefix": {
        },
        "storageAccountType": {
        }
    }
}
```

Обратите внимание на тип параметра. Значения в файле параметров должны иметь одинаковые типы. Для этого шаблона можно указать оба параметра как строки.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storagePrefix": {
            "value": ""
        },
        "storageAccountType": {
            "value": ""
        }
    }
}
```

Затем найдите значение по умолчанию. Если параметр имеет значение по умолчанию, можно указать значение, но это не обязательно.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storagePrefix": {
            "value": "" // This value must be provided.
        },
        "storageAccountType": {
            "value": "" // This value is optional. Template will use default value if not provided.
        }
    }
}
```

Наконец, просмотрите допустимые значения и любые ограничения, такие как максимальная длина. Они сообщают диапазон значений, которые можно указать для параметра.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storagePrefix": {
            "value": "storage"
        },
        "storageAccountType": {
            "value": "Standard_ZRS"
        }
    }
}
```

## <a name="parameter-type-formats"></a>Форматы типов параметров

В следующем примере показаны форматы различных типов параметров.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "exampleString": {
            "value": "test string"
        },
        "exampleInt": {
            "value": 4
        },
        "exampleBool": {
            "value": true
        },
        "exampleArray": {
            "value": [
                "value 1",
                "value 2"
            ]
        },
        "exampleObject": {
            "value": {
                "property1": "value1",
                "property2": "value2"
            }
        }
   }
}
```

## <a name="file-name"></a>Имя файла

Общее соглашение об именовании файла параметров — добавить **Параметры** к имени шаблона. Например, если шаблон имеет имя **azuredeploy. JSON**, файл параметров будет называться **azuredeploy. parameters. JSON**. Это соглашение об именовании помогает увидеть подключение между шаблоном и параметрами.

Для развертывания в разных средах Создайте более одного файла параметров. При именовании файла параметров добавьте способ для указания его использования. Например, используйте **azuredeploy. parameters-dev. JSON** и **azuredeploy. parameters-"произ. JSON** "


## <a name="parameter-precedence"></a>Приоритет параметров

Вы можете использовать в ходе одной операции развертывания как встроенные параметры, так и локальный файл параметров. Например, часть значений можно указать в локальном файле параметров, а другую часть — в команде развертывания. Если значения для одного параметра указаны одновременно и в локальном файле параметров, и в командной строке, более высокий приоритет имеет значение из командной строки.

Тем не менее при использовании внешнего файла параметров нельзя передавать другие значения в командной строке или локальном файле. Все встроенные параметры игнорируются. Все значения параметров следует указать во внешнем файле.

## <a name="parameter-name-conflicts"></a>Конфликты параметров

Если шаблон содержит параметр, имя которого совпадает с именем одного из параметров в команде PowerShell, параметр из шаблон отображается с постфиксом **FromTemplate**. Предположим, что параметр **ResourceGroupName** в шаблоне конфликтует с параметром **ResourceGroupName** в командлете [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment). Будет предложено указать значение для параметра **ResourceGroupNameFromTemplate**. Эту путаницу можно избежать, используя имена параметров, которые не используются для команд развертывания.

## <a name="next-steps"></a>Дополнительная информация

- Сведения о том, как определить параметры в шаблоне, см. в разделе [Параметры в Azure Resource Manager Templates](template-parameters.md).
- Дополнительные сведения об использовании значений из хранилища ключей см. в разделе [использование Azure Key Vault для передачи безопасного значения параметра во время развертывания](resource-manager-keyvault-parameter.md).
- Дополнительные сведения о параметрах см. [в разделе Параметры в Azure Resource Manager Templates](template-parameters.md).
