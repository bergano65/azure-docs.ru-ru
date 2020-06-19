---
title: Использование сценариев развертывания в шаблонах | Документация Майкрософт
description: Использование сценариев развертывания в шаблонах Azure Resource Manager.
services: azure-resource-manager
author: mumian
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 05/20/2020
ms.author: jgao
ms.openlocfilehash: 24a0891b57f67bfb78cf3699bddbcf8d345ee679
ms.sourcegitcommit: a3c6efa4d4a48e9b07ecc3f52a552078d39e5732
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2020
ms.locfileid: "83708012"
---
# <a name="use-deployment-scripts-in-templates-preview"></a>Использование сценариев развертывания в шаблонах (предварительная версия)

Узнайте, как использовать сценарии развертывания в шаблонах ресурсов Azure. Используя новый тип ресурсов `Microsoft.Resources/deploymentScripts`, пользователи могут выполнять сценарии развертывания в шаблонах развертывания и просматривать результаты выполнения. Эти сценарии можно использовать для выполнения настраиваемых действий, в том числе с их помощью можно:

- добавить пользователей в каталог;
- выполнить операции плоскости данных, например копирование больших двоичных объектов или базы данных начальных значений;
- найти и проверить ключ лицензии;
- создать самозаверяющий сертификат;
- создать объект в Azure AD;
- найти блоки IP-адресов из пользовательской системы.

Преимущества сценария развертывания:

- Простое программирование, использование и отладка. Сценарии развертывания можно разрабатывать в средах разработки на свой выбор. Эти сценарии можно внедрять в шаблоны или во внешние файлы сценариев.
- Можно указать язык и платформу сценария. В настоящее время поддерживаются сценарии развертывания Azure PowerShell и Azure CLI в среде Linux.
- Можно указать удостоверения, используемые для выполнения сценариев. Сейчас поддерживаются только [управляемые удостоверения Azure, назначаемые пользователем](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md).
- В сценарий можно передавать аргументы командной строки.
- Можно указать выходные данные сценария и передать их в развертывание.

Ресурс сценария развертывания доступен только в регионах, где доступен экземпляр контейнера Azure.  Ознакомьтесь с разделом [Доступность ресурсов службы "Экземпляры контейнеров Azure" в регионах Azure](../../container-instances/container-instances-region-availability.md).

> [!IMPORTANT]
> Учетная запись хранения и экземпляр контейнера необходимы для выполнения сценария и устранения неполадок. Вы можете указать существующую учетную запись хранения. В противном случае учетная запись хранения вместе с экземпляром контейнера автоматически создается службой сценариев. Эти два создаваемых автоматически ресурса обычно удаляются службой сценариев, когда выполнение сценария развертывания достигает состояния завершения. Плата взимается за ресурсы, пока они не будут удалены. Дополнительные сведения в разделе об [очистке ресурсов сценариев развертывания](#clean-up-deployment-script-resources).

## <a name="prerequisites"></a>Предварительные требования

- **Управляемое удостоверение, назначаемое пользователем, с ролью участника в целевой группе ресурсов**. Это удостоверение используется для выполнения скриптов развертывания. Для выполнения операций вне группы ресурсов необходимо предоставить дополнительные разрешения. Например, назначьте удостоверение для уровня подписки, если хотите создать новую группу ресурсов.

  > [!NOTE]
  > Служба сценариев создает учетную запись хранения (если не указана существующая учетная запись хранения) и экземпляр контейнера в фоновом режиме.  Управляемое удостоверение, назначаемое пользователем, с ролью участника на уровне подписки является обязательным, если в подписке не зарегистрированы поставщики ресурсов учетных записей хранения Azure (Microsoft.Storage) и экземпляров контейнера Azure (Microsoft.ContainerInstance).

  Сведения о создании удостоверения см. в разделах [Создание, получение списка, удаление ролей и их назначение для управляемого удостоверения, назначаемого пользователем, с помощью портала Azure](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md), [Создание и удаление управляемых удостоверений, назначаемых пользователем, а также получение их списка с помощью Azure CLI](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md) и [Создание и удаление управляемых удостоверений, назначаемых пользователем, а также получение их списка с помощью Azure PowerShell](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md). Идентификатор удостоверения необходим при развертывании шаблона. Требуемый формат удостоверения:

  ```json
  /subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<IdentityID>
  ```

  Чтобы получить идентификатор, выполните следующий сценарий интерфейса командной строки или PowerShell, указав в нем имя группы ресурсов и имя удостоверения.

  # <a name="cli"></a>[CLI](#tab/CLI)

  ```azurecli-interactive
  echo "Enter the Resource Group name:" &&
  read resourceGroupName &&
  echo "Enter the managed identity name:" &&
  read idName &&
  az identity show -g jgaoidentity1008rg -n jgaouami --query id
  ```

  # <a name="powershell"></a>[PowerShell](#tab/PowerShell)

  ```azurepowershell-interactive
  $idGroup = Read-Host -Prompt "Enter the resource group name for the managed identity"
  $idName = Read-Host -Prompt "Enter the name of the managed identity"

  (Get-AzUserAssignedIdentity -resourcegroupname $idGroup -Name $idName).Id
  ```

  ---

- **Azure PowerShell** или **Azure CLI**. Список поддерживаемых версий Azure PowerShell приведен [здесь](https://mcr.microsoft.com/v2/azuredeploymentscripts-powershell/tags/list). Список поддерживаемых версий Azure CLI приведен [здесь](https://mcr.microsoft.com/v2/azuredeploymentscripts-powershell/tags/list).

    >[!IMPORTANT]
    > Сценарий развертывания использует доступные образы CLI из Реестра контейнеров Майкрософт (MCR). Сертификация образа CLI для сценария развертывания занимает около одного месяца. Не используйте версии CLI, выпущенные менее 30 дней назад. Чтобы найти даты выпуска образов, ознакомьтесь с [заметками о выпуске Azure CLI](https://docs.microsoft.com/cli/azure/release-notes-azure-cli?view=azure-cli-latest). Если используется неподдерживаемая версия, в сообщении об ошибке выводится список поддерживаемых версий.

    Эти версии не требуются для развертывания шаблонов. Однако они необходимы для локального тестирования сценариев развертывания. См. статью [Установка модуля Azure PowerShell](/powershell/azure/install-az-ps). Вы можете использовать предварительно настроенный образ Docker.  Ознакомьтесь с [настройкой среды разработки](#configure-development-environment).

## <a name="sample-templates"></a>Примеры шаблонов

Приведенный ниже код JSON является примером.  Последнюю схему шаблона можно найти [здесь](/azure/templates/microsoft.resources/deploymentscripts).

```json
{
  "type": "Microsoft.Resources/deploymentScripts",
  "apiVersion": "2019-10-01-preview",
  "name": "runPowerShellInline",
  "location": "[resourceGroup().location]",
  "kind": "AzurePowerShell", // or "AzureCLI"
  "identity": {
    "type": "userAssigned",
    "userAssignedIdentities": {
      "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myID": {}
    }
  },
  "properties": {
    "forceUpdateTag": 1,
    "containerSettings": {
      "containerGroupName": "mycustomaci"
    },
    "storageAccountSettings": {
      "storageAccountName": "myStorageAccount",
      "storageAccountKey": "myKey"
    },
    "azPowerShellVersion": "3.0",  // or "azCliVersion": "2.0.80"
    "arguments": "[concat('-name ', parameters('name'))]",
    "environmentVariables": [
      {
        "name": "someSecret",
        "secureValue": "if this is really a secret, don't put it here... in plain text..."
      }
    ],
    "scriptContent": "
      param([string] $name)
      $output = 'Hello {0}' -f $name
      Write-Output $output
      $DeploymentScriptOutputs = @{}
      $DeploymentScriptOutputs['text'] = $output
    ", // or "primaryScriptUri": "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-helloworld.ps1",
    "supportingScriptUris":[],
    "timeout": "PT30M",
    "cleanupPreference": "OnSuccess",
    "retentionInterval": "P1D"
  }
}
```

> [!NOTE]
> Этот пример предназначен только для демонстрации.  **scriptContent** и **primaryScriptUris** не могут существовать в одном шаблоне.

Сведения о значениях свойств.

- **Identity**. Служба сценариев развертывания использует назначаемое пользователем управляемое удостоверение для выполнения сценариев. Сейчас поддерживаются только управляемые удостоверения, назначаемые пользователем.
- **kind**. Укажите тип скрипта. В настоящее время поддерживаются сценарии Azure PowerShell и Azure CLI. Допустимые значения: **AzurePowerShell** и **AzureCLI**.
- **forceUpdateTag**. Изменение этого значения между развертываниями шаблона приводит к повторному выполнению сценария развертывания. Используйте функцию newGuid() или utcNow(), которая должна быть задана в качестве значения defaultValue параметра. Дополнительные сведения о выполнении скрипта несколько раз см. [здесь](#run-script-more-than-once).
- **containerSettings**. Укажите параметры для настройки экземпляра контейнера Azure.  **containerGroupName** используется для указания имени группы контейнеров.  Если этот параметр не указан, имя группы будет создано автоматически.
- **storageAccountSettings**. Укажите параметры для использования существующей учетной записи хранения. Если этот параметр не указан, учетная запись хранения будет создана автоматически. Ознакомьтесь с [использованием имеющейся учетной записи хранения](#use-an-existing-storage-account).
- **azPowerShellVersion**/**azCliVersion**. Укажите используемую версию модуля. Список поддерживаемых версий PowerShell и CLI см. в разделе [Предварительные требования](#prerequisites).
- **arguments**. Укажите значения параметров. Значения разделяются пробелами.
- **environmentVariables**. Укажите переменные среды, передаваемые в сценарий. Дополнительные сведения см. в статье [Разработка сценариев развертывания](#develop-deployment-scripts).
- **scriptContent**. Укажите содержимое скрипта. Чтобы запустить внешний сценарий, используйте `primaryScriptUri`. Примеры см. в разделах [Использование встроенных сценариев](#use-inline-scripts) и [Использование внешних сценариев](#use-external-scripts).
- **primaryScriptUri**. Укажите общедоступный URL-адрес основного сценария развертывания с поддерживаемыми расширениями файла.
- **supportingScriptUris**. Укажите массив общедоступных URL-адресов для вспомогательных файлов, которые вызываются посредством `ScriptContent` либо `PrimaryScriptUri`.
- **timeout**. Укажите максимально допустимое время выполнения скрипта в [формате ISO 8601](https://en.wikipedia.org/wiki/ISO_8601). Значение по умолчанию — **P1D**.
- **cleanupPreference**. Укажите параметры очистки ресурсов развертывания после перехода сценария в состояние завершения. Значение по умолчанию — **Always**, что означает удаление ресурсов независимо от состояния завершения (Succeeded, Failed, Canceled). Дополнительные сведения в разделе об [очистке ресурсов скриптов развертывания](#clean-up-deployment-script-resources).
- **retentionInterval**. Укажите интервал, в течение которого служба будет хранить ресурсы сценария развертывания после того, как выполнение сценария будет завершено. По истечении этого времени ресурсы сценария развертывания будут удалены. Длительность основывается на [методике ISO 8601](https://en.wikipedia.org/wiki/ISO_8601). Значение по умолчанию — **P1D**, что означает семь дней. Это свойство используется, если для параметра cleanupPreference установлено значение *OnExpiration*. Свойство *OnExpiration* сейчас не включено. Дополнительные сведения в разделе об [очистке ресурсов скриптов развертывания](#clean-up-deployment-script-resources).

### <a name="additional-samples"></a>Дополнительные примеры

- [Создание и назначение сертификата для хранилища ключей](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-keyvault.json).

- [Создание и назначение управляемого удостоверения, назначаемого пользователем, группе ресурсов и выполнение сценария развертывания](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-keyvault-mi.json).

> [!NOTE]
> Рекомендуется создать назначаемое пользователем удостоверение и предоставить разрешения заранее. Если создать удостоверение и предоставить разрешения в том же шаблоне, в котором выполняются сценарии развертывания, могут возникнуть ошибки, связанные со входом и разрешениями. Чтобы разрешения вступили в действие, требуется некоторое время.

## <a name="use-inline-scripts"></a>Использование встроенных сценариев

В приведенном ниже шаблоне определен один ресурс с типом `Microsoft.Resources/deploymentScripts`. Выделенная часть — встроенный сценарий.

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-helloworld.json" range="1-54" highlight="34-40":::

> [!NOTE]
> Так как встроенные скрипты развертывания заключены в двойные кавычки, строки внутри этих скриптов необходимо заключить в одинарные кавычки. Escape-символ для PowerShell — **&#92;** . Можно также использовать подстановку строк, как показано в предыдущем примере JSON. Обратите внимание на значение по умолчанию параметра name.

Сценарий принимает один параметр и выводит значение параметра. Для хранения выходных данных используется **DeploymentScriptOutputs**.  В разделе выходных данных строка **value** показывает, как получить доступ к сохраненным значениям. `Write-Output` используется для отладки. Чтобы узнать, как получить доступ к выходному файлу, ознакомьтесь с разделом [Отладка сценариев развертывания](#debug-deployment-scripts).  Описания свойств см. в разделе [Примеры шаблонов](#sample-templates).

Чтобы выполнить сценарий, выберите **Попробовать**. Откроется Cloud Shell. Вставьте приведенный ниже сценарий PowerShell в окно оболочки.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the name of the resource group to be created"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$id = Read-Host -Prompt "Enter the user-assigned managed identity ID"

New-AzResourceGroup -Name $resourceGroupName -Location $location

New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-helloworld.json" -identity $id

Write-Host "Press [ENTER] to continue ..."
```

Он возвращает примерно такие выходные данные:

![Выходные данные сценария развертывания шаблона Resource Manager Hello World](./media/deployment-script-template/resource-manager-template-deployment-script-helloworld-output.png)

## <a name="use-external-scripts"></a>Использование внешних сценариев

Помимо встроенных сценариев можно также использовать внешние файлы сценария. Поддерживаются только основные сценарии PowerShell с расширением файла **PS1**. Для CLI основные сценарии могут иметь любое расширение (или не иметь его), если это допустимые сценарии Bash. Чтобы использовать внешние файлы сценария, замените `scriptContent` на `primaryScriptUri`. Пример:

```json
"primaryScriptURI": "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-helloworld.ps1",
```

Чтобы ознакомиться с примером, щелкните [здесь](https://github.com/Azure/azure-docs-json-samples/blob/master/deployment-script/deploymentscript-helloworld-primaryscripturi.json).

Внешние файлы сценария должны быть доступны.  Сведения о защите файлов сценария, которые хранятся в учетных записях хранения Azure, см. в разделе [Развертывание закрытого шаблона ARM с маркером SAS](./secure-template-with-sas-token.md).

Вы несете ответственность за целостность сценариев, на которые ссылается сценарий развертывания посредством **PrimaryScriptUri** или **SupportingScriptUris**.  Указывайте только те надежные сценарии.

## <a name="use-supporting-scripts"></a>Использование вспомогательных сценариев

Можно разделить сложную логику и поместить ее в один или несколько вспомогательных файлов сценария. Свойство `supportingScriptURI` позволяет при необходимости указать массив универсальных кодов ресурса (URI) для вспомогательных файлов сценария.

```json
"scriptContent": "
    ...
    ./Create-Cert.ps1
    ...
"

"supportingScriptUris": [
  "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/create-cert.ps1"
],
```

Вспомогательные файлы сценария можно вызывать как из встроенных сценариев, так и из основных файлов сценария. Поддержка файлов сценария не накладывает ограничений на расширение файла.

Вспомогательные файлы копируются в папку azscripts/azscriptinput во время выполнения. Используйте относительный путь, чтобы указать вспомогательные файлы в файлах встроенных сценариев и основных сценариев.

## <a name="work-with-outputs-from-powershell-script"></a>Использование выходных данных сценария PowerShell

В следующем шаблоне показано, как передавать значения между двумя ресурсами deploymentScripts.

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-basic.json" range="1-84" highlight="39-40,66":::

В первом ресурсе необходимо определить переменную **$DeploymentScriptOutputs** и использовать ее для хранения выходных значений. Чтобы получить доступ к выходному значению из другого ресурса в шаблоне, используйте следующий код.

```json
reference('<ResourceName>').output.text
```

## <a name="work-with-outputs-from-cli-script"></a>Использование выходных данных сценария CLI

В отличие от сценария развертывания PowerShell, для поддержки CLI и Bash не предоставляется общая переменная для хранения выходных данных сценария. Вместо этого используется переменная среды **AZ_SCRIPTS_OUTPUT_PATH**, в которой хранится расположение файла выходных данных сценария. Если сценарий развертывания запускается из шаблона Resource Manager, эту переменную среды автоматически задает оболочка Bash.

Выходные данные сценария развертывания должны быть сохранены в расположении AZ_SCRIPTS_OUTPUT_PATH, и это должен быть допустимый строковый объект JSON. Содержимое файла должно быть сохранено в виде пар "ключ-значение". Например, массив строк сохраняется как { "MyResult": ["foo", "bar"] }.  Сохранение только результатов массива, например [ "foo", "Bar" ], недопустимо.

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-basic-cli.json" range="1-44" highlight="32":::

В предыдущем примере используется компонент [jq](https://stedolan.github.io/jq/). Он поставляется с образами контейнеров. Ознакомьтесь с [настройкой среды разработки](#configure-development-environment).

## <a name="develop-deployment-scripts"></a>Разработка сценариев развертывания

### <a name="handle-non-terminating-errors"></a>Обработка непрерывающих ошибок

Вы можете управлять тем, как PowerShell реагирует на непрерывающие ошибки, с помощью переменной [ **$ErrorActionPreference**](/powershell/module/microsoft.powershell.core/about/about_preference_variables?view=powershell-7#erroractionpreference
) в сценарии развертывания. Служба сценариев не устанавливает или не изменяет ее значение.  Несмотря на значение, заданное для $ErrorActionPreference, при обнаружении ошибки сценарий развертывания задает для состояния подготовки ресурсов значение *Failed*.

### <a name="pass-secured-strings-to-deployment-script"></a>Передача защищенных строк в сценарий развертывания

Используя переменные среды (EnvironmentVariable) в экземплярах контейнеров, вы можете динамически изменять конфигурацию приложения или сценария, запущенного в контейнере. Сценарий развертывания обрабатывает незащищенные и защищенные переменные среды так же, как и экземпляр контейнера Azure. Узнайте больше об [определении переменных среды для экземпляров контейнеров](../../container-instances/container-instances-environment-variables.md#secure-values).

Максимальный допустимый размер переменных среды — 64 КБ.

## <a name="debug-deployment-scripts"></a>Отладка сценариев развертывания

Для выполнения сценария служба сценариев создает [учетную запись хранения](../../storage/common/storage-account-overview.md) (если не указана существующая учетная запись хранения) и [экземпляр контейнера](../../container-instances/container-instances-overview.md). Если служба сценариев автоматически создает эти ресурсы, то они оба содержат суффикс **azscripts** в имени.

![Имена ресурсов сценария развертывания шаблона Resource Manager](./media/deployment-script-template/resource-manager-template-deployment-script-resources.png)

Пользовательский сценарий, результаты выполнения и файл stdout хранятся в общих папках учетной записи хранения. Для этого используется папка **azscripts**. В ней есть еще две папки для входных и выходных файлов: **azscriptinput** и **azscriptoutput**.

Выходная папка содержит файл **executionresult.json** и выходной файл скрипта. Сообщение об ошибке выполнения сценария можно просмотреть в файле **executionresult.json**. Выходной файл создается только при успешном выполнении сценария. Входная папка содержит системный файл скрипта PowerShell и пользовательские файлы скрипта развертывания. Вы можете заменить файл пользовательского сценария развертывания измененным файлом и повторно запустить сценарий развертывания из экземпляра контейнера Azure.

Сведения о развертывании ресурсов сценария развертывания можно получить на уровне группы ресурсов и на уровне подписки с помощью REST API.

```rest
/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/microsoft.resources/deploymentScripts/<DeploymentScriptResourceName>?api-version=2019-10-01-preview
```

```rest
/subscriptions/<SubscriptionID>/providers/microsoft.resources/deploymentScripts?api-version=2019-10-01-preview
```

В следующем примере используется [ARMClient](https://github.com/projectkudu/ARMClient).

```azurepowershell
armclient login
armclient get /subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourcegroups/myrg/providers/microsoft.resources/deploymentScripts/myDeployementScript?api-version=2019-10-01-preview
```

Выходные данные должны быть следующего вида.

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-status.json" range="1-37" highlight="15,34":::

В выходных данных показано состояние развертывания и идентификаторы ресурсов сценария развертывания.

Приведенный ниже REST API возвращает журнал.

```rest
/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/microsoft.resources/deploymentScripts/<DeploymentScriptResourceName>/logs?api-version=2019-10-01-preview
```

Он работает только до удаления ресурсов сценария развертывания.

Выберите **Показать скрытые типы**, чтобы просмотреть список ресурсов deploymentScripts на портале.

![Сценарий развертывания шаблона Resource Manager, показать скрытые типы, портал](./media/deployment-script-template/resource-manager-deployment-script-portal-show-hidden-types.png)

## <a name="use-an-existing-storage-account"></a>Использование имеющейся учетной записи хранения

Учетная запись хранения и экземпляр контейнера необходимы для выполнения сценария и устранения неполадок. Вы можете указать существующую учетную запись хранения. В противном случае учетная запись хранения вместе с экземпляром контейнера автоматически создается службой сценариев. Требования к использованию существующей учетной записи хранения.

- Поддерживаются следующие типы учетной записи хранения: общего назначения версии 2, общего назначения версии 1 и учетные записи FileStorage. Только учетные записи FileStorage поддерживают SKU ценовой категории "Премиум". Дополнительные сведения см. в разделе [Типы учетных записей хранения](../../storage/common/storage-account-overview.md).
- Правила брандмауэра учетной записи хранения еще не поддерживаются. Дополнительные сведения см. в статье [Настройка брандмауэров службы хранилища Azure и виртуальных сетей](../../storage/common/storage-network-security.md).
- Управляемое удостоверение, назначаемое пользователем для сценария развертывания, должно иметь разрешения на управление учетной записью хранения, включая чтение, создание и удаление файловых ресурсов.

Чтобы указать существующую учетную запись хранения, добавьте следующий код JSON в элемент Property `Microsoft.Resources/deploymentScripts`:

```json
"storageAccountSettings": {
  "storageAccountName": "myStorageAccount",
  "storageAccountKey": "myKey"
},
```

- **storageAccountName** — укажите имя учетной записи хранения.
- **storageAccountKey"** — укажите один из ключей учетной записи хранения. Получить ключ можно с помощью функции [`listKeys()`](./template-functions-resource.md#listkeys). Пример:

    ```json
    "storageAccountSettings": {
        "storageAccountName": "[variables('storageAccountName')]",
        "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName')), '2019-06-01').keys[0].value]"
    }
    ```

Полный пример определения `Microsoft.Resources/deploymentScripts` приведен в разделе [Примеры шаблонов](#sample-templates).

Если используется существующая учетная запись хранения, служба сценариев создает общую папку с уникальным именем. Сведения о том, как служба сценариев очищает файловый ресурс, см. в разделе [Очистка ресурсов сценария развертывания](#clean-up-deployment-script-resources).

## <a name="clean-up-deployment-script-resources"></a>Очистка ресурсов сценария развертывания

Учетная запись хранения и экземпляр контейнера необходимы для выполнения сценария и устранения неполадок. Вы можете указать существующую учетную запись хранения. В противном случае учетная запись хранения вместе с экземпляром контейнера автоматически создается службой сценариев. Эти два создаваемых автоматически ресурса удаляются службой сценариев, когда выполнение сценария развертывания достигает состояния завершения. Плата взимается за ресурсы, пока они не будут удалены. Сведения о ценах приведены на страницах цен на [экземпляры контейнеров](https://azure.microsoft.com/pricing/details/container-instances/) и [службу хранилища Azure](https://azure.microsoft.com/pricing/details/storage/).

Жизненный цикл этих ресурсов определяется приведенными ниже свойствами в шаблоне.

- **cleanupPreference**. Очистка параметров при завершении выполнения сценария. Поддерживаются такие значения:

  - **Always**. Удаление созданных автоматически ресурсов после завершения выполнения сценария. Если используется существующая учетная запись хранения, то служба сценариев удаляет общую папку, созданную в этой учетной записи хранения. Так как ресурс deploymentScripts по-прежнему может существовать после очистки ресурсов, перед удалением ресурсов служба сценариев сохраняет результаты выполнения сценария, например stdout, выходные данные, возвращаемое значение и т. д.
  - **OnSuccess**. Удаление созданных автоматически ресурсов только после успешного выполнения сценария. Если используется существующая учетная запись хранения, то служба сценариев удаляет файловый ресурс только после успешного выполнения сценария. Вы по-прежнему сможете получить доступ к ресурсам для поиска отладочной информации.
  - **OnExpiration**. Удаление созданных автоматически ресурсов происходит только после истечения срока, заданного с помощью параметра **retentionInterval**. Если используется существующая учетная запись хранения, то служба сценариев удаляет общую папку, но оставляет учетную запись хранения.

- **retentionInterval**. Позволяет указать интервал времени, в течение которого ресурс сценария будет храниться, после чего он будет удален.

> [!NOTE]
> Не рекомендуется использовать учетную запись хранения и экземпляр контейнера, созданные службой сценария для других целей. Эти два ресурса могут быть удалены в зависимости от жизненного цикла сценария.

## <a name="run-script-more-than-once"></a>Многократное выполнение сценария

Выполнение сценария развертывания является идемпотентной операцией. Если ни одно из свойств ресурса deploymentScripts (включая встроенный сценарий) не изменилось, сценарий не будет выполнен при повторном развертывании шаблона. Служба сценариев развертывания сравнивает имена ресурсов в шаблоне с существующими ресурсами в той же группе ресурсов. Существуют два варианта многократного выполнения одного сценария.

- Измените имя ресурса deploymentScripts. Например, используйте функцию шаблона [utcNow](./template-functions-date.md#utcnow) в качестве имени ресурса или части его имени. При изменении имени ресурса создается новый ресурс deploymentScripts. Удобно хранить журнал выполнения сценария.

    > [!NOTE]
    > Функцию utcNow можно использовать только в значении по умолчанию для параметра.

- Укажите другое значение в свойстве шаблона `forceUpdateTag`.  Например, используйте значение utcNow.

> [!NOTE]
> Напишите идемпотентные сценарии развертывания. Это гарантирует, что если они случайно будут выполнены повторно, это не приведет к изменениям системы. Например, если сценарий развертывания используется для создания ресурса Azure, убедитесь, что этот ресурс не существует, прежде чем создавать его, чтобы выполнение сценария могло завершиться успешно и вы не создали ресурс повторно.

## <a name="configure-development-environment"></a>Настройка среды разработки

Вы можете использовать предварительно настроенный образ контейнера Docker в качестве среды разработки сценариев развертывания. В следующей процедуре показано, как настроить образ Docker в Windows. Для Linux и Mac можно найти соответствующие сведения в Интернете.

1. Установите [Docker Desktop](https://www.docker.com/products/docker-desktop) на компьютере разработки.
1. Откройте Docker Desktop.
1. Щелкните значок "Docker Desktop" на панели задач и выберите **Settings** (Параметры).
1. Выберите **Shared Drives** (Общие диски), выберите локальный диск, который должен быть доступен для контейнеров, а затем нажмите кнопку **Apply** (Применить).

    ![Диск Docker сценария развертывания шаблона Resource Manager](./media/deployment-script-template/resource-manager-deployment-script-docker-setting-drive.png)

1. Введите учетные данные Windows в командной строке.
1. Откройте окно терминала — командную строку либо Windows PowerShell (не используйте интегрированную среду сценариев PowerShell).
1. Извлеките образ контейнера сценария развертывания на локальный компьютер.

    ```command
    docker pull mcr.microsoft.com/azuredeploymentscripts-powershell:az2.7
    ```

    В примере используется версия PowerShell 2.7.0.

    Чтобы извлечь образ CLI из Реестра контейнеров Майкрософт (MCR), сделайте следующее.

    ```command
    docker pull mcr.microsoft.com/azure-cli:2.0.80
    ```

    В этом примере используется версия CLI 2.0.80. Сценарий развертывания использует образы контейнеров CLI по умолчанию, доступные [здесь](https://hub.docker.com/_/microsoft-azure-cli).

1. Запустите образ Docker в локальной среде.

    ```command
    docker run -v <host drive letter>:/<host directory name>:/data -it mcr.microsoft.com/azuredeploymentscripts-powershell:az2.7
    ```

    Замените **&lt;host driver letter>** и **&lt;host directory name>** , указав существующую папку на общем диске.  Она будет сопоставлена с папкой **/data** в контейнере. Например, вот как можно указать для сопоставления папку D:\docker.

    ```command
    docker run -v d:/docker:/data -it mcr.microsoft.com/azuredeploymentscripts-powershell:az2.7
    ```

    **-it** означает поддержание активности образа контейнера.

    Пример для CLI.

    ```command
    docker run -v d:/docker:/data -it mcr.microsoft.com/azure-cli:2.0.80
    ```

1. Выберите **Поделиться!** при появлении запроса.
1. На следующем снимке экрана показано, как запустить сценарий PowerShell, если файл helloworld.ps1 находится в папке d:\docker.

    ![Диск Docker сценария развертывания шаблона Resource Manager в командной строке](./media/deployment-script-template/resource-manager-deployment-script-docker-cmd.png)

После успешного тестирования сценария его можно использовать в качестве сценария развертывания.

## <a name="next-steps"></a>Дальнейшие действия

В этой статье вы узнали, как использовать сценарии развертывания. Теперь вы можете изучить учебник по сценариям развертывания:

> [!div class="nextstepaction"]
> [Руководство. Использование сценариев развертывания в шаблонах Azure Resource Manager](./template-tutorial-deployment-script.md)