---
title: Использование скриптов развертывания в шаблонах | Документация Майкрософт
description: Используйте скрипты развертывания в шаблонах Azure Resource Manager.
services: azure-resource-manager
author: mumian
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 01/09/2020
ms.author: jgao
ms.openlocfilehash: 6308f7832a898d97c455dc90265adea345aeb0cc
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/15/2020
ms.locfileid: "75981213"
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
- Можно указать язык скрипта и платформу. В настоящее время поддерживаются только скрипты развертывания Azure PowerShell в среде Linux.
- Разрешить указание удостоверений, используемых для выполнения скриптов. В настоящее время поддерживается только [управляемое пользователем удостоверение Azure](../../active-directory/managed-identities-azure-resources/overview.md) .
- Разрешает передавать скрипту аргументы командной строки.
- Может указывать выходные данные скрипта и передавать их обратно в развертывание.

> [!NOTE]
> Сейчас скрипт развертывания находится на этапе предварительной версии. Чтобы использовать его, необходимо [зарегистрироваться для использования предварительной версии](https://aka.ms/armtemplatepreviews).

> [!IMPORTANT]
> Два ресурса скрипта развертывания, учетная запись хранения и экземпляр контейнера, создаются в одной группе ресурсов для выполнения сценариев и устранения неполадок. Эти ресурсы обычно удаляются службой сценариев, когда выполнение скрипта развертывания получает состояние терминала. Плата взимается за ресурсы, пока ресурсы не будут удалены. Дополнительные сведения см. в разделе [Очистка ресурсов скрипта развертывания](#clean-up-deployment-script-resources).

## <a name="prerequisites"></a>Технические условия

- **Назначаемое пользователем управляемое удостоверение с ролью участника на уровне подписки**. Это удостоверение используется для выполнения скриптов развертывания. Чтобы создать его, см. раздел [назначенное пользователем управляемое удостоверение](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#user-assigned-managed-identity). ИДЕНТИФИКАТОР удостоверения необходим при развертывании шаблона. Формат удостоверения:

  ```json
  /subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<IdentityID>
  ```

  Используйте следующий сценарий PowerShell для получения идентификатора, указав имя группы ресурсов и имя удостоверения.

  ```azurepowershell-interactive
  $idGroup = Read-Host -Prompt "Enter the resource group name for the managed identity"
  $idName = Read-Host -Prompt "Enter the name of the managed identity"

  $id = (Get-AzUserAssignedIdentity -resourcegroupname $idGroup -Name idName).Id
  ```

- **Azure PowerShell версии 2.7.0, 2.8.0 или 3.0.0**. Эти версии не требуются для развертывания шаблонов. Но эти версии необходимы для локального тестирования сценариев развертывания. См. статью [Установка модуля Azure PowerShell](/powershell/azure/install-az-ps). Вы можете использовать предварительно настроенный образ DOCKER.  См. раздел [Настройка среды разработки](#configure-development-environment).

## <a name="resource-schema"></a>Схема ресурса

Ниже приведен пример JSON.  Последнюю схему шаблона можно найти [здесь](/azure/templates/microsoft.resources/deploymentscripts).

```json
{
  "type": "Microsoft.Resources/deploymentScripts",
  "apiVersion": "2019-10-01-preview",
  "name": "myDeploymentScript",
  "location": "[resourceGroup().location]",
  "identity": {
    "type": "userAssigned",
    "userAssignedIdentities": {
      "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myID": {}
    }
  },
  "kind": "AzurePowerShell",
  "properties": {
    "forceUpdateTag": 1,
    "azPowerShellVersion": "2.8",
    "arguments": "[concat('-name ', parameters('name'))]",
    "scriptContent": "
      param([string] $name)
      $output = 'Hello {0}' -f $name
      Write-Output $output
      $DeploymentScriptOutputs = @{}
      $DeploymentScriptOutputs['text'] = $output
    ",
    "primaryScriptUri": "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-helloworld.json",
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
- **Тип: укажите**тип скрипта. В настоящее время поддерживается только Azure PowerShell скриптов. Значение равно **азуреповершелл**.
- **forceUpdateTag**: изменение этого значения между развертываниями шаблона приводит к повторному выполнению скрипта развертывания. Используйте функцию newGuid () или utcNow (), которая должна быть задана как defaultValue параметра. Дополнительные сведения см. в разделе [Запуск скрипта более одного раза](#run-script-more-than-once).
- **азповершеллверсион**: Укажите используемую версию модуля Azure PowerShell. В настоящее время скрипт развертывания поддерживает версии 2.7.0, 2.8.0 и 3.0.0.
- **аргументы**: укажите значения параметров. Значения разделяются пробелами.
- **скриптконтент**: укажите содержимое скрипта. Чтобы запустить внешний скрипт, используйте вместо него `primaryScriptUri`. Примеры см. в разделе [Использование встроенного скрипта](#use-inline-scripts) и [Использование внешнего скрипта](#use-external-scripts).
- **примарискриптури**: укажите общедоступный URL-адрес основного скрипта PowerShell с поддерживаемым расширением файла PowerShell.
- **суппортингскриптурис**: укажите массив общедоступных URL-адресов для поддержки файлов PowerShell, которые будут вызываться либо в `ScriptContent`, либо в `PrimaryScriptUri`.
- **время ожидания**: укажите максимально допустимое время выполнения сценария, указанное в [формате ISO 8601](https://en.wikipedia.org/wiki/ISO_8601). Значение по умолчанию — **P1D**.
- **клеануппреференце**. Укажите предпочтения по очистке ресурсов развертывания при выполнении скрипта в состоянии терминала. Значение по умолчанию — **всегда**, что означает удаление ресурсов, несмотря на состояние терминала (успешно, сбой, отменено). Дополнительные сведения см. в разделе [Очистка ресурсов скриптов развертывания](#clean-up-deployment-script-resources).
- **ретентионинтервал**: укажите интервал, в течение которого служба будет хранить ресурсы скрипта развертывания после того, как выполнение скрипта развертывания достигнет состояния терминала. Ресурсы скрипта развертывания будут удалены по истечении этого срока. Длительность основывается на [шаблоне ISO 8601](https://en.wikipedia.org/wiki/ISO_8601). Значение по умолчанию — **P1D**, то есть семь дней. Это свойство используется, если для Клеануппреференце задано значение *onexpir*. Свойство *onexpir* в настоящее время не включено. Дополнительные сведения см. в разделе [Очистка ресурсов скриптов развертывания](#clean-up-deployment-script-resources).

## <a name="use-inline-scripts"></a>Использование встроенных скриптов

Следующий шаблон содержит один ресурс, определенный типом `Microsoft.Resources/deploymentScripts`.

[!code-json[](~/resourcemanager-templates/deployment-script/deploymentscript-helloworld.json?range=1-54)]

> [!NOTE]
> Так как встроенные скрипты развертывания заключены в двойные кавычки, строки внутри скриптов развертывания необходимо заключать в одинарные кавычки. Для PowerShell используется escape- **&#92;** символ. Можно также использовать подстановку строк, как показано в предыдущем примере JSON. См. значение по умолчанию для параметра Name.

Скрипт принимает один параметр и выводит значение параметра. **Деплойментскриптаутпутс** используется для хранения выходных данных.  В разделе выходные данные в строке **значение** показано, как получить доступ к сохраненным значениям. `Write-Output` используется для целей отладки. Сведения о том, как получить доступ к выходному файлу, см. в разделе [Отладка скриптов развертывания](#debug-deployment-scripts).  Описания свойств см. в разделе [схема ресурсов](#resource-schema).

Чтобы запустить скрипт, выберите **попробовать** открыть Cloud Shell, а затем вставьте следующий код в область оболочки.

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

В дополнение к встроенным сценариям можно также использовать внешние файлы скриптов. В настоящее время поддерживаются только скрипты PowerShell с расширением файла **PS1** . Чтобы использовать внешние файлы скриптов, замените `scriptContent` на `primaryScriptUri`. Пример.

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

Вспомогательные файлы скриптов можно вызывать как из встроенных скриптов, так и из первичных файлов скриптов.

Вспомогательные файлы копируются в азскриптс/азскриптинпут во время выполнения. Используйте относительный путь для ссылки на вспомогательные файлы из встроенных скриптов и первичных файлов скриптов.

## <a name="work-with-outputs-from-deployment-scripts"></a>Работа с выходами из скриптов развертывания

В следующем шаблоне показано, как передавать значения между двумя ресурсами deploymentScripts:

[!code-json[](~/resourcemanager-templates/deployment-script/deploymentscript-basic.json?range=1-84)]

В первом ресурсе необходимо определить переменную с именем **$DeploymentScriptOutputs**и использовать ее для хранения выходных значений. Чтобы получить доступ к выходному значению из другого ресурса в шаблоне, используйте:

```json
reference('<ResourceName>').output.text
```

## <a name="debug-deployment-scripts"></a>Отладка скриптов развертывания

Служба скриптов создает [учетную запись хранения](../../storage/common/storage-account-overview.md) и [экземпляр контейнера](../../container-instances/container-instances-overview.md) для выполнения скрипта. Оба ресурса имеют суффикс **азскриптс** в именах ресурсов.

![Имена ресурсов скрипта развертывания шаблона диспетчер ресурсов](./media/deployment-script-template/resource-manager-template-deployment-script-resources.png)

Пользовательский сценарий, результаты выполнения и файл stdout хранятся в общих папках учетной записи хранения. Существует папка с именем **азскриптс**. В папке есть еще две папки для входных и выходных файлов: **азскриптинпут** и **азскриптаутпут**.

Выходная папка содержит **ExecutionResult. JSON** и выходной файл скрипта. Сообщение об ошибке выполнения сценария можно увидеть в **ExecutionResult. JSON**. Выходной файл создается только при успешном выполнении скрипта. Входная папка содержит системный файл сценария PowerShell и файлы скриптов развертывания пользователя. Вы можете заменить файл скрипта развертывания пользователя измененным и повторно запустить скрипт развертывания из экземпляра контейнера Azure.

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

В настоящее время скрипт развертывания поддерживает Azure PowerShell версии 2.7.0, 2.8.0 и 3.0.0.  Если у вас есть компьютер Windows, можно установить одну из поддерживаемых Azure PowerShell версий и приступить к разработке и тестированию сценариев развертывания.  Если у вас нет компьютера Windows или не установлена ни одна из этих Azure PowerShell версий, можно использовать предварительно настроенный образ контейнера DOCKER. В следующей процедуре показано, как настроить образ DOCKER в Windows. Для Linux и Mac можно найти сведения в Интернете.

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

    В примере используется версия 2.7.0.

1. Локальное выполнение образа DOCKER.

    ```command
    docker run -v <host drive letter>:/<host directory name>:/data -it mcr.microsoft.com/azuredeploymentscripts-powershell:az2.7
    ```

    Замените **&lt;букву драйвера узла >** и **&lt;имени каталога узлов >** существующей папкой на общем диске.  Она сопоставляет папку с папкой **/Дата** в контейнере. Например, для сопоставлений Д:\доккер:

    ```command
    docker run -v d:/docker:/data -it mcr.microsoft.com/azuredeploymentscripts-powershell:az2.7
    ```

    **— это** означает поддержание активности образа контейнера.

1. При появлении запроса выберите **поделиться им** .
1. Запустите сценарий PowerShell, как показано на следующем снимке экрана (при наличии файла HelloWorld. ps1 в папке д:\доккер).

    ![диспетчер ресурсов скрипт развертывания шаблона DOCKER cmd](./media/deployment-script-template/resource-manager-deployment-script-docker-cmd.png)

После успешного тестирования скрипта PowerShell его можно использовать в качестве скрипта развертывания.

## <a name="next-steps"></a>Дальнейшие действия

В этой статье вы узнали, как использовать скрипты развертывания. Для просмотра учебника по скриптам развертывания выполните следующие действия.

> [!div class="nextstepaction"]
> [Учебник. Использование скриптов развертывания в шаблонах Azure Resource Manager](./template-tutorial-deployment-script.md)