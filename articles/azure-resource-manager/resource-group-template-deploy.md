---
title: Развертывание ресурсов с помощью PowerShell и шаблона | Документация Майкрософт
description: Используйте Azure Resource Manager и Azure PowerShell для развертывания ресурсов в Azure. Эти ресурсы определяются в шаблоне Resource Manager.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.assetid: 55903f35-6c16-4c6d-bf52-dbf365605c3f
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/28/2019
ms.author: tomfitz
ms.openlocfilehash: 2ef5cc702bd5035c958a8feb9b6f5051781cd3cc
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58649800"
---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-powershell"></a>Развертывание ресурсов с использованием шаблонов Resource Manager и Azure PowerShell

Узнайте, как использовать Azure PowerShell с шаблонами Resource Manager для развертывания ресурсов в Azure. См. дополнительные сведения о развертывании решений Azure и управлении ими в обзоре [Azure Resource Manager](resource-group-overview.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="deployment-scope"></a>Область развертывания

Можно создавать решения развертывания для подписки Azure или группу ресурсов в подписке. В большинстве случаев его ориентации на развертывание в группе ресурсов. Используйте развертывания подписки для применения политик и назначения ролей для подписки. Развертывания подписки также использовать для создания группы ресурсов и развернуть в ней ресурсы. В зависимости от области развертывания использовать другие команды.

Для развертывания на **группы ресурсов**, использовать [New AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment):

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateFile <path-to-template>
```

Для развертывания на **подписки**, использовать [New AzDeployment](/powershell/module/az.resources/new-azdeployment):

```azurepowershell
New-AzDeployment -Location <location> -TemplateFile <path-to-template>
```

В примерах в этой статье используется развертываний групп ресурсов. Дополнительные сведения о развертываниях подписки см. в разделе [создания группы ресурсов и ресурсов на уровне подписки](deploy-to-subscription.md).

## <a name="prerequisites"></a>Технические условия

Требуется шаблон для развертывания. Если вы еще не, скачайте и сохраните [пример шаблона](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json) из репозитория шаблонов быстрого запуска Azure. В этой статье используется локальный файл с именем **c:\MyTemplates\azuredeploy.json**.

Если вы не используете Azure Cloud shell можно развертывать шаблоны, вам потребуется установить Azure PowerShell и подключитесь к Azure.

- **Установите командлеты Azure PowerShell на локальном компьютере.** Дополнительные сведения см. в статье [Начало работы с Azure PowerShell](/powershell/azure/get-started-azureps).
- **Подключитесь к Azure с помощью командлета [Connect-AZAccount](/powershell/module/az.accounts/connect-azaccount)**. Если у вас несколько подписок Azure, выполните также командлет [Set-AzContext](/powershell/module/Az.Accounts/Set-AzContext). См. дополнительные сведения в статье [Use multiple Azure subscriptions](/powershell/azure/manage-subscriptions-azureps) (Использование нескольких подписок Azure).

## <a name="deploy-local-template"></a>Развертывание локального шаблона

Следующий пример создает группу ресурсов и развертывает шаблон с локального компьютера. Имя группы ресурсов может содержать только буквы, цифры, точки, знаки подчеркивания, дефисы и скобки. Оно может содержать до 90 знаков и не должно заканчиваться точкой.

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
  -TemplateFile c:\MyTemplates\azuredeploy.json
```

Развертывание может занять несколько минут.

## <a name="deploy-remote-template"></a>Развертывание удаленного шаблона

Шаблоны Resource Manager можно хранить не на локальном компьютере, а на внешнем источнике. Вы можете хранить шаблоны в репозитории системы управления версиями (например, GitHub). А также их можно хранить в учетной записи хранения Azure для общего доступа в организации.

Для развертывания внешнего шаблона используйте параметр **TemplateUri**. Используйте универсальный код ресурса (URI) в примере для развертывания примера шаблона из GitHub.

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

В предыдущем примере для шаблона требуется общедоступный код URI, который подходит для большинства сценариев, так как шаблон не должен содержать конфиденциальные данные. Если необходимо указать конфиденциальные данные (например, пароль администратора), то передайте это значение с помощью безопасного параметра. Но если вы не хотите, чтобы шаблон был общедоступным, можно защитить его, сохранив в закрытом контейнере хранилища. Сведения о развертывании шаблона, требующего маркер подписанного URL-адреса (SAS), см. в статье [Развертывание частного шаблона Resource Manager с использованием токена SAS и Azure PowerShell](resource-manager-powershell-sas-token.md). Изучите статью [Руководство. Интеграция с Azure Key Vault при развертывании шаблона Resource Manager](./resource-manager-tutorial-use-key-vault.md).

## <a name="deploy-from-azure-cloud-shell"></a>Развертывание из Azure Cloud shell

Для развертывания шаблона можно использовать [Azure Cloud Shell](https://shell.azure.com). Чтобы развернуть внешний шаблон, укажите URI шаблона. Чтобы развернуть локальный шаблон, сначала необходимо загрузить шаблон для Cloud Shell в учетную запись хранения. Чтобы отправить файлы в Cloud Shell, щелкните значок **Upload/Download files** (Отправить/скачать файлы) в окне Cloud Shell.

Чтобы открыть Cloud Shell, перейдите по ссылке [https://shell.azure.com](https://shell.azure.com) или выберите **Try It** (Пробная версия) в следующем окне с кодом:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

Чтобы вставить код в Cloud Shell, щелкните правой кнопкой мыши внутри окна, а затем выберите **Paste** (Вставить).

## <a name="redeploy-when-deployment-fails"></a>Повторное развертывание при сбое развертывания

Эта функция также называется *отката в случае ошибки*. Если развертывание завершается ошибкой, вы можете автоматически повторно развернуть ресурс, успешно развернутый ранее, из журнала развертывания. Чтобы запустить повторное развертывание, используйте параметр `-RollbackToLastDeployment` или `-RollBackDeploymentName` в команде развертывания. Эта функция полезна в том случае, если установлена ли у известного рабочего состояния для развертывания инфраструктуры и это должно быть возвращен в категорию. Существует несколько предостережений, а также ограничения:

- Повторное развертывание выполняется так же, как она была запущена ранее, с теми же параметрами. Не может изменять параметры.
- Предыдущее развертывание выполняется с использованием [полный режим](./deployment-modes.md#complete-mode). Будут удалены все ресурсы, не включены в предыдущем развертывании, а все конфигурации ресурсов задаются к предыдущим значениям. Убедитесь, что вы полностью понимаете [режимов развертывания](./deployment-modes.md).
- Повторное развертывание влияет только на ресурсы, любые изменения данных не изменяются.
- Эта функция поддерживается только на развертывания группы ресурсов, подписки уровня развертывания. Дополнительные сведения о развертывании уровня подписки, см. в разделе [создания группы ресурсов и ресурсов на уровне подписки](./deploy-to-subscription.md).

Для этого развертывания должны иметь уникальные имена, чтобы их можно было идентифицировать в журнале. Если у вас нет уникальных имен, текущее неудачное развертывание может перезаписать более раннее успешное развертывание, зафиксированное в журнале. Этот параметр можно использовать только с развертываниями корневого уровня. Повторное развертывание из вложенных шаблонов не поддерживается.

Чтобы выполнить повторно последнее успешное развертывание, добавьте параметр `-RollbackToLastDeployment` в качестве флага.

```azurepowershell-interactive
New-AzResourceGroupDeployment -Name ExampleDeployment02 `
  -ResourceGroupName $resourceGroupName `
  -TemplateFile c:\MyTemplates\azuredeploy.json `
  -RollbackToLastDeployment
```

Чтобы выполнить повторно конкретное развертывание, используйте параметр `-RollBackDeploymentName` и укажите имя развертывания.

```azurepowershell-interactive
New-AzResourceGroupDeployment -Name ExampleDeployment02 `
  -ResourceGroupName $resourceGroupName `
  -TemplateFile c:\MyTemplates\azuredeploy.json `
  -RollBackDeploymentName ExampleDeployment01
```

Указанное развертывание должно быть успешным.

## <a name="pass-parameter-values"></a>Передача значений параметров

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
  -TemplateFile c:\MyTemplates\azuredeploy.json `
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

## <a name="test-template-deployments"></a>Проверка развертывания шаблонов

Чтобы проверить шаблон и значения параметров без фактического развертывания ресурсов, используйте [Test-AzureRmResourceGroupDeployment](/powershell/module/az.resources/test-azresourcegroupdeployment). 

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\azuredeploy.json -storageAccountType Standard_GRS
```

Если ошибок не обнаружено, то команда завершается без ответа. Если обнаруживается ошибка, то команда возвращает сообщение об ошибке. Например, попытка передать недопустимое значение для номера SKU учетной записи возвращает следующую ошибку:

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile c:\MyTemplates\azuredeploy.json -storageAccountType badSku

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

## <a name="next-steps"></a>Дальнейшие действия

- Для безопасного развертывания службы в нескольких регионах обратитесь к статье о [диспетчере развертывания Azure](deployment-manager-overview.md).
- Сведения о том, как указать способ обработки ресурсов, которые существуют в группе ресурсов, но не определены в шаблоне, см. в [описании режимов развертывания с помощью Azure Resource Manager](deployment-modes.md).
- Сведения об определении параметров в шаблоне см. в статье [Описание структуры и синтаксиса шаблонов Azure Resource Manager](resource-group-authoring-templates.md).
- Сведения о развертывании шаблона, которому нужен токен SAS, см. в статье [Развертывание частного шаблона с помощью маркера SAS](resource-manager-powershell-sas-token.md).
