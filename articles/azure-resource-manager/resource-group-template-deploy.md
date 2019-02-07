---
title: Развертывание ресурсов с помощью PowerShell и шаблона | Документация Майкрософт
description: Узнайте, как использовать Azure Resource Manager и Azure PowerShell для развертывания ресурсов в Azure. Эти ресурсы определяются в шаблоне Resource Manager.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.assetid: 55903f35-6c16-4c6d-bf52-dbf365605c3f
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/30/2019
ms.author: tomfitz
ms.openlocfilehash: ff67474566737ca75206cd1237c89f873cb173a8
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55489858"
---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-powershell"></a>Развертывание ресурсов с использованием шаблонов Resource Manager и Azure PowerShell

В этой статье объясняется, как использовать Azure PowerShell с шаблонами Resource Manager для развертывания ресурсов в Azure. Если вы не знакомы с основными понятиями, связанными с развертыванием решений Azure и управлением ими, см. [обзор Azure Resource Manager](resource-group-overview.md).

Развертываемый шаблон Resource Manager может быть локальным файлом на вашем компьютере или внешним файлом, расположенным в репозитории, например в GitHub. Шаблон, который вы развернете в этой статье, доступен в разделе [шаблона учетной записи хранения на GitHub](https://github.com/Azure/azure-quickstart-templates/blob/master/101-storage-account-create/azuredeploy.json).

При необходимости установите модуль Azure PowerShell, следуя инструкциям из [руководства по Azure PowerShell](/powershell/azure/overview), а затем выполните команду `Connect-AzAccount`, чтобы подключиться к Azure.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]


<a id="deploy-local-template" />

## <a name="deploy-a-template-from-your-local-machine"></a>Развертывание шаблона с локального компьютера

При развертывании ресурсов в Azure выполните следующие действия:

1. Вход в учетную запись Azure
2. Создайте группу ресурсов, которая послужит в качестве контейнера для развертываемых ресурсов. Имя группы ресурсов может содержать только буквы, цифры, точки, знаки подчеркивания, дефисы и скобки. Оно может содержать до 90 знаков и не должно заканчиваться точкой.
3. Разверните в группе ресурсов шаблон, определяющий ресурсы, которые необходимо создать.

Шаблон может включать параметры, которые позволяют настроить развертывание. Например, вы можете предоставить значения, предназначенные для конкретной среды (такой как среда разработки, тестирования и рабочая среда). Пример шаблона определяет параметр для номера SKU учетной записи хранения.

В следующем примере создается группа ресурсов и развертывается шаблон с локального компьютера:

```powershell
Connect-AzAccount

Select-AzSubscription -SubscriptionName <yourSubscriptionName>
 
New-AzResourceGroup -Name ExampleResourceGroup -Location "South Central US"
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\storage.json -storageAccountType Standard_GRS
```

Развертывание может занять несколько минут. По завершении появится сообщение, содержащее результат:

```powershell
ProvisioningState       : Succeeded
```

## <a name="deploy-a-template-from-an-external-source"></a>Развертывание шаблона из внешнего источника

Шаблоны Resource Manager можно хранить не на локальном компьютере, а на внешнем источнике. Вы можете хранить шаблоны в репозитории системы управления версиями (например, GitHub). А также их можно хранить в учетной записи хранения Azure для общего доступа в организации.

Для развертывания внешнего шаблона используйте параметр **TemplateUri**. Используйте универсальный код ресурса (URI) в примере для развертывания примера шаблона из GitHub.

```powershell
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json `
  -storageAccountType Standard_GRS
```

В предыдущем примере для шаблона требуется общедоступный код URI, который подходит для большинства сценариев, так как шаблон не должен содержать конфиденциальные данные. Если необходимо указать конфиденциальные данные (например, пароль администратора), то передайте это значение с помощью безопасного параметра. Но если вы не хотите, чтобы шаблон был общедоступным, можно защитить его, сохранив в закрытом контейнере хранилища. Сведения о развертывании шаблона, требующего маркер подписанного URL-адреса (SAS), см. в статье [Развертывание частного шаблона Resource Manager с использованием токена SAS и Azure PowerShell](resource-manager-powershell-sas-token.md).

[!INCLUDE [resource-manager-cloud-shell-deploy.md](../../includes/resource-manager-cloud-shell-deploy.md)]

В Cloud Shell введите следующие команды:

```powershell
New-AzResourceGroup -Name ExampleResourceGroup -Location "South Central US"
New-AzResourceGroupDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateUri <copied URL> `
  -storageAccountType Standard_GRS
```

## <a name="deploy-to-more-than-one-resource-group-or-subscription"></a>Развертывание в несколько групп ресурсов или подписок

Обычно развертывание всех ресурсов в шаблоне выполняется в отдельную группу ресурсов. Тем не менее возможны ситуации, когда необходимо развернуть набор ресурсов одновременно, но при этом разместить их в отдельных подписках или группах ресурсов. Развертывание можно выполнять только в пять групп ресурсов в рамках одного развертывания. Дополнительные сведения см. в разделе [Развертывание ресурсов Azure в нескольких подписках или группах ресурсов](resource-manager-cross-resource-group-deployment.md).

<a id="parameter-file" />

## <a name="parameters"></a>Параметры

Чтобы передать значения параметров, можно использовать встроенные параметры или файл параметров. В предыдущих примерах в этой статье используются встроенные параметры.

### <a name="inline-parameters"></a>Встроенные параметры

Чтобы передать встроенные параметры, укажите название параметра с помощью команды `New-AzResourceGroupDeployment`. Например, строки и массив передаются в шаблон следующим образом.

```powershell
$arrayParam = "value1", "value2"
New-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile c:\MyTemplates\demotemplate.json `
  -exampleString "inline string" `
  -exampleArray $arrayParam
```

Можно также получить содержимое файла и предоставлять его в виде встроенного параметра.

```powershell
$arrayParam = "value1", "value2"
New-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile c:\MyTemplates\demotemplate.json `
  -exampleString $(Get-Content -Path c:\MyTemplates\stringcontent.txt -Raw) `
  -exampleArray $arrayParam
```

Получение значения параметра из файла полезно в тех случаях, когда есть необходимость предоставить значения конфигурации. Например, вы можете предоставить [значения cloud-init для виртуальной машины Linux](../virtual-machines/linux/using-cloud-init.md).

### <a name="parameter-files"></a>Файлы параметров

Вместо передачи параметров в виде встроенных значений в сценарии вам может быть проще использовать JSON-файл, содержащий значения параметров. Файл параметров может быть локальным или храниться во внешнем расположении с доступным адресом URI.

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

Чтобы передать локальный файл параметров, используйте параметр **TemplateParameterFile**:

```powershell
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\storage.json `
  -TemplateParameterFile c:\MyTemplates\storage.parameters.json
```

Чтобы передать внешний файл параметров, используйте параметр **TemplateParameterUri**:

```powershell
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json `
  -TemplateParameterUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.parameters.json
```

### <a name="parameter-precedence"></a>Приоритет параметров

Вы можете использовать в ходе одной операции развертывания как встроенные параметры, так и локальный файл параметров. Например, часть значений можно указать в локальном файле параметров, а другую часть — в команде развертывания. Если значения для одного параметра указаны одновременно и в локальном файле параметров, и в командной строке, более высокий приоритет имеет значение из командной строки.

Тем не менее при использовании внешнего файла параметров нельзя передавать другие значения в командной строке или локальном файле. Если в параметре **TemplateParameterUri** указан файл параметров, все параметры командной строки игнорируются. Все значения параметров следует указать во внешнем файле. Если в шаблоне используется конфиденциальное значение, которое нельзя включать в файл параметров, то его можно передать в хранилище ключей. Кроме того, можно динамически указать значения всех параметров в командной строке.

### <a name="parameter-name-conflicts"></a>Конфликты параметров

Если шаблон содержит параметр, имя которого совпадает с именем одного из параметров в команде PowerShell, параметр из шаблон отображается с постфиксом **FromTemplate**. Предположим, что параметр **ResourceGroupName** в шаблоне конфликтует с параметром **ResourceGroupName** в командлете [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment). Будет предложено указать значение для параметра **ResourceGroupNameFromTemplate**. В общем случае следует избегать этой путаницы, не присваивая параметрам имена параметров, используемых для операций развертывания.

## <a name="test-a-template-deployment"></a>Тестовое развертывание шаблона

Чтобы проверить шаблон и значения параметров без фактического развертывания ресурсов, используйте [Test-AzureRmResourceGroupDeployment](/powershell/module/az.resources/test-azresourcegroupdeployment). 

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\storage.json -storageAccountType Standard_GRS
```

Если ошибок не обнаружено, то команда завершается без ответа. Если обнаруживается ошибка, то команда возвращает сообщение об ошибке. Например, попытка передать недопустимое значение для номера SKU учетной записи возвращает следующую ошибку:

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile c:\MyTemplates\storage.json -storageAccountType badSku

Code    : InvalidTemplate
Message : Deployment template validation failed: 'The provided value 'badSku' for the template parameter 'storageAccountType'
          at line '15' and column '24' is not valid. The parameter value is not part of the allowed value(s):
          'Standard_LRS,Standard_ZRS,Standard_GRS,Standard_RAGRS,Premium_LRS'.'.
Details :
```

Если шаблон содержит синтаксическую ошибку, команда возвращает сообщение об ошибке, указывающее, что ей не удалось проанализировать шаблон. В сообщении указывается номер строки и позиция ошибки синтаксического анализа.

```powershell
Test-AzResourceGroupDeployment : After parsing a value an unexpected character was encountered: 
  ". Path 'variables', line 31, position 3.
```

## <a name="next-steps"></a>Дополнительная информация
* В примерах этой статьи ресурсы развертываются в группу ресурсов в подписке по умолчанию. Чтобы использовать другую подписку, см. статью [Управление несколькими подписками Azure](/powershell/azure/manage-subscriptions-azureps).
* Сведения о том, как указать способ обработки ресурсов, которые существуют в группе ресурсов, но не определены в шаблоне, см. в [описании режимов развертывания с помощью Azure Resource Manager](deployment-modes.md).
* Сведения об определении параметров в шаблоне см. в статье [Описание структуры и синтаксиса шаблонов Azure Resource Manager](resource-group-authoring-templates.md).
* Советы по устранению распространенных ошибок развертывания см. в разделе [Устранение распространенных ошибок развертывания в Azure с помощью Azure Resource Manager](resource-manager-common-deployment-errors.md).
* Сведения о развертывании шаблона, которому нужен токен SAS, см. в статье [Развертывание частного шаблона с помощью маркера SAS](resource-manager-powershell-sas-token.md).
* Для безопасного развертывания службы в нескольких регионах обратитесь к статье о [диспетчере развертывания Azure](deployment-manager-overview.md).
