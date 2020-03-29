---
title: Создание файла параметров
description: Создание файла параметров для передачи значений во время развертывания шаблона управления ресурсами Azure
ms.topic: conceptual
ms.date: 08/21/2019
ms.openlocfilehash: bb52d9c5ebcb0820362e5de3d6b24b0b18d742e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76155627"
---
# <a name="create-resource-manager-parameter-file"></a>Создание параметра ресурсного менеджера

Вместо передачи параметров в виде встроенных значений в сценарии вам может быть проще использовать JSON-файл, содержащий значения параметров. В этой статье показано, как создать файл параметров.

## <a name="parameter-file"></a>Файл параметров

Файл параметра имеет следующий формат:

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

Обратите внимание, что значения параметра хранятся в виде обычного текста в файле параметра. Этот подход работает для значений, которые не являются чувствительными, таких как указание SKU для ресурса. Он не работает для чувствительных значений, таких как пароли. Если вам необходимо передать конфиденциальное значение в качестве параметра, храните значение в хранилище ключей и ссылка на хранилище ключей в файле параметра. Чувствительное значение надежно извлекается во время развертывания.

Следующий файл параметра включает простое значение текста и значение, которое хранится в хранилище ключей.

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

Для получения дополнительной информации об использовании значений из хранилища ключей [см.](key-vault-parameter.md)

## <a name="define-parameter-values"></a>Определение значений параметров

Чтобы определить значения параметров, откройте развертываемый шаблон. Посмотрите на раздел параметров шаблона. В следующем примере показаны параметры из шаблона.

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

Первой деталью, которое следует заметить, является название каждого параметра. Значения в файле параметра должны совпадать с именами.

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

Обратите внимание на тип параметра. Значения в файле параметра должны иметь одинаковые типы. Для этого шаблона вы можете предоставить оба параметра в виде строк.

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

Далее ищите значение по умолчанию. Если параметр имеет значение по умолчанию, вы можете предоставить значение, но вам не нужно.

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

Наконец, посмотрите на допустимые значения и любые ограничения, такие как максимальная длина. Они говорят вам диапазон значений, которые вы можете обеспечить для параметра.

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

## <a name="parameter-type-formats"></a>Форматы типа параметра

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

Общая конвенция для именования файла параметра заключается в **добавлении параметров .параметров** к имени шаблона. Например, если шаблон называется **azuredeploy.json,** ваш параметр называется **azuredeploy.parameters.json.** Эта конвенция именования поможет вам увидеть связь между шаблоном и параметрами.

Чтобы развернуть в различных средах, создайте несколько файлов параметров. При наименовании файла параметра добавьте способ идентификации его использования. Например, используйте **azuredeploy.parameters-dev.json** и **azuredeploy.parameters-prod.json**


## <a name="parameter-precedence"></a>Приоритет параметров

Вы можете использовать в ходе одной операции развертывания как встроенные параметры, так и локальный файл параметров. Например, часть значений можно указать в локальном файле параметров, а другую часть — в команде развертывания. Если значения для одного параметра указаны одновременно и в локальном файле параметров, и в командной строке, более высокий приоритет имеет значение из командной строки.

Тем не менее при использовании внешнего файла параметров нельзя передавать другие значения в командной строке или локальном файле. Все вливые параметры игнорируются. Все значения параметров следует указать во внешнем файле.

## <a name="parameter-name-conflicts"></a>Конфликты параметров

Если шаблон содержит параметр, имя которого совпадает с именем одного из параметров в команде PowerShell, параметр из шаблон отображается с постфиксом **FromTemplate**. Предположим, что параметр **ResourceGroupName** в шаблоне конфликтует с параметром **ResourceGroupName** в командлете [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment). Будет предложено указать значение для параметра **ResourceGroupNameFromTemplate**. Можно избежать этой путаницы, используя имена параметров, которые не используются для команд развертывания.

## <a name="next-steps"></a>Дальнейшие действия

- Чтобы понять, как определить параметры в шаблоне, [см.](template-parameters.md)
- Для получения дополнительной информации об использовании значений из хранилища ключей [см.](key-vault-parameter.md)
- Более подробную информацию о параметрах можно узнать [в шаблонах «Параметры» в шаблонах управления ресурсами Azure.](template-parameters.md)
