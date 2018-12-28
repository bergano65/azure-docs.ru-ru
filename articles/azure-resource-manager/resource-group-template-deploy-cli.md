---
title: Развертывание ресурсов с помощью интерфейса командной строки Azure и шаблона | Документация Майкрософт
description: Узнайте, как использовать Azure Resource Manager и Azure CLI для развертывания ресурсов в Azure. Эти ресурсы определяются в шаблоне Resource Manager.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.assetid: 493b7932-8d1e-4499-912c-26098282ec95
ms.service: azure-resource-manager
ms.devlang: azurecli
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/14/2018
ms.author: tomfitz
ms.openlocfilehash: 5f27b34bda930d020461bd5f4f7986091ffd5549
ms.sourcegitcommit: b254db346732b64678419db428fd9eb200f3c3c5
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/14/2018
ms.locfileid: "53413665"
---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-cli"></a>Развертывание ресурсов с использованием шаблонов Resource Manager и Azure CLI

В этой статье объясняется, как использовать Azure CLI и шаблоны Resource Manager для развертывания ресурсов в Azure. Если вы не знакомы с основными понятиями, связанными с развертыванием решений Azure и управлением ими, см. [обзор Azure Resource Manager](resource-group-overview.md).  

Развертываемый шаблон Resource Manager может быть локальным файлом на вашем компьютере или внешним файлом, расположенным в репозитории, например в GitHub. Шаблон, который вы развернете в этой статье, доступен в разделе [шаблона учетной записи хранения на GitHub](https://github.com/Azure/azure-quickstart-templates/blob/master/101-storage-account-create/azuredeploy.json).

[!INCLUDE [sample-cli-install](../../includes/sample-cli-install.md)]

Если у вас не установлен интерфейс командной строки Azure, можете использовать [Cloud Shell](#deploy-template-from-cloud-shell).

## <a name="deploy-local-template"></a>Развертывание локального шаблона

При развертывании ресурсов в Azure выполните следующие действия:

1. Вход в учетную запись Azure
2. Создайте группу ресурсов, которая послужит в качестве контейнера для развертываемых ресурсов. Имя группы ресурсов может содержать только буквы, цифры, точки, знаки подчеркивания, дефисы и скобки. Оно может содержать до 90 знаков и не должно заканчиваться точкой.
3. Разверните в группе ресурсов шаблон, определяющий ресурсы, которые необходимо создать.

Шаблон может включать параметры, которые позволяют настроить развертывание. Например, вы можете предоставить значения, предназначенные для конкретной среды (такой как среда разработки, тестирования и рабочая среда). Пример шаблона определяет параметр для номера SKU учетной записи хранения. 

В следующем примере создается группа ресурсов и развертывается шаблон с локального компьютера:

```azurecli-interactive
az group create --name ExampleGroup --location "Central US"
az group deployment create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS
```

Развертывание может занять несколько минут. По завершении появится сообщение, содержащее результат:

```azurecli
"provisioningState": "Succeeded",
```

## <a name="deploy-external-template"></a>Развертывание внешнего шаблона

Шаблоны Resource Manager можно хранить не на локальном компьютере, а на внешнем источнике. Вы можете хранить шаблоны в репозитории системы управления версиями (например, GitHub). А также их можно хранить в учетной записи хранения Azure для общего доступа в организации.

Для развертывания внешнего шаблона используйте параметр **template-uri**. Используйте универсальный код ресурса (URI) в примере для развертывания примера шаблона из GitHub.
   
```azurecli-interactive
az group create --name ExampleGroup --location "Central US"
az group deployment create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json" \
  --parameters storageAccountType=Standard_GRS
```

В предыдущем примере для шаблона требуется общедоступный код URI, который подходит для большинства сценариев, так как шаблон не должен содержать конфиденциальные данные. Если необходимо указать конфиденциальные данные (например, пароль администратора), то передайте это значение с помощью безопасного параметра. Но если вы не хотите, чтобы шаблон был общедоступным, можно защитить его, сохранив в закрытом контейнере хранилища. Сведения о развертывании шаблона, требующего маркер подписанного URL-адреса (SAS), см. в статье [Развертывание частного шаблона Resource Manager с использованием токена SAS и Azure PowerShell](resource-manager-cli-sas-token.md).

[!INCLUDE [resource-manager-cloud-shell-deploy.md](../../includes/resource-manager-cloud-shell-deploy.md)]

В Cloud Shell введите следующие команды:

```azurecli-interactive
az group create --name examplegroup --location "South Central US"
az group deployment create --resource-group examplegroup \
  --template-uri <copied URL> \
  --parameters storageAccountType=Standard_GRS
```

## <a name="deploy-to-more-than-one-resource-group-or-subscription"></a>Развертывание в несколько групп ресурсов или подписок

Обычно развертывание всех ресурсов в шаблоне выполняется в отдельную группу ресурсов. Тем не менее возможны ситуации, когда необходимо развернуть набор ресурсов одновременно, но при этом разместить их в отдельных подписках или группах ресурсов. Развертывание можно выполнять только в пять групп ресурсов в рамках одного развертывания. Дополнительные сведения см. в разделе [Развертывание ресурсов Azure в нескольких подписках или группах ресурсов](resource-manager-cross-resource-group-deployment.md).

## <a name="redeploy-when-deployment-fails"></a>Повторное развертывание при сбое развертывания

Для неудачных развертываний можно задать автоматическое повторное выполнение более ранних развертываний, зафиксированных в журнале. Для этого развертывания должны иметь уникальные имена, чтобы их можно было идентифицировать в журнале. Если у вас нет уникальных имен, текущее неудачное развертывание может перезаписать более раннее успешное развертывание, зафиксированное в журнале. Этот параметр можно использовать только с развертываниями корневого уровня. Повторное развертывание из вложенных шаблонов не поддерживается.

Чтобы выполнить повторно последнее успешное развертывание, добавьте параметр `--rollback-on-error` в качестве флага.

```azurecli-interactive
az group deployment create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS \
  --rollback-on-error
```

Чтобы выполнить повторно конкретное развертывание, используйте параметр `--rollback-on-error` и укажите имя развертывания.

```azurecli-interactive
az group deployment create \
  --name ExampleDeployment02 \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS \
  --rollback-on-error ExampleDeployment01
```

Указанное развертывание должно быть успешным.

## <a name="parameters"></a>Параметры

Чтобы передать значения параметров, можно использовать встроенные параметры или файл параметров. В предыдущих примерах в этой статье используются встроенные параметры.

### <a name="inline-parameters"></a>Встроенные параметры

Чтобы передать встроенные параметры, укажите значения в `parameters`. Например, строки и массив передаются в шаблон из оболочки Bash следующим образом:

```azurecli
az group deployment create \
  --resource-group testgroup \
  --template-file demotemplate.json \
  --parameters exampleString='inline string' exampleArray='("value1", "value2")'
```

Можно также получить содержимое файла и предоставлять его в виде встроенного параметра.

```azurecli
az group deployment create \
  --resource-group testgroup \
  --template-file demotemplate.json \
  --parameters exampleString=@stringContent.txt exampleArray=@arrayContent.json
```

Получение значения параметра из файла полезно в тех случаях, когда есть необходимость предоставить значения конфигурации. Например, вы можете предоставить [значения cloud-init для виртуальной машины Linux](../virtual-machines/linux/using-cloud-init.md).

Файл ArrayContent.json имеет следующий формат:

```json
[
    "value1",
    "value2"
]
```

### <a name="parameter-files"></a>Файлы параметров

Вместо передачи параметров в виде встроенных значений в сценарии вам может быть проще использовать JSON-файл, содержащий значения параметров. Файл параметров должен находиться в локальной среде. Внешние файлы параметров не поддерживаются в Azure CLI.

Файл параметров должен быть в указанном ниже формате.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
     "storageAccountType": {
         "value": "Standard_GRS"
     }
  }
}
```

Обратите внимание, что раздел parameters содержит имя параметра, которое совпадает с параметром, определенным в шаблоне (storageAccountType). Файл параметров содержит значение для параметра. Во время развертывания это значение автоматически передается в шаблон. Вы можете создать несколько файлов параметров и передавать нужный файл параметров в зависимости от конкретной ситуации. 

Скопируйте предыдущий пример и сохраните его как файл с именем `storage.parameters.json`.

Для передачи локального файла параметров используйте `@`, чтобы указать локальный файл с именем storage.parameters.json.

```azurecli-interactive
az group deployment create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters @storage.parameters.json
```

### <a name="parameter-precedence"></a>Приоритет параметров

Вы можете использовать в ходе одной операции развертывания как встроенные параметры, так и локальный файл параметров. Например, часть значений можно указать в локальном файле параметров, а другую часть — в команде развертывания. Если значения для одного параметра указаны одновременно и в локальном файле параметров, и в командной строке, более высокий приоритет имеет значение из командной строки.

```azurecli
az group deployment create \
  --resource-group testgroup \
  --template-file demotemplate.json \
  --parameters @demotemplate.parameters.json \
  --parameters exampleArray=@arrtest.json
```


## <a name="test-a-template-deployment"></a>Тестовое развертывание шаблона

Чтобы проверить шаблон и значения параметров без фактического развертывания ресурсов, используйте [az group deployment validate](/cli/azure/group/deployment#az-group-deployment-validate). 

```azurecli-interactive
az group deployment validate \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters @storage.parameters.json
```

Если ошибок не обнаружено, то команда возвращает сведения о тестовом развертывании. В частности, обратите внимание, что **error** имеет значение null.

```azurecli
{
  "error": null,
  "properties": {
      ...
```

Если обнаруживается ошибка, то команда возвращает сообщение об ошибке. Например, попытка передать недопустимое значение для номера SKU учетной записи возвращает следующую ошибку:

```azurecli
{
  "error": {
    "code": "InvalidTemplate",
    "details": null,
    "message": "Deployment template validation failed: 'The provided value 'badSKU' for the template parameter 
      'storageAccountType' at line '13' and column '20' is not valid. The parameter value is not part of the allowed 
      value(s): 'Standard_LRS,Standard_ZRS,Standard_GRS,Standard_RAGRS,Premium_LRS'.'.",
    "target": null
  },
  "properties": null
}
```

Если шаблон содержит синтаксическую ошибку, команда возвращает сообщение об ошибке, указывающее, что ей не удалось проанализировать шаблон. В сообщении указывается номер строки и позиция ошибки синтаксического анализа.

```azurecli
{
  "error": {
    "code": "InvalidTemplate",
    "details": null,
    "message": "Deployment template parse failed: 'After parsing a value an unexpected character was encountered:
      \". Path 'variables', line 31, position 3.'.",
    "target": null
  },
  "properties": null
}
```

## <a name="next-steps"></a>Дополнительная информация
* В примерах этой статьи ресурсы развертываются в группу ресурсов в подписке по умолчанию. Чтобы использовать другую подписку, см. статью [Управление несколькими подписками Azure](/cli/azure/manage-azure-subscriptions-azure-cli).
* Сведения о том, как указать способ обработки ресурсов, которые существуют в группе ресурсов, но не определены в шаблоне, см. в [описании режимов развертывания с помощью Azure Resource Manager](deployment-modes.md).
* Сведения об определении параметров в шаблоне см. в статье [Описание структуры и синтаксиса шаблонов Azure Resource Manager](resource-group-authoring-templates.md).
* Советы по устранению распространенных ошибок развертывания см. в разделе [Устранение распространенных ошибок развертывания в Azure с помощью Azure Resource Manager](resource-manager-common-deployment-errors.md).
* Сведения о развертывании шаблона, которому нужен токен SAS, см. в статье [Развертывание частного шаблона с помощью маркера SAS](resource-manager-cli-sas-token.md).
* Для безопасного развертывания службы в нескольких регионах обратитесь к статье о [диспетчере развертывания Azure](deployment-manager-overview.md).
