---
title: Используйте шаблоны развертывания в шаблонах Документы Майкрософт
description: использовать сценарии развертывания в шаблонах управления ресурсами Azure.
services: azure-resource-manager
author: mumian
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: jgao
ms.openlocfilehash: f84707adfa406011989c8f9bfdf1e8d9270698a6
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/09/2020
ms.locfileid: "80984799"
---
# <a name="use-deployment-scripts-in-templates-preview"></a>Используйте сценарии развертывания в шаблонах (Предварительный просмотр)

Узнайте, как использовать шаблоны развертывания в шаблонах ресурсов Azure. С помощью нового `Microsoft.Resources/deploymentScripts`типа ресурсов под названием пользователи могут выполнять сценарии развертывания в развертываниях шаблонов и просматривать результаты выполнения. Эти скрипты могут быть использованы для выполнения пользовательских шагов, таких как:

- добавить пользователей в каталог
- создать регистрацию приложения
- выполнять операции плоскости данных, например, копировать капли или базу данных семян
- поиск и проверка ключа лицензии
- создать самозаверяющий сертификат;
- создание объекта в Azure AD
- искать блоки IP-адреса из пользовательской системы

Преимущества сценария развертывания:

- Легко кодировать, использовать и отладить. Можно разрабатывать сценарии развертывания в любимых средах разработки. Скрипты могут быть встроены в шаблоны или во внешние файлы скриптов.
- Вы можете указать язык и платформу скрипта. В настоящее время поддерживаются скрипты развертывания Azure PowerShell и Azure CLI в среде Linux.
- Разрешить указать имена, используемые для выполнения скриптов. В настоящее время поддерживается только [управляемая иличность Azure, назначенная пользователем.](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)
- Разрешить передачу аргументов командной строки скрипту.
- Можно указать выходы скриптов и передать их обратно в развертывание.

Ресурс скриптов развертывания доступен только в регионах, где доступна группа Ресурсов контейнеров Azure.  Просмотрите [доступность ресурсов для экземпляров контейнеров Azure в регионах Azure.](../../container-instances/container-instances-region-availability.md)

> [!IMPORTANT]
> Два ресурса скрипта развертывания, учетная запись хранения и экземпляр контейнера, создаются в одной группе ресурсов для выполнения сценариев и устранения неполадок. Эти ресурсы обычно удаляются службой скриптов при выполнении сценария развертывания в состоянии терминала. Плата взимается за ресурсы, пока они не будут удалены. Чтобы узнать больше, смотрите [ресурсы скриптов развертывания Clean-up.](#clean-up-deployment-script-resources)

## <a name="prerequisites"></a>Предварительные требования

- **Назначенное пользователем управляемое удостоверение с ролью участника в целевой группе ресурсов.** Это удостоверение используется для выполнения скриптов развертывания. Для выполнения операций за пределами группы ресурсов необходимо предоставить дополнительные разрешения. Например, присвоить идентификационный уровень на уровне подписки, если требуется создать новую группу ресурсов.

  > [!NOTE]
  > Движок сценария развертывания создает учетную запись хранения и экземпляр контейнера в фоновом режиме.  Для того чтобы подписка не зарегистрировала учетную запись хранения данных Azure (Microsoft.Storage) и экземпляр контейнеров Azure (Microsoft.ContainerInstance) (Microsoft.ContainerInstance) требуется для того, чтобы автор ская была установлена, если подписка не зарегистрировала учетную запись хранения данных Azure (Microsoft.Storage) и экземпляр контейнеров Azure (Microsoft.ContainerInstance).

  Чтобы создать идентификацию, [см. Создайте управляемую изначадляемую информацию, назначенную пользователем, используя портал Azure,](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)или [с помощью Azure CLI](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)или [с помощью Azure PowerShell.](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md) Идентификатор удостоверения необходим при развертывании шаблона. Требуемый формат удостоверения:

  ```json
  /subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<IdentityID>
  ```

  Используйте следующий скрипт CLI или PowerShell, чтобы получить идентификатор, предоставив имя группы ресурсов и имя личности.

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

- **Azure PowerShell** или **Azure CLI**. Список поддерживаемых версий Azure PowerShell можно узнать [здесь;](https://mcr.microsoft.com/v2/azuredeploymentscripts-powershell/tags/list) список поддерживаемых версий Azure CLI смотрите [здесь](https://mcr.microsoft.com/v2/azuredeploymentscripts-powershell/tags/list).

    >[!IMPORTANT]
    > Скрипт развертывания использует доступные изображения CLI из реестра контейнеров Майкрософт (MCR). Сертификация изображения CLI для сценария развертывания занимает около месяца. Не используйте версии CLI, которые были выпущены в течение 30 дней. Чтобы найти даты выпуска изображений, см. [примечания к выпуску Azure CLI.](https://docs.microsoft.com/cli/azure/release-notes-azure-cli?view=azure-cli-latest) Если используется неподдерживаемая версия, сообщение об ошибке перечисляет поддерживаемые версии.

    Эти версии не нужны для развертывания шаблонов. Но эти версии необходимы для локального тестирования сценариев развертывания. См. статью [Установка модуля Azure PowerShell](/powershell/azure/install-az-ps). Вы можете использовать предварительно настроенное изображение Docker.  Смотрите [Настройка среды разработки](#configure-development-environment).

## <a name="sample-templates"></a>Примеры шаблонов

Следующий Json является примером.  Последние схемы шаблона можно найти [здесь](/azure/templates/microsoft.resources/deploymentscripts).

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
> Пример предназначен для демонстрационных целей.  **scriptContent** и **primaryScriptUris** не могут сосуществовать в шаблоне.

Детали стоимости недвижимости:

- **Identity**: Служба скриптов развертывания использует назначенную пользователем управляемую идентификацию для выполнения скриптов. В настоящее время поддерживается только назначенная пользователем управляемая идентификация.
- **вид**: Укажите тип скрипта. В настоящее время скрипты Azure PowerShell и Azure CLI поддерживаются. Значения: **AzurePowerShell** и **AzureCLI.**
- **forceUpdateTag**: Изменение этого значения между развертываниями шаблонов заставляет сценарий развертывания повторно выполняться. Используйте функцию newGuid() или utcNow(), которая должна быть установлена как значение значения параметра по умолчанию. Дополнительные сведения о выполнении скрипта несколько раз см. [здесь](#run-script-more-than-once).
- **azPowerShellVersion**/**azCliVersion**: Укажите версию модуля, которая будет использоваться. Список поддерживаемых версий PowerShell и CLI можно узнать на [примере Prerequisites.](#prerequisites)
- **аргументы**: Укажите значения параметра. Значения разделяются пробелами.
- **environmentVariables**: Укажите переменные среды, чтобы перейти к скрипту. Для получения дополнительной [информации см.](#develop-deployment-scripts)
- **scriptContent**: Укажите содержимое скрипта. Для запуска внешнего скрипта используйте `primaryScriptUri` вместо этого. Например, [см.](#use-inline-scripts) [Use external script](#use-external-scripts)
- **primaryScriptUri**: Укажите общедоступный Url для основного сценария развертывания с поддерживаемыми расширениями файлов.
- **поддержкаScriptUris**: Указать массив общедоступных Urls для поддержки `ScriptContent` `PrimaryScriptUri`файлов, которые называются в любом или .
- **тайм-аут**: Укажите максимально допустимое время выполнения скрипта, указанное в [формате ISO 8601.](https://en.wikipedia.org/wiki/ISO_8601) Значение по умолчанию — **P1D**.
- **очисткаПреференция**. Укажите предпочтение очистки ресурсов развертывания при выполнении скриптов в состоянии терминала. Настройка по умолчанию **всегда,** что означает удаляние ресурсов, несмотря на состояние терминала (Успешно, не удалось, отменено). Дополнительные сведения в разделе об [очистке ресурсов скриптов развертывания](#clean-up-deployment-script-resources).
- **RetentionInterval**: Указать интервал, для которого служба сохраняет ресурсы сценария развертывания после того, как выполнение сценария развертывания достигает состояния терминала. Ресурсы сценария развертывания будут удалены по истечении этого срока. Продолжительность основана на [шаблоне ISO 8601.](https://en.wikipedia.org/wiki/ISO_8601) Значение по умолчанию **P1D,** что означает семь дней. Это свойство используется, если для параметра cleanupPreference установлено значение *OnExpiration*. Свойство *OnExation* в настоящее время не включено. Дополнительные сведения в разделе об [очистке ресурсов скриптов развертывания](#clean-up-deployment-script-resources).

### <a name="additional-samples"></a>Дополнительные примеры

- [создать и назначить сертификат в хранилище ключей](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-keyvault.json)

- [создать и назначить назначенному пользователем управляемому идентификатору группе ресурсов и запустить скрипт развертывания.](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-keyvault-mi.json)

> [!NOTE]
> Рекомендуется заранее создать удостоверение личности, назначенное пользователем, и предоставить разрешения. Ошибки, связанные с ввозами и разрешениями, могут быть получены при создании удостоверения личности и предоставлении разрешений в том же шаблоне, где запускались сценарии развертывания. Для вступления в силу требуется некоторое время.

## <a name="use-inline-scripts"></a>Использование внеочередных скриптов

Следующий шаблон имеет один `Microsoft.Resources/deploymentScripts` ресурс, определенный с типом. Выделенная часть — это вонючий скрипт.

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-helloworld.json" range="1-54" highlight="34-40":::

> [!NOTE]
> Так как встроенные скрипты развертывания заключены в двойные кавычки, строки внутри этих скриптов необходимо заключить в одинарные кавычки. Escape-символ для PowerShell — **&#92;**. Вы также можете рассмотреть возможность использования замены строк, как это показано в предыдущем образце JSON. Просмотрите значение параметра имени по умолчанию.

Скрипт принимает один параметр и выводит значение параметра. **РазвертываниеScriptOutputs** используется для хранения выходов.  В разделе выводов строка **значений** показывает, как получить доступ к сохраненным значениям. `Write-Output`используется для отладки. Чтобы узнать, как получить доступ к выходу файла, смотрите [сценарии развертывания Debug.](#debug-deployment-scripts)  Описания свойств см. [Sample templates](#sample-templates)

Чтобы запустить скрипт, **выберите попытайтесь** открыть облачную оболочку, а затем вставьте следующий код в панель оболочки.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the name of the resource group to be created"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$id = Read-Host -Prompt "Enter the user-assigned managed identity ID"

New-AzResourceGroup -Name $resourceGroupName -Location $location

New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-helloworld.json" -identity $id

Write-Host "Press [ENTER] to continue ..."
```

Он возвращает примерно такие выходные данные:

![Шаблон развертывания шаблонов ресурсного менеджера привет выход мира](./media/deployment-script-template/resource-manager-template-deployment-script-helloworld-output.png)

## <a name="use-external-scripts"></a>Использование внешних скриптов

В дополнение к внеочередным скриптам можно также использовать внешние файлы скриптов. Поддерживаются только первичные скрипты PowerShell с расширением файла **PS1.** Для скриптов CLI основные скрипты могут иметь любые расширения (или без расширения), если скрипты являются действительными скриптами Bash. Чтобы использовать внешние файлы `primaryScriptUri`скрипта, замените `scriptContent` на . Пример:

```json
"primaryScriptURI": "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-helloworld.ps1",
```

Чтобы просмотреть пример, выберите [здесь](https://github.com/Azure/azure-docs-json-samples/blob/master/deployment-script/deploymentscript-helloworld-primaryscripturi.json).

Внешние файлы скриптов должны быть доступны.  Чтобы обезопасить файлы скриптов, которые хранятся в учетных записях хранения Azure, [см.](./template-tutorial-secure-artifacts.md)

Вы несете ответственность за обеспечение целостности скриптов, на которые ссылается сценарий развертывания, либо **PrimaryScriptUri,** либо **SupportingScriptUris.**  Ссылка только сценарии, которым вы доверяете.

## <a name="use-supporting-scripts"></a>Использование вспомогательных скриптов

Сложные логики можно разделить на один или несколько вспомогательных файлов скриптов. Свойство `supportingScriptURI` позволяет предоставлять массив URIs для вспомогательных файлов скриптов, если это необходимо:

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

Поддержка файлов скриптов может быть вызвана как из влинейных скриптов, так и из основных файлов скриптов. Поддержка файлов скриптов не имеет ограничений на расширение файла.

Вспомогательные файлы копируются в azscripts/azscriptinput во времени выполнения. Используйте относительный путь для ссылки на вспомогательные файлы из внеочередных скриптов и основных файлов скриптов.

## <a name="work-with-outputs-from-powershell-script"></a>Работа с выходами из сценария PowerShell

Следующий шаблон показывает, как передавать значения между двумя ресурсами развертыванияScripts:

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-basic.json" range="1-84" highlight="39-40,66":::

В первом ресурсе вы определяете переменную, называемую **$DeploymentScriptOutputs,** и используете ее для хранения значений вывода. Чтобы получить доступ к выходному значению из другого ресурса в шаблоне, используйте:

```json
reference('<ResourceName>').output.text
```

## <a name="work-with-outputs-from-cli-script"></a>Работа с выходами из сценария CLI

В отличие от сценария развертывания PowerShell, поддержка CLI/bash не предоставляет общую переменную для хранения выходных скриптов, вместо этого существует переменная среды, называемая **AZ_SCRIPTS_OUTPUT_PATH,** которая хранит место, где находится файл выводов скрипта. Если скрипт развертывания запущен из шаблона «Менеджер ресурсов», эта переменная среды автоматически устанавливается для вас оболочкой Bash.

Выводы сценариев развертывания должны быть сохранены в AZ_SCRIPTS_OUTPUT_PATH местоположении, а выводы должны быть действительным объектом строки JSON. Содержимое файла должно сохраняться в виде пары ключей. Например, массив строк хранится в виде «MyResult»: «Foo», «бар».  Хранение только результатов массива, например, "foo", "бар", является недействительным.

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-basic-cli.json" range="1-44" highlight="32":::

[jq](https://stedolan.github.io/jq/) используется в предыдущем образце. Он поставляется с контейнерными изображениями. Смотрите [Настройка среды разработки](#configure-development-environment).

## <a name="develop-deployment-scripts"></a>Разработка сценариев развертывания

### <a name="handle-non-terminating-errors"></a>Обработка непрекращающихся ошибок

Вы можете контролировать, как PowerShell реагирует на ошибки, не прекращающиеся, используя [**$ErrorActionPreference**](/powershell/module/microsoft.powershell.core/about/about_preference_variables?view=powershell-7#erroractionpreference
) переменную в скрипте развертывания. Движок сценария развертывания не устанавливает/изменяет значение.  Несмотря на значение, установленное для $ErrorActionPreference, сценарий развертывания устанавливает состояние подготовки ресурсов к *ошибке,* когда скрипт сталкивается с ошибкой.

### <a name="pass-secured-strings-to-deployment-script"></a>Передайте защищенные строки в сценарий развертывания

Настройка переменных среды (EnvironmentVariable) в экземплярах контейнера позволяет обеспечить динамическую конфигурацию приложения или скрипта, запущенного контейнером. Скрипт развертывания обрабатывает незащищенные и защищенные переменные среды так же, как Azure Container Instance. Для получения дополнительной информации см. [Переменные среды Set в экземплярах контейнеров.](../../container-instances/container-instances-environment-variables.md#secure-values)

## <a name="debug-deployment-scripts"></a>Сценарии развертывания отогина

Служба скриптов создает [учетную запись хранения](../../storage/common/storage-account-overview.md) и [экземпляр контейнера](../../container-instances/container-instances-overview.md) для выполнения скрипта. Оба ресурса имеют суффикс **азскриптов** в названиях ресурсов.

![Имена шаблонов развертывания шаблонов ресурсов диспетчера ресурсов](./media/deployment-script-template/resource-manager-template-deployment-script-resources.png)

Пользовательский скрипт, результаты выполнения и файл stdout хранятся в файлах, хранящихся в учетной записи хранилища. Существует папка под названием **azscripts**. В папке есть еще две папки для ввода и выходных файлов: **azscriptinput** и **azscriptoutput.**

Выходная папка содержит файл **executionresult.json** и выходной файл скрипта. Вы можете увидеть сообщение об ошибке выполнения скрипта в **executionresult.json**. Выводной файл создается только тогда, когда скрипт успешно выполняется. Входная папка содержит системный файл скрипта PowerShell и пользовательские файлы скрипта развертывания. Можно заменить файл скрипта развертывания пользователя на пересмотренный и перезапустить сценарий развертывания из экземпляра контейнера Azure.

Информацию о развертывании ресурсов скриптов можно получить на уровне группы ресурсов и на уровне подписки с помощью REST API:

```rest
/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/microsoft.resources/deploymentScripts/<DeploymentScriptResourceName>?api-version=2019-10-01-preview
```

```rest
/subscriptions/<SubscriptionID>/providers/microsoft.resources/deploymentScripts?api-version=2019-10-01-preview
```

Следующий пример использует [ARMClient:](https://github.com/projectkudu/ARMClient)

```azurepowershell
armclient login
armclient get /subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourcegroups/myrg/providers/microsoft.resources/deploymentScripts/myDeployementScript?api-version=2019-10-01-preview
```

Выходные данные должны быть следующего вида.

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-status.json" range="1-37" highlight="15,34":::

Вывод отображает состояние развертывания и идентиматары ресурсов скриптов развертывания.

Следующий REST API возвращает журнал:

```rest
/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/microsoft.resources/deploymentScripts/<DeploymentScriptResourceName>/logs?api-version=2019-10-01-preview
```

Он работает только до удаления ресурсов сценария развертывания.

Чтобы просмотреть ресурс развертыванияScripts на портале, выберите **Отображайте скрытые типы:**

![Сценарий развертывания шаблона менеджера ресурсов, показ скрытых типов, портал](./media/deployment-script-template/resource-manager-deployment-script-portal-show-hidden-types.png)

## <a name="clean-up-deployment-script-resources"></a>Очистка ресурсов сценария развертывания

Скрипт развертывания создает учетную запись хранения и экземпляр контейнера, которые используются для выполнения сценариев развертывания и хранения информации об отладке. Эти два ресурса создаются в той же группе ресурсов, что и готовые ресурсы, и будут удалены службой скриптов по истечении срока действия скрипта. Вы можете контролировать жизненный цикл этих ресурсов.  До тех пор, пока они не будут удалены, вы будете выставленсчеты на оба ресурса. Для получения информации о [ценах](https://azure.microsoft.com/pricing/details/container-instances/) см. [Azure Storage pricing](https://azure.microsoft.com/pricing/details/storage/)

Цикл жизни этих ресурсов контролируется следующими свойствами в шаблоне:

- **очисткаPreference:** Очистка предпочтений, когда выполнение скрипта попадает в состояние терминала.  Поддерживаются такие значения:

  - **Всегда**: Удалите ресурсы, как только выполнение скрипта попадает в состояние терминала. Поскольку ресурс развертываемых сценариев может по-прежнему присутствовать после очистки ресурсов, скрипт системы будет копировать результаты выполнения сценария, например, stdout, выходы, значение возврата и т.д. в DB до удаления ресурсов.
  - **OnSuccess**: Удалите ресурсы только тогда, когда выполнение сценария успешно. Вы все еще можете получить доступ к ресурсам, чтобы найти информацию об отладке.
  - **OnExpiration**: Удалите ресурсы только по истечении срока действия параметра **retentionInterval.** Это свойство в настоящее время отключено.

- **retentionInterval**: Укажите временной интервал, в течение которого ресурс скрипта будет сохранен и после которого истекает срок действия и удален.

> [!NOTE]
> Не рекомендуется использовать ресурсы сценария развертывания для других целей.

## <a name="run-script-more-than-once"></a>Запуск скрипта более одного раза

Выполнение сценария развертывания является идемпотентной операцией. Если ни одно из свойств ресурсов ресурсов развертываемых (включая рядовый сценарий) не будет изменено, при передислокации шаблона сценарий не будет выполнен. Служба скриптов развертывания сравнивает имена ресурсов в шаблоне с существующими ресурсами в той же группе ресурсов. Есть два варианта, если вы хотите выполнить один и тот же сценарий развертывания несколько раз:

- Измените название ресурса развертыванийScripts. Например, используйте функцию шаблона [utcNow](./template-functions-date.md#utcnow) как имя ресурса или как часть имени ресурса. Изменение имени ресурса создает новый ресурс развертыванияScripts. Это хорошо для хранения истории выполнения сценария.

    > [!NOTE]
    > Функция utcNow может использоваться только в значении по умолчанию для параметра.

- Укажите другое `forceUpdateTag` значение в свойстве шаблона.  Например, используйте utcNow в качестве значения.

> [!NOTE]
> Напишите модемпотентные сценарии развертывания. Это гарантирует, что если они снова заработают случайно, это не вызовет системных изменений. Например, если скрипт развертывания используется для создания ресурса Azure, проверьте, что ресурс не существует перед его созданием, так что скрипт будет успешным или вы не создадите ресурс снова.

## <a name="configure-development-environment"></a>Настройка среды разработки

Предварительно настроенное изображение контейнера докера можно использовать в среде разработки сценария развертывания. Следующая процедура показывает, как настроить изображение докера на Windows. Для Linux и Mac, вы можете найти информацию в Интернете.

1. Установите [Docker Desktop](https://www.docker.com/products/docker-desktop) на компьютере разработки.
1. Открыть рабочий стол докера.
1. Выберите значок Docker Desktop из панелей задач, а затем выберите **настройки.**
1. Выберите **общие диски,** выберите локальный диск, который вы хотите быть доступны для контейнеров, а затем выберите **Apply**

    ![Шаблон развертывания шаблонов ресурсов докер-драйв](./media/deployment-script-template/resource-manager-deployment-script-docker-setting-drive.png)

1. Введите учетные данные Windows в запросе.
1. Откройте окно терминала, как Command Prompt, так и Windows PowerShell (не используйте PowerShell ISE).
1. Потяните изображение контейнера скрипта развертывания к локальному компьютеру:

    ```command
    docker pull mcr.microsoft.com/azuredeploymentscripts-powershell:az2.7
    ```

    В примере используется версия PowerShell 2.7.0.

    Чтобы вытащить изображение CLI из реестра контейнеров Майкрософт (MCR):

    ```command
    docker pull mcr.microsoft.com/azure-cli:2.0.80
    ```

    В этом примере используется версия CLI 2.0.80. Скрипт развертывания использует изображения контейнеров CLI по умолчанию, найденные [здесь.](https://hub.docker.com/_/microsoft-azure-cli)

1. Выполнить изображение докера локально.

    ```command
    docker run -v <host drive letter>:/<host directory name>:/data -it mcr.microsoft.com/azuredeploymentscripts-powershell:az2.7
    ```

    Замените ** &lt;письмо драйвера-хоста>** и ** &lt;имя>каталога с** помощью существующей папки на общем диске.  Он отображает папку к **папке /данные** в контейнере. Для примера, чтобы отображение D: Docker:

    ```command
    docker run -v d:/docker:/data -it mcr.microsoft.com/azuredeploymentscripts-powershell:az2.7
    ```

    **-это** означает сохранение изображения контейнера в живых.

    Пример CLI:

    ```command
    docker run -v d:/docker:/data -it mcr.microsoft.com/azure-cli:2.0.80
    ```

1. Выберите **Поделитесь им,** когда вы получите подсказку.
1. На следующем скриншоте показано, как запустить сценарий PowerShell, учитывая, что у вас есть файл helloworld.ps1 в папке d:'docker.

    ![Шаблон развертывания шаблонов ресурсов докер шаблона cmd](./media/deployment-script-template/resource-manager-deployment-script-docker-cmd.png)

После успешного тестирования скрипта его можно использовать в качестве сценария развертывания.

## <a name="next-steps"></a>Дальнейшие действия

В этой статье вы узнали, как использовать сценарии развертывания. Чтобы пройти через учебник по сценарию развертывания:

> [!div class="nextstepaction"]
> [Учебник: Используйте сценарии развертывания в шаблонах управления ресурсами Azure](./template-tutorial-deployment-script.md)