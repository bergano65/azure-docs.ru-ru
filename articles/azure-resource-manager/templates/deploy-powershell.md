---
title: Развертывание ресурсов с помощью PowerShell и шаблона
description: Используйте Azure Resource Manager и Azure PowerShell для развертывания ресурсов в Azure. Эти ресурсы определяются в шаблоне Resource Manager.
ms.topic: conceptual
ms.date: 03/16/2020
ms.openlocfilehash: e595aa8f86a24e59c8e00d24ea8e9dcb0875a8f4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80153273"
---
# <a name="deploy-resources-with-arm-templates-and-azure-powershell"></a>Развертывание ресурсов с помощью шаблонов ARM и Azure PowerShell

Узнайте, как использовать Azure PowerShell с шаблонами Azure Resource Manager (ARM) для развертывания ресурсов в Azure. Дополнительные сведения о концепциях развертывания и управления решениями Azure см. в разделе [Общие сведения о развертывании шаблонов](overview.md).

## <a name="deployment-scope"></a>Область развертывания

Вы можете выбрать для развертывания группу ресурсов, подписку, группу управления или клиент. В большинстве случаев вы планируете развертывание в группе ресурсов. Чтобы применить политики и назначения ролей в более масштабной области, используйте развертывание по подписке, группе управления или развертыванию клиента. При развертывании в подписке можно создать группу ресурсов и развернуть в ней ресурсы.

В зависимости от области развертывания используются разные команды.

Чтобы выполнить развертывание в **группе ресурсов**, используйте команду [New-азресаурцеграупдеплоймент](/powershell/module/az.resources/new-azresourcegroupdeployment).

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateFile <path-to-template>
```

Для развертывания в **подписке**используйте New-азсубскриптиондеплоймент:

```azurepowershell
New-AzSubscriptionDeployment -Location <location> -TemplateFile <path-to-template>
```

Дополнительные сведения о развертываниях уровня подписки см. в статье [Создание групп ресурсов и ресурсов на уровне подписки](deploy-to-subscription.md).

Для развертывания в **группе управления**используйте [New-азманажементграупдеплоймент](/powershell/module/az.resources/New-AzManagementGroupDeployment).

```azurepowershell
New-AzManagementGroupDeployment -Location <location> -TemplateFile <path-to-template>
```

Дополнительные сведения о развертывании на уровне группы управления см. [в разделе Создание ресурсов на уровне группы управления](deploy-to-management-group.md).

Для развертывания в **клиенте**используйте [New-азтенантдеплоймент](/powershell/module/az.resources/new-aztenantdeployment).

```azurepowershell
New-AzTenantDeployment -Location <location> -TemplateFile <path-to-template>
```

Дополнительные сведения о развертываниях на уровне клиента см. в разделе [Создание ресурсов на уровне клиента](deploy-to-tenant.md).

В примерах этой статьи используются развертывания групп ресурсов.

## <a name="prerequisites"></a>Предварительные условия

Вам нужен шаблон для развертывания. Если у вас его еще нет, скачайте и сохраните [Пример шаблона](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json) из репозитория шаблонов быстрого запуска Azure. В этой статье используется локальный файл с именем **c:\MyTemplates\azuredeploy.json**.

Если вы не используете Azure Cloud Shell для развертывания шаблонов, необходимо установить Azure PowerShell и подключиться к Azure:

- **Установите командлеты Azure PowerShell на локальном компьютере.** Дополнительные сведения см. в статье [Начало работы с Azure PowerShell](/powershell/azure/get-started-azureps).
- **Подключитесь к Azure с помощью командлета [Connect-AZAccount](/powershell/module/az.accounts/connect-azaccount)**. Если у вас несколько подписок Azure, выполните также командлет [Set-AzContext](/powershell/module/Az.Accounts/Set-AzContext). См. дополнительные сведения в статье [Use multiple Azure subscriptions](/powershell/azure/manage-subscriptions-azureps) (Использование нескольких подписок Azure).

## <a name="deploy-local-template"></a>Развертывание локального шаблона

В следующем примере создается группа ресурсов и развертывается шаблон на локальном компьютере. Имя группы ресурсов может содержать только буквы, цифры, точки, знаки подчеркивания, дефисы и скобки. Оно может содержать до 90 знаков и не должно заканчиваться точкой.

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
  -TemplateFile c:\MyTemplates\azuredeploy.json
```

Развертывание может занять несколько минут.

## <a name="deploy-remote-template"></a>Развертывание удаленного шаблона

Вместо того чтобы хранить шаблоны ARM на локальном компьютере, вы можете хранить их во внешнем расположении. Вы можете хранить шаблоны в репозитории системы управления версиями (например, GitHub). А также их можно хранить в учетной записи хранения Azure для общего доступа в организации.

Чтобы развернуть внешний шаблон, используйте параметр **TemplateUri** . Используйте универсальный код ресурса (URI) в примере для развертывания примера шаблона из GitHub.

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

В предыдущем примере для шаблона требуется общедоступный код URI, который подходит для большинства сценариев, так как шаблон не должен содержать конфиденциальные данные. Если необходимо указать конфиденциальные данные (например, пароль администратора), то передайте это значение с помощью безопасного параметра. Но если вы не хотите, чтобы шаблон был общедоступным, можно защитить его, сохранив в закрытом контейнере хранилища. Сведения о развертывании шаблона, требующего маркер подписанного URL-адреса (SAS), см. в статье [Развертывание частного шаблона Resource Manager с использованием токена SAS и Azure PowerShell](secure-template-with-sas-token.md). Дополнительные сведения см. в разделе [учебник. интеграция Azure Key Vault в развертывании шаблона ARM](template-tutorial-use-key-vault.md).

## <a name="deploy-from-azure-cloud-shell"></a>Развертывание из Azure Cloud Shell

Для развертывания шаблона можно использовать [Azure Cloud Shell](https://shell.azure.com). Чтобы развернуть внешний шаблон, укажите URI шаблона. Чтобы развернуть локальный шаблон, сначала необходимо загрузить шаблон для Cloud Shell в учетную запись хранения. Чтобы отправить файлы в Cloud Shell, щелкните значок **Upload/Download files** (Отправить/скачать файлы) в окне Cloud Shell.

Чтобы открыть Cloud Shell, перейдите по [https://shell.azure.com](https://shell.azure.com)адресу или выберите команду " **попробовать** " в следующем разделе кода:

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

Если необходимо передать массив объектов, создайте хэш-таблицы в PowerShell и добавьте их в массив. Передайте этот массив в качестве параметра во время развертывания.

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

Дополнительные сведения о файле параметров см. в разделе [Создание файла параметров Диспетчер ресурсов](parameter-files.md).

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

Чтобы протестировать шаблон и значения параметров без фактического развертывания ресурсов, используйте командлет [Test-азресаурцеграупдеплоймент](/powershell/module/az.resources/test-azresourcegroupdeployment). 

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

## <a name="next-steps"></a>Дальнейшие шаги

- Сведения о откате к успешному развертыванию при возникновении ошибки см. в разделе [откат при ошибке для успешного развертывания](rollback-on-error.md).
- Сведения о том, как указать способ обработки ресурсов, которые существуют в группе ресурсов, но не определены в шаблоне, см. в [описании режимов развертывания с помощью Azure Resource Manager](deployment-modes.md).
- Сведения о том, как определить параметры в шаблоне, см. [в разделе Общие сведения о структуре и синтаксисе шаблонов ARM](template-syntax.md).
- Сведения о развертывании шаблона, которому нужен токен SAS, см. в статье [Развертывание частного шаблона с помощью маркера SAS](secure-template-with-sas-token.md).
