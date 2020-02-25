---
title: Использование скриптов развертывания в шаблонах | Документация Майкрософт
description: Используйте скрипты развертывания в шаблонах Azure Resource Manager.
services: azure-resource-manager
author: mumian
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: jgao
ms.openlocfilehash: d8212fb55b20f051c6479071010ef4f828792baa
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/22/2020
ms.locfileid: "77561159"
---
# <a name="use-deployment-scripts-in-templates-preview"></a>Использование скриптов развертывания в шаблонах (Предварительная версия)

Узнайте, как использовать скрипты развертывания в шаблонах ресурсов Azure. Используя новый тип ресурсов с именем `Microsoft.Resources/deploymentScripts`, пользователи могут выполнять сценарии развертывания в шаблонах развертывания и просматривать результаты выполнения. Эти скрипты можно использовать для выполнения пользовательских действий, таких как:

- Добавление пользователей в каталог
- Создание регистрации приложения
- выполнение операций с плоскостью данных, например копирование больших двоичных объектов или базы данных начального значения
- Поиск и проверка лицензионного ключа
- создать самозаверяющий сертификат;
- Создание объекта в Azure AD
- Поиск блоков IP-адресов из пользовательской системы

Преимущества скрипта развертывания:

- Простая в кодировании, использовании и отладка. Сценарии развертывания можно разрабатывать в избранных средах разработки. Скрипты можно внедрять в шаблоны или во внешние файлы скриптов.
- Можно указать язык скрипта и платформу. В настоящее время поддерживаются сценарии развертывания Azure PowerShell и Azure CLI в среде Linux.
- Разрешить указание удостоверений, используемых для выполнения скриптов. В настоящее время поддерживается только [управляемое пользователем удостоверение Azure](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) .
- Разрешает передавать скрипту аргументы командной строки.
- Может указывать выходные данные скрипта и передавать их обратно в развертывание.

> [!NOTE]
> Сейчас скрипт развертывания находится на этапе предварительной версии. Чтобы использовать его, необходимо [зарегистрироваться для использования предварительной версии](https://aka.ms/armtemplatepreviews).

> [!IMPORTANT]
> Два ресурса скрипта развертывания, учетная запись хранения и экземпляр контейнера, создаются в одной группе ресурсов для выполнения сценариев и устранения неполадок. Эти ресурсы обычно удаляются службой сценариев, когда выполнение скрипта развертывания получает состояние терминала. Плата взимается за ресурсы, пока они не будут удалены. Дополнительные сведения см. в разделе [Очистка ресурсов скрипта развертывания](#clean-up-deployment-script-resources).

## <a name="prerequisites"></a>Предварительные требования

- **Назначаемое пользователем управляемое удостоверение с ролью участника на уровне подписки**. Это удостоверение используется для выполнения скриптов развертывания. Сведения о создании см. в разделе [Создание назначаемого пользователем управляемого удостоверения с помощью портал Azure](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)или с [помощью Azure CLI](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)или с [помощью Azure PowerShell](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md). Идентификатор удостоверения необходим при развертывании шаблона. Требуемый формат удостоверения:

  ```json
  /subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<IdentityID>
  ```

  Используйте следующий сценарий CLI или PowerShell, чтобы получить идентификатор, указав имя группы ресурсов и имя удостоверения.

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

- **Azure PowerShell версии 3.0.0, 2.8.0 или 2.7.0** или **Azure CLI версии 2.0.80, 2.0.79, 2.0.78 или 2.0.77**. Эти версии не требуются для развертывания шаблонов. Но эти версии необходимы для локального тестирования сценариев развертывания. См. статью [Установка модуля Azure PowerShell](/powershell/azure/install-az-ps). Вы можете использовать предварительно настроенный образ DOCKER.  См. раздел [Настройка среды разработки](#configure-development-environment).

## <a name="sample-template"></a>Пример шаблона

Ниже приведен пример JSON.  Последнюю схему шаблона можно найти [здесь](/azure/templates/microsoft.resources/deploymentscripts).

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
    "azPowerShellVersion": "3.0",  // or "azCliVersion": "2.0.80"
    "arguments": "[concat('-name ', parameters('name'))]",
    "scriptContent": "
      param([string] $name)
      $output = 'Hello {0}' -f $name
      Write-Output $output
      $DeploymentScriptOutputs = @{}
      $DeploymentScriptOutputs['text'] = $output
    ",
    "primaryScriptUri": "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-helloworld.ps1",
    "supportingScriptUris":[],
    "timeout": "PT30M",
    "cleanupPreference": "OnSuccess",
    "retentionInterval": "P1D"
  }
}
```

> [!NOTE]
> В этом примере используется демонстрационная цель.  **скриптконтент** и **примарискриптурис** не могут сосуществовать в шаблоне.

Сведения о значении свойства:

- **Identity**: служба скрипта развертывания использует управляемое пользователем удостоверение для выполнения скриптов. В настоящее время поддерживается только управляемое пользователем удостоверение.
- **Тип: укажите**тип скрипта. В настоящее время скрипты Azure PowerShell и Azure CLI поддерживаются. Значения — **азуреповершелл** и **Azure CLI**.
- **forceUpdateTag**: изменение этого значения между развертываниями шаблона приводит к повторному выполнению скрипта развертывания. Используйте функцию newGuid () или utcNow (), которая должна быть задана как defaultValue параметра. Дополнительные сведения о выполнении скрипта несколько раз см. [здесь](#run-script-more-than-once).
- **азповершеллверсион**/**азкливерсион**: Укажите версию модуля для использования. Скрипт развертывания в настоящее время поддерживает Azure PowerShell версии 2.7.0, 2.8.0, 3.0.0 и Azure CLI версии 2.0.80, 2.0.79, 2.0.78, 2.0.77.
- **аргументы**: укажите значения параметров. Значения разделяются пробелами.
- **скриптконтент**: укажите содержимое скрипта. Чтобы запустить внешний скрипт, используйте вместо него `primaryScriptUri`. Примеры см. в разделе [Использование встроенного скрипта](#use-inline-scripts) и [Использование внешнего скрипта](#use-external-scripts).
- **примарискриптури**: укажите общедоступный URL-адрес основного скрипта развертывания с поддерживаемыми расширениями файлов.
- **суппортингскриптурис**: укажите массив общедоступных URL-адресов для поддержки файлов, которые вызываются либо `ScriptContent`, либо `PrimaryScriptUri`.
- **время ожидания**: укажите максимально допустимое время выполнения сценария, указанное в [формате ISO 8601](https://en.wikipedia.org/wiki/ISO_8601). Значение по умолчанию — **P1D**.
- **клеануппреференце**. Укажите предпочтения по очистке ресурсов развертывания при выполнении скрипта в состоянии терминала. Значение по умолчанию — **всегда**, что означает удаление ресурсов, несмотря на состояние терминала (успешно, сбой, отменено). Дополнительные сведения в разделе об [очистке ресурсов скриптов развертывания](#clean-up-deployment-script-resources).
- **ретентионинтервал**: укажите интервал, в течение которого служба будет хранить ресурсы скрипта развертывания после того, как выполнение скрипта развертывания достигнет состояния терминала. Ресурсы скрипта развертывания будут удалены по истечении этого срока. Длительность основывается на [шаблоне ISO 8601](https://en.wikipedia.org/wiki/ISO_8601). Значение по умолчанию — **P1D**, то есть семь дней. Это свойство используется, если для Клеануппреференце задано значение *onexpir*. Свойство *onexpir* в настоящее время не включено. Дополнительные сведения в разделе об [очистке ресурсов скриптов развертывания](#clean-up-deployment-script-resources).

## <a name="use-inline-scripts"></a>Использование встроенных скриптов

Следующий шаблон содержит один ресурс, определенный типом `Microsoft.Resources/deploymentScripts`.

[!code-json[](~/resourcemanager-templates/deployment-script/deploymentscript-helloworld.json?range=1-54)]

> [!NOTE]
> Так как встроенные скрипты развертывания заключены в двойные кавычки, строки внутри этих скриптов необходимо заключить в одинарные кавычки. Escape-символ для PowerShell — **&#92;** . Можно также использовать подстановку строк, как показано в предыдущем примере JSON. См. значение по умолчанию для параметра Name.

Скрипт принимает один параметр и выводит значение параметра. **Деплойментскриптаутпутс** используется для хранения выходных данных.  В разделе выходные данные в строке **значение** показано, как получить доступ к сохраненным значениям. `Write-Output` используется для целей отладки. Сведения о том, как получить доступ к выходному файлу, см. в разделе [Отладка скриптов развертывания](#debug-deployment-scripts).  Описания свойств см. в разделе [Пример шаблона](#sample-template).

Чтобы запустить скрипт, выберите **попробовать** открыть Azure Cloud Shell, а затем вставьте следующий код в область оболочки.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the name of the resource group to be created"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$id = Read-Host -Prompt "Enter the user-assigned managed identity ID"

New-AzResourceGroup -Name $resourceGroupName -Location $location

New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-helloworld.json" -identity $id

Write-Host "Press [ENTER] to continue ..."
```

Он возвращает примерно такие выходные данные:

![диспетчер ресурсов сценарий развертывания шаблона, выходные данные Hello World](./media/deployment-script-template/resource-manager-template-deployment-script-helloworld-output.png)

## <a name="use-external-scripts"></a>Использование внешних скриптов

В дополнение к встроенным сценариям можно также использовать внешние файлы скриптов. Поддерживаются только основные скрипты PowerShell с расширением файла **PS1** . Для скриптов CLI первичные скрипты могут иметь расширения (или без расширения), если скрипты являются допустимыми скриптами bash. Чтобы использовать внешние файлы скриптов, замените `scriptContent` на `primaryScriptUri`. Например:

```json
"primaryScriptURI": "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-helloworld.ps1",
```

Чтобы увидеть пример, выберите [здесь](https://github.com/Azure/azure-docs-json-samples/blob/master/deployment-script/deploymentscript-helloworld-primaryscripturi.json).

Внешние файлы скриптов должны быть доступны.  Сведения о защите файлов скриптов, которые хранятся в учетных записях хранения Azure, см. [в разделе Учебник. Защита артефактов в Azure Resource Manager развертывании шаблонов](./template-tutorial-secure-artifacts.md).

## <a name="use-supporting-scripts"></a>Использование вспомогательных скриптов

Можно разделить сложные логики на один или несколько вспомогательных файлов скриптов. Свойство `supportingScriptURI` позволяет предоставить при необходимости массив URI для вспомогательных файлов скрипта:

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

Вспомогательные файлы скриптов можно вызывать как из встроенных скриптов, так и из первичных файлов скриптов. Поддержка файлов скриптов не имеет ограничений на расширение файла.

Вспомогательные файлы копируются в азскриптс/азскриптинпут во время выполнения. Используйте относительный путь для ссылки на вспомогательные файлы из встроенных скриптов и первичных файлов скриптов.

## <a name="work-with-outputs-from-powershell-script"></a>Работа с выходами из скрипта PowerShell

В следующем шаблоне показано, как передавать значения между двумя ресурсами deploymentScripts:

[!code-json[](~/resourcemanager-templates/deployment-script/deploymentscript-basic.json?range=1-84)]

В первом ресурсе необходимо определить переменную с именем **$DeploymentScriptOutputs**и использовать ее для хранения выходных значений. Чтобы получить доступ к выходному значению из другого ресурса в шаблоне, используйте:

```json
reference('<ResourceName>').output.text
```

## <a name="work-with-outputs-from-cli-script"></a>Работа с выходами из скрипта CLI

В отличие от скрипта развертывания PowerShell, поддержка CLI/Bash не предоставляет общую переменную для хранения выходных данных скрипта. вместо этого существует переменная среды с именем **AZ_SCRIPTS_OUTPUT_PATH** , в которой хранится расположение, в котором находится файл выходных данных сценария. Если скрипт развертывания запускается из шаблона диспетчер ресурсов, эта переменная среды автоматически задается оболочкой bash.

Выходные данные скрипта развертывания должны быть сохранены в расположении AZ_SCRIPTS_OUTPUT_PATH, а выходные данные должны быть допустимым строковым объектом JSON. Содержимое файла должно быть сохранено как пара "ключ-значение". Например, массив строк сохраняется как {"Миресулт": ["foo", "Bar"]}.  Сохранение только результатов массива, например ["foo", "Bar"], недопустимо.

[!code-json[](~/resourcemanager-templates/deployment-script/deploymentscript-basic-cli.json?range=1-44)]

[JQ](https://stedolan.github.io/jq/) используется в предыдущем примере. Он поставляется с образами контейнеров. См. раздел [Настройка среды разработки](#configure-development-environment).

## <a name="debug-deployment-scripts"></a>Отладка скриптов развертывания

Служба скриптов создает [учетную запись хранения](../../storage/common/storage-account-overview.md) и [экземпляр контейнера](../../container-instances/container-instances-overview.md) для выполнения скрипта. Оба ресурса имеют суффикс **азскриптс** в именах ресурсов.

![Имена ресурсов скрипта развертывания шаблона диспетчер ресурсов](./media/deployment-script-template/resource-manager-template-deployment-script-resources.png)

Пользовательский сценарий, результаты выполнения и файл stdout хранятся в общих папках учетной записи хранения. Существует папка с именем **азскриптс**. В папке есть еще две папки для входных и выходных файлов: **азскриптинпут** и **азскриптаутпут**.

Выходная папка содержит файл **executionresult.json** и выходной файл скрипта. Сообщение об ошибке выполнения сценария можно увидеть в **ExecutionResult. JSON**. Выходной файл создается только при успешном выполнении скрипта. Входная папка содержит системный файл скрипта PowerShell и пользовательские файлы скрипта развертывания. Вы можете заменить файл скрипта развертывания пользователя измененным и повторно запустить скрипт развертывания из экземпляра контейнера Azure.

Сведения о развертывании ресурсов скрипта развертывания можно получить на уровне группы ресурсов и на уровне подписки с помощью REST API.

```rest
/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/microsoft.resources/deploymentScripts/<DeploymentScriptResourceName>?api-version=2019-10-01-preview
```

```rest
/subscriptions/<SubscriptionID>/providers/microsoft.resources/deploymentScripts?api-version=2019-10-01-preview
```

В следующем примере используется [ARMClient](https://github.com/projectkudu/ARMClient):

```azurepowershell
armclient login
armclient get /subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourcegroups/myrg/providers/microsoft.resources/deploymentScripts/myDeployementScript?api-version=2019-10-01-preview
```

Выходные данные должны быть следующего вида.

[!code-json[](~/resourcemanager-templates/deployment-script/deploymentscript-status.json?range=1-48)]

В выходных данных отображается состояние развертывания и идентификаторы ресурсов скрипта развертывания.

Следующий REST API возвращает журнал:

```rest
/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/microsoft.resources/deploymentScripts/<DeploymentScriptResourceName>/logs?api-version=2019-10-01-preview
```

Он работает только до удаления ресурсов скрипта развертывания.

Чтобы просмотреть ресурс deploymentScripts на портале, выберите **Показать скрытые типы**:

![Диспетчер ресурсов скрипт развертывания шаблона, отображение скрытых типов, портала](./media/deployment-script-template/resource-manager-deployment-script-portal-show-hidden-types.png)

## <a name="clean-up-deployment-script-resources"></a>Очистка ресурсов скрипта развертывания

Скрипт развертывания создает учетную запись хранения и экземпляр контейнера, которые используются для выполнения скриптов развертывания и хранения отладочных данных. Эти два ресурса создаются в той же группе ресурсов, что и подготовленные ресурсы, и будут удалены службой скриптов при истечении срока действия скрипта. Вы можете управлять жизненным циклом этих ресурсов.  До тех пор, пока они не будут удалены, вам будет выставлен счет за оба ресурса. Сведения о ценах см. в статье цены на [экземпляры контейнеров](https://azure.microsoft.com/pricing/details/container-instances/) и [цены на хранилище Azure](https://azure.microsoft.com/pricing/details/storage/).

Жизненный цикл этих ресурсов определяется следующими свойствами в шаблоне:

- **клеануппреференце**: Очистка предпочтений при выполнении скрипта в состоянии терминала.  Поддерживаются такие значения:

  - **Всегда**: удаляйте ресурсы после выполнения скрипта в состоянии терминала. Так как ресурс deploymentScripts по-прежнему может присутствовать после очистки ресурсов, системный сценарий скопирует результаты выполнения сценария, например stdout, Outputs, return value и т. д. в базу данных перед удалением ресурсов.
  - **Onsuccesss**: удаление ресурсов только после успешного выполнения скрипта. Вы по-прежнему можете получить доступ к ресурсам для поиска отладочной информации.
  - **Onexpir**: удаляйте ресурсы только при истечении срока действия параметра **ретентионинтервал** . Это свойство сейчас отключено.

- **ретентионинтервал**: укажите интервал времени, в течение которого ресурс скрипта будет храниться, после чего он будет удален.

> [!NOTE]
> Не рекомендуется использовать ресурсы скрипта развертывания для других целей.

## <a name="run-script-more-than-once"></a>Запустить скрипт несколько раз

Выполнение скрипта развертывания является операцией идемпотентными. Если ни одно из свойств ресурса deploymentScripts (включая встроенный скрипт) не изменилось, скрипт не будет выполнен при повторном развертывании шаблона. Служба скриптов развертывания сравнивает имена ресурсов в шаблоне с существующими ресурсами в той же группе ресурсов. Существует два варианта выполнения одного скрипта развертывания несколько раз:

- Измените имя ресурса deploymentScripts. Например, используйте функцию шаблона [UtcNow](./template-functions-string.md#utcnow) в качестве имени ресурса или как часть имени ресурса. При изменении имени ресурса создается новый ресурс deploymentScripts. Удобно хранить историю выполнения скрипта.

    > [!NOTE]
    > Функцию utcNow можно использовать только в значении по умолчанию для параметра.

- Укажите другое значение в свойстве шаблона `forceUpdateTag`.  Например, используйте utcNow в качестве значения.

> [!NOTE]
> Напишите идемпотентными скрипты развертывания. Это гарантирует, что если они снова будут выполняться случайно, это не приведет к изменениям системы. Например, если скрипт развертывания используется для создания ресурса Azure, убедитесь, что ресурс не существует, прежде чем создавать его, поэтому сценарий завершится с ошибкой или вы не создадите ресурс повторно.

## <a name="configure-development-environment"></a>Настройка среды разработки

Вы можете использовать предварительно настроенный образ контейнера DOCKER в качестве среды разработки скриптов развертывания. В следующей процедуре показано, как настроить образ DOCKER в Windows. Для Linux и Mac можно найти сведения в Интернете.

1. Установка [DOCKER Desktop](https://www.docker.com/products/docker-desktop) на компьютере разработчика.
1. Откройте классическое окно DOCKER.
1. Выберите значок DOCKER Desktop на панели задач и щелкните **Параметры**.
1. Выберите **Общие диски**, выберите локальный диск, который должен быть доступен для контейнеров, а затем нажмите кнопку **Применить** .

    ![Диск DOCKER сценария развертывания шаблона диспетчер ресурсов](./media/deployment-script-template/resource-manager-deployment-script-docker-setting-drive.png)

1. Введите учетные данные Windows в командной строке.
1. Откройте окно терминала — либо командную строку, либо Windows PowerShell (не используйте PowerShell ISE).
1. Вытяните образ контейнера скрипта развертывания на локальный компьютер:

    ```command
    docker pull mcr.microsoft.com/azuredeploymentscripts-powershell:az2.7
    ```

    В примере используется версия PowerShell 2.7.0.

    Чтобы извлечь образ CLI из реестра контейнеров Майкрософт (мкр), сделайте следующее:

    ```command
    docker pull mcr.microsoft.com/azure-cli:2.0.80
    ```

    В этом примере используется версия 2.0.80 CLI. Скрипт развертывания использует образы по умолчанию контейнеров CLI, расположенные [здесь](https://hub.docker.com/_/microsoft-azure-cli).

1. Локальное выполнение образа DOCKER.

    ```command
    docker run -v <host drive letter>:/<host directory name>:/data -it mcr.microsoft.com/azuredeploymentscripts-powershell:az2.7
    ```

    Замените **&lt;букву драйвера узла >** и **&lt;имени каталога узлов >** существующей папкой на общем диске.  Она сопоставляет папку с папкой **/Дата** в контейнере. Например, для сопоставлений Д:\доккер:

    ```command
    docker run -v d:/docker:/data -it mcr.microsoft.com/azuredeploymentscripts-powershell:az2.7
    ```

    **— это** означает поддержание активности образа контейнера.

    Пример интерфейса командной строки:

    ```command
    docker run -v d:/docker:/data -it mcr.microsoft.com/azure-cli:2.0.80
    ```

1. При появлении запроса выберите **поделиться им** .
1. На следующем снимке экрана показано, как запустить сценарий PowerShell, учитывая, что файл HelloWorld. ps1 находится в папке д:\доккер

    ![диспетчер ресурсов скрипт развертывания шаблона DOCKER cmd](./media/deployment-script-template/resource-manager-deployment-script-docker-cmd.png)

После успешного тестирования скрипта его можно использовать в качестве скрипта развертывания.

## <a name="next-steps"></a>Следующие шаги

В этой статье вы узнали, как использовать скрипты развертывания. Для просмотра учебника по скриптам развертывания выполните следующие действия.

> [!div class="nextstepaction"]
> [Учебник. Использование скриптов развертывания в шаблонах Azure Resource Manager](./template-tutorial-deployment-script.md)