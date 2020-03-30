---
title: Развертывание ресурсов с помощью PowerShell и шаблона
description: Для развертывания ресурсов в Azure и Azure PowerShell используйте диспетчер ресурсов Azure и Azure PowerShell. Эти ресурсы определяются в шаблоне Resource Manager.
ms.topic: conceptual
ms.date: 03/16/2020
ms.openlocfilehash: e595aa8f86a24e59c8e00d24ea8e9dcb0875a8f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80153273"
---
# <a name="deploy-resources-with-arm-templates-and-azure-powershell"></a>Развертывание ресурсов с шаблонами ARM и Azure PowerShell

Узнайте, как использовать Azure PowerShell с помощью шаблонов Azure Resource Manager (ARM) для развертывания ресурсов в Azure. Для получения дополнительной информации о концепциях развертывания и [template deployment overview](overview.md)управления решениями Azure см.

## <a name="deployment-scope"></a>Область развертывания

Развертывание можно нацелить на группу ресурсов, подписку, группу управления или арендатора. В большинстве случаев развертывание будет нацелено на группу ресурсов. Чтобы применить политики и назначения ролей в более широком диапазоне, используйте подписку, группу управления или развертывания клиентов. При развертывании в подписке можно создать группу ресурсов и развернуть ресурсы в ней.

В зависимости от сферы развертывания используются различные команды.

Для развертывания в **группе ресурсов**используйте [New-AzResourceGroupDeployment:](/powershell/module/az.resources/new-azresourcegroupdeployment)

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateFile <path-to-template>
```

Для развертывания **в подписке**используйте New-AzSubscriptionDeployment:

```azurepowershell
New-AzSubscriptionDeployment -Location <location> -TemplateFile <path-to-template>
```

Для получения дополнительной информации о развертывании уровня подписки [см.](deploy-to-subscription.md)

Для развертывания в **группе управления**используйте [New-AzManagementGroupDeployment.](/powershell/module/az.resources/New-AzManagementGroupDeployment)

```azurepowershell
New-AzManagementGroupDeployment -Location <location> -TemplateFile <path-to-template>
```

Для получения дополнительной информации о развертываниях группы управления [см.](deploy-to-management-group.md)

Для развертывания **в аренду**используйте [New-AzTenantDeployment.](/powershell/module/az.resources/new-aztenantdeployment)

```azurepowershell
New-AzTenantDeployment -Location <location> -TemplateFile <path-to-template>
```

Для получения дополнительной информации о развертываниях уровня клиента [см.](deploy-to-tenant.md)

Примеры в этой статье используют развертывания групп ресурсов.

## <a name="prerequisites"></a>Предварительные требования

Для развертывания нужен шаблон. Если у вас его еще нет, загрузите и сохраните [пример шаблона](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json) из репо шаблонов Azure quickstart. В этой статье используется локальный файл с именем **c:\MyTemplates\azuredeploy.json**.

Если вы не используете оболочку Azure Cloud Для развертывания шаблонов, вам необходимо установить Azure PowerShell и подключиться к Azure:

- **Установите командлеты Azure PowerShell на локальном компьютере.** Дополнительные сведения см. в статье [Начало работы с Azure PowerShell](/powershell/azure/get-started-azureps).
- **Подключитесь к Azure с помощью командлета [Connect-AZAccount](/powershell/module/az.accounts/connect-azaccount)**. Если у вас несколько подписок Azure, выполните также командлет [Set-AzContext](/powershell/module/Az.Accounts/Set-AzContext). См. дополнительные сведения в статье [Use multiple Azure subscriptions](/powershell/azure/manage-subscriptions-azureps) (Использование нескольких подписок Azure).

## <a name="deploy-local-template"></a>Развертывание локального шаблона

Следующий пример создает группу ресурсов и развертывает шаблон из локальной машины. Имя группы ресурсов может содержать только буквы, цифры, точки, знаки подчеркивания, дефисы и скобки. Оно может содержать до 90 знаков и не должно заканчиваться точкой.

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
  -TemplateFile c:\MyTemplates\azuredeploy.json
```

Развертывание может занять несколько минут.

## <a name="deploy-remote-template"></a>Развертывание удаленного шаблона

Вместо того, чтобы хранить шаблоны ARM на локальной машине, вы можете предпочесть хранить их во внешнем месте. Вы можете хранить шаблоны в репозитории системы управления версиями (например, GitHub). А также их можно хранить в учетной записи хранения Azure для общего доступа в организации.

Для развертывания внешнего шаблона используйте параметр **TemplateUri.** Используйте универсальный код ресурса (URI) в примере для развертывания примера шаблона из GitHub.

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

В предыдущем примере для шаблона требуется общедоступный код URI, который подходит для большинства сценариев, так как шаблон не должен содержать конфиденциальные данные. Если необходимо указать конфиденциальные данные (например, пароль администратора), то передайте это значение с помощью безопасного параметра. Но если вы не хотите, чтобы шаблон был общедоступным, можно защитить его, сохранив в закрытом контейнере хранилища. Сведения о развертывании шаблона, требующего маркер подписанного URL-адреса (SAS), см. в статье [Развертывание частного шаблона Resource Manager с использованием токена SAS и Azure PowerShell](secure-template-with-sas-token.md). Чтобы пройти через учебник, [см.](template-tutorial-use-key-vault.md)

## <a name="deploy-from-azure-cloud-shell"></a>Развертывание из облачной оболочки Azure

Для развертывания шаблона можно использовать [Azure Cloud Shell](https://shell.azure.com). Чтобы развернуть внешний шаблон, укажите URI шаблона. Чтобы развернуть локальный шаблон, сначала необходимо загрузить шаблон для Cloud Shell в учетную запись хранения. Чтобы отправить файлы в Cloud Shell, щелкните значок **Upload/Download files** (Отправить/скачать файлы) в окне Cloud Shell.

Чтобы открыть оболочку облака, просмотрите [https://shell.azure.com](https://shell.azure.com)или выберите **Try-It** из следующего раздела кода:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

Чтобы вставить код в Cloud Shell, щелкните правой кнопкой мыши внутри окна, а затем выберите **Paste** (Вставить).

## <a name="pass-parameter-values"></a>Передача значений параметров

Чтобы передать значения параметров, можно использовать встроенные параметры или файл параметров.

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

Получение значения параметра из файла полезно в тех случаях, когда есть необходимость предоставить значения конфигурации. Например, вы можете предоставить [значения cloud-init для виртуальной машины Linux](../../virtual-machines/linux/using-cloud-init.md).

Если вам нужно пройти в массив объектов, создайте хэш-таблицы в PowerShell и добавьте их в массив. Передайте этот массив в качестве параметра во время развертывания.

```powershell
$hash1 = @{ Name = "firstSubnet"; AddressPrefix = "10.0.0.0/24"}
$hash2 = @{ Name = "secondSubnet"; AddressPrefix = "10.0.1.0/24"}
$subnetArray = $hash1, $hash2
New-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile c:\MyTemplates\demotemplate.json `
  -exampleArray $subnetArray
```

### <a name="parameter-files"></a>Файлы параметров

Вместо передачи параметров в виде встроенных значений в сценарии вам может быть проще использовать JSON-файл, содержащий значения параметров. Файл параметров может быть локальным или храниться во внешнем расположении с доступным адресом URI.

Для получения дополнительной информации о файле параметра [см.](parameter-files.md)

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

## <a name="test-template-deployments"></a>Проверка развертывания шаблонов

Чтобы проверить значения шаблона и параметров без фактического развертывания ресурсов, используйте [Test-AzResourceGroupDeployment.](/powershell/module/az.resources/test-azresourcegroupdeployment) 

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

- Чтобы откатить к успешному развертыванию при ошибке, просмейте [откат по ошибке для успешного развертывания.](rollback-on-error.md)
- Сведения о том, как указать способ обработки ресурсов, которые существуют в группе ресурсов, но не определены в шаблоне, см. в [описании режимов развертывания с помощью Azure Resource Manager](deployment-modes.md).
- Чтобы понять, как определить параметры в шаблоне, [см.](template-syntax.md)
- Сведения о развертывании шаблона, которому нужен токен SAS, см. в статье [Развертывание частного шаблона с помощью маркера SAS](secure-template-with-sas-token.md).
