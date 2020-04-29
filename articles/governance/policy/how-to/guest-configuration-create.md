---
title: Создание политик конфигурации гостя для Windows
description: Узнайте, как создать политику гостевой конфигурации политики Azure для Windows.
ms.date: 03/20/2020
ms.topic: how-to
ms.openlocfilehash: a75525b25945dd9548d7c293d5965cc67eb463dc
ms.sourcegitcommit: eaec2e7482fc05f0cac8597665bfceb94f7e390f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/29/2020
ms.locfileid: "82509624"
---
# <a name="how-to-create-guest-configuration-policies-for-windows"></a>Создание политик конфигурации гостя для Windows

Перед созданием настраиваемых политик рекомендуется прочитать концептуальную информацию на странице [Настройка гостевой системы политики Azure](../concepts/guest-configuration.md).
 
Дополнительные сведения о создании политик гостевой конфигурации для Linux см. на странице [Создание политик конфигурации гостей для Linux](./guest-configuration-create-linux.md) .

При аудите Windows Гостевая конфигурация использует модуль ресурсов [Desired State Configuration](/powershell/scripting/dsc/overview/overview) (DSC) для создания файла конфигурации. Конфигурация DSC определяет условие, в котором должен находиться компьютер.
Если при вычислении конфигурации произошел сбой, то активируется действие политики **помощью параметров auditifnotexists** , и компьютер **считается несовместимым.**

[Гостевую конфигурацию политики Azure](../concepts/guest-configuration.md) можно использовать только для аудита параметров внутри компьютеров. Исправление параметров в виртуальных машинах пока недоступно.

Используйте следующие действия, чтобы создать собственную конфигурацию для проверки состояния виртуальной машины Azure или платформы, отличной от Azure.

> [!IMPORTANT]
> Настраиваемые политики с гостевой конфигурацией — это предварительная версия функции.
>
> Для проведения аудита на виртуальных машинах Azure требуется расширение конфигурации гостевой виртуальной машины.
> Чтобы развернуть расширение в масштабе на всех компьютерах Windows, назначьте следующие определения политик:
>   - [Развертывание необходимых компонентов для политики гостевой конфигурации на виртуальных машинах Windows](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ecd903d-91e7-4726-83d3-a229d7f2e293)

## <a name="install-the-powershell-module"></a>Установка модуля PowerShell

Создание артефакта конфигурации гостя, автоматическое тестирование артефакта, создание определения политики и публикация политики, полностью автоматизируются с помощью модуля гостевой настройки в PowerShell. Модуль можно установить на компьютере под управлением Windows, macOS или Linux с помощью PowerShell 6,2 или более поздней версии, локально или [Azure Cloud Shell](https://shell.azure.com)или с [образом Azure PowerShell Core DOCKER](https://hub.docker.com/r/azuresdk/azure-powershell-core).

> [!NOTE]
> Компиляция конфигураций еще не поддерживается в Linux.

### <a name="base-requirements"></a>Основные требования

Операционные системы, в которых можно установить модуль:

- Linux
- macOS
- Windows

Для модуля ресурсов гостевой настройки требуется следующее программное обеспечение:

- PowerShell 6,2 или более поздней версии. Если она еще не установлена, выполните [эти инструкции](/powershell/scripting/install/installing-powershell).
- Azure PowerShell 1.5.0 или более поздней версии. Если она еще не установлена, выполните [эти инструкции](/powershell/azure/install-az-ps).
  - Требуются только модули AZ "az. Accounts" и "az. Resources".

### <a name="install-the-module"></a>Установка модуля

Чтобы установить модуль **гуестконфигуратион** в PowerShell, выполните следующие действия.

1. В командной строке PowerShell выполните следующую команду:

   ```azurepowershell-interactive
   # Install the Guest Configuration DSC resource module from PowerShell Gallery
   Install-Module -Name GuestConfiguration
   ```

1. Убедитесь, что модуль импортирован:

   ```azurepowershell-interactive
   # Get a list of commands for the imported GuestConfiguration module
   Get-Command -Module 'GuestConfiguration'
   ```

## <a name="guest-configuration-artifacts-and-policy-for-windows"></a>Артефакты и политика гостевой настройки для Windows

Конфигурация гостя использует настройку требуемого состояния PowerShell в качестве абстракции языка для записи данных аудита в Windows. Агент загружает отдельный экземпляр PowerShell 6,2, поэтому нет конфликтов с использованием PowerShell DSC в Windows PowerShell 5,1, и нет необходимости в предварительной установке PowerShell 6,2 или более поздней версии.

Общие сведения о понятиях и терминологии DSC см. в статье [Общие сведения о POWERSHELL DSC](/powershell/scripting/dsc/overview/overview).

### <a name="how-guest-configuration-modules-differ-from-windows-powershell-dsc-modules"></a>Отличия модулей конфигурации виртуальной машины от модулей DSC Windows PowerShell

Когда гостевая конфигурация выполняет аудит компьютера:

1. Сначала агент запускает `Test-TargetResource` , чтобы определить, находится ли конфигурация в правильном состоянии.
1. Логическое значение, возвращаемое функцией, определяет, является ли состояние Azure Resource Manager для назначения гостя соответствующим или несоответствующим.
1. Поставщик выполняется `Get-TargetResource` для возврата текущего состояния каждого параметра, чтобы получить сведения о том, почему компьютер не соответствует требованиям, и убедиться, что текущее состояние соответствует требованиям.

### <a name="get-targetresource-requirements"></a>Требования Get-TargetResource

Функция `Get-TargetResource` имеет особые требования к гостевой конфигурации, которые не требуются для настройки требуемого состояния Windows.

- Возвращаемая хэш-таблица должна включать свойство с именем **reasons**.
- Свойство reasons должно быть массивом.
- Каждый элемент в массиве должен быть хэш-таблицей с ключами с именами **Code** и **Phrase**.

Свойство reasons используется службой для стандартизации представления информации о том, что компьютер не соответствует требованиям. Каждый элемент можно представить по причинам «причина», если ресурс не соответствует требованиям. Свойство является массивом, так как ресурс может не соответствовать требованиям по более чем одной причине.

Служба ожидает **код** и **фразу** свойств. При создании настраиваемого ресурса задайте текст (обычно stdout), который будет отображаться как причина, по которой ресурс не соответствует значению **фразы**. **Код** имеет определенные требования к форматированию, поэтому отчеты могут четко отображать сведения о ресурсе, используемом для аудита. Это решение делает конфигурацию гостя расширяемой. Любая команда может выполняться, пока выходные данные могут быть возвращены в виде строкового значения для свойства **фразы** .

- **Code** (строка): имя ресурса, повторяющееся, а затем короткое имя без пробелов в качестве идентификатора причины. Эти три значения должны быть заключены в двоеточие без пробелов.
  - В качестве примера можно привести`registry:registry:keynotpresent`
- **Phrase** (строка): понятный для человека текст, поясняющий причину несоответствия параметра.
  - В качестве примера можно привести`The registry key $key is not present on the machine.`

```powershell
$reasons = @()
$reasons += @{
  Code = 'Name:Name:ReasonIdentifer'
  Phrase = 'Explain why the setting is not compliant'
}
return @{
    reasons = $reasons
}
```

Свойство reasons также должно быть добавлено в MOF-файл схемы для ресурса в качестве внедренного класса.

```mof
[ClassVersion("1.0.0.0")] 
class Reason
{
    [Read] String Phrase;
    [Read] String Code;
};

[ClassVersion("1.0.0.0"), FriendlyName("ResourceName")]
class ResourceName : OMI_BaseResource
{
    [Key, Description("Example description")] String Example;
    [Read, EmbeddedInstance("Reason")] String Reasons[];
};
```

### <a name="configuration-requirements"></a>Требования настройки

Имя пользовательской конфигурации должно быть одинаковым по всему миру. Имя ZIP-файла для пакета содержимого, имя конфигурации в MOF-файле и имя назначения гостя в шаблоне диспетчер ресурсов должны совпадать.

### <a name="scaffolding-a-guest-configuration-project"></a>Формирование шаблонов проекта гостевой конфигурации

Разработчики, которые хотели бы ускорить процесс начала и работы с примером кода, могут установить проект сообщества с именем " **проект гостевой конфигурации**". Проект устанавливает шаблон для модуля PowerShell [Пластер](https://github.com/powershell/plaster) . Это средство можно использовать для формирования шаблонов проекта, включая рабочую конфигурацию и образец ресурса, а также набор [Pester](https://github.com/pester/pester) тестов для проверки проекта. Этот шаблон также включает средства выполнения задач для Visual Studio Code автоматизации создания и проверки гостевого пакета конфигурации. Дополнительные сведения см. в разделе [проект гостевой конфигурации](https://github.com/microsoft/guestconfigurationproject)проекта GitHub.

Дополнительные сведения о работе с конфигурациями в целом см. в разделе [запись, компиляция и применение конфигурации](/powershell/scripting/dsc/configurations/write-compile-apply-configuration).

### <a name="expected-contents-of-a-guest-configuration-artifact"></a>Ожидаемое содержимое артефакта конфигурации виртуальной машины

Завершенный пакет используется гостевой конфигурацией для создания определений политик Azure. Пакет состоит из следующих компонентов:

- Скомпилированная конфигурация DSC в MOF
- Папка для модулей
  - Модуль Гуестконфигуратион
  - Модуль Дскнативересаурцес
  - Windows Модули ресурсов DSC, необходимые для MOF

Командлеты PowerShell помогают при создании пакета.
Папка с корневым уровнем или папка версии не требуется.
Формат пакета должен быть ZIP-файлом.

### <a name="storing-guest-configuration-artifacts"></a>Хранение артефактов гостевой конфигурации

ZIP-пакет должен храниться в расположении, доступном для управляемых виртуальных машин.
Примеры включают репозитории GitHub, репозиторий Azure или службу хранилища Azure. Если вы предпочитаете не делать этот пакет общедоступным, можно включить [маркер SAS](../../../storage/common/storage-dotnet-shared-access-signature-part-1.md) в URL-адрес.
Вы также можете реализовать [конечную точку службы](../../../storage/common/storage-network-security.md#grant-access-from-a-virtual-network) для компьютеров в частной сети, хотя эта конфигурация применяется только к пакету и не взаимодействует со службой.

## <a name="step-by-step-creating-a-custom-guest-configuration-audit-policy-for-windows"></a>Пошаговые инструкции по созданию пользовательской политики аудита настройки гостевой конфигурации для Windows

Создайте конфигурацию DSC для параметров аудита. Следующий пример скрипта PowerShell создает конфигурацию с именем **аудитбитлоккер**, импортирует модуль ресурсов **псдскресаурцес** и использует `Service` ресурс для аудита работающей службы. Скрипт конфигурации можно выполнить на компьютере с Windows или macOS.

```powershell
# Define the DSC configuration and import GuestConfiguration
Configuration AuditBitLocker
{
    Import-DscResource -ModuleName 'PSDscResources'

    Node AuditBitlocker {
      Service 'Ensure BitLocker service is present and running'
      {
          Name = 'BDESVC'
          Ensure = 'Present'
          State = 'Running'
      }
    }
}

# Compile the configuration to create the MOF files
AuditBitLocker ./Config
```

Сохраните этот файл с именем `config.ps1` в папке проекта. Запустите его в PowerShell, выполнив `./config.ps1` в терминале. Будет создан новый MOF-файл.

`Node AuditBitlocker` Команда не является технически необходимой, но создает файл с именем `AuditBitlocker.mof` , а не по умолчанию, `localhost.mof`. Наличие имени MOF-файла соответствует конфигурации, что позволяет легко организовать множество файлов при масштабе работы.

После компиляции MOF вспомогательные файлы должны быть упакованы вместе. Завершенный пакет используется гостевой конфигурацией для создания определений политик Azure.

`New-GuestConfigurationPackage` Командлет создает пакет. Модули, необходимые для конфигурации, должны быть доступны в `$Env:PSModulePath`. Параметры `New-GuestConfigurationPackage` командлета при создании содержимого Windows:

- **Имя**: имя гостевого пакета конфигурации.
- **Конфигурация**: закомпилированный полный путь к документу конфигурации DSC.
- **Путь**: путь к выходной папке. Это необязательный параметр. Если этот параметр не указан, пакет создается в текущем каталоге.

Выполните следующую команду, чтобы создать пакет с использованием конфигурации, заданной на предыдущем шаге.

```azurepowershell-interactive
New-GuestConfigurationPackage `
  -Name 'AuditBitlocker' `
  -Configuration './Config/AuditBitlocker.mof'
```

После создания пакета конфигурации, но перед его публикацией в Azure можно протестировать пакет с рабочей станции или среды CI/CD. Командлет `Test-GuestConfigurationPackage` гуестконфигуратион включает в среду разработки тот же агент, который используется на компьютерах Azure. С помощью этого решения можно выполнить локальное тестирование интеграции перед выпуском в выставляемые облачные среды.

Так как агент фактически оценивает локальную среду, в большинстве случаев необходимо выполнить командлет Test-на той же платформе операционной системы, что и вы планируете проводить аудит. В тесте будут использоваться только модули, входящие в пакет содержимого.

Параметры `Test-GuestConfigurationPackage` командлета:

- **Имя**: имя политики конфигурации гостя.
- **Параметр**: параметры политики, указанные в формате Hashtable.
- **Путь**: полный путь к пакету конфигурации гостя.

Выполните следующую команду, чтобы протестировать пакет, созданный на предыдущем шаге:

```azurepowershell-interactive
Test-GuestConfigurationPackage `
  -Path ./AuditBitlocker.zip
```

Командлет также поддерживает входные данные из конвейера PowerShell. Передать выходные данные `New-GuestConfigurationPackage` командлета в `Test-GuestConfigurationPackage` командлет.

```azurepowershell-interactive
New-GuestConfigurationPackage -Name AuditBitlocker -Configuration ./Config/AuditBitlocker.mof | Test-GuestConfigurationPackage
```

Следующим шагом является публикация файла в хранилище BLOB-объектов. Приведенный ниже сценарий содержит функцию, которую можно использовать для автоматизации этой задачи. Для команд, используемых в `publish` функции, требуется `Az.Storage` модуль.

```azurepowershell-interactive
function publish {
    param(
    [Parameter(Mandatory=$true)]
    $resourceGroup,
    [Parameter(Mandatory=$true)]
    $storageAccountName,
    [Parameter(Mandatory=$true)]
    $storageContainerName,
    [Parameter(Mandatory=$true)]
    $filePath,
    [Parameter(Mandatory=$true)]
    $blobName
    )

    # Get Storage Context
    $Context = Get-AzStorageAccount -ResourceGroupName $resourceGroup `
        -Name $storageAccountName | `
        ForEach-Object { $_.Context }

    # Upload file
    $Blob = Set-AzStorageBlobContent -Context $Context `
        -Container $storageContainerName `
        -File $filePath `
        -Blob $blobName `
        -Force

    # Get url with SAS token
    $StartTime = (Get-Date)
    $ExpiryTime = $StartTime.AddYears('3')  # THREE YEAR EXPIRATION
    $SAS = New-AzStorageBlobSASToken -Context $Context `
        -Container $storageContainerName `
        -Blob $blobName `
        -StartTime $StartTime `
        -ExpiryTime $ExpiryTime `
        -Permission rl `
        -FullUri

    # Output
    return $SAS
}

# replace the $storageAccountName value below, it must be globally unique
$resourceGroup        = 'policyfiles'
$storageAccountName   = 'youraccountname'
$storageContainerName = 'artifacts'

$uri = publish `
  -resourceGroup $resourceGroup `
  -storageAccountName $storageAccountName `
  -storageContainerName $storageContainerName `
  -filePath ./AuditBitlocker.zip `
  -blobName 'AuditBitlocker'
```

После создания и отправки пакета настраиваемой политики гостевой конфигурации Создайте определение политики гостевой конфигурации. `New-GuestConfigurationPolicy` Командлет принимает пользовательский пакет политики и создает определение политики.

Параметры `New-GuestConfigurationPolicy` командлета:

- **Контентури**: общедоступный HTTP (s) URI пакета содержимого гостевой конфигурации.
- **DisplayName**: отображаемое имя политики.
- **Описание**: описание политики.
- **Параметр**: параметры политики, указанные в формате Hashtable.
- **Версия**: версия политики.
- **Путь**: путь назначения, по которому создаются определения политик.
- **Платформа**: Целевая платформа (Windows/Linux) для политики конфигурации гостя и пакета содержимого.

В следующем примере создаются определения политик в указанном пути из пакета настраиваемой политики.

```azurepowershell-interactive
New-GuestConfigurationPolicy `
    -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditBitLocker.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
    -DisplayName 'Audit BitLocker Service.' `
    -Description 'Audit if BitLocker is not enabled on Windows machine.' `
    -Path './policies' `
    -Platform 'Windows' `
    -Version 1.0.0 `
    -Verbose
```

Следующие файлы создаются `New-GuestConfigurationPolicy`:

- **Помощью параметров auditifnotexists. JSON**
- **deployIfNotExists. JSON**
- **Initiative. JSON**

Выходные данные командлета возвращают объект, содержащий отображаемое имя инициативы и путь к файлам политики.

Наконец, опубликуйте определения политик с помощью `Publish-GuestConfigurationPolicy` командлета. Командлет содержит только параметр **path** , указывающий на расположение JSON файлов, созданных `New-GuestConfigurationPolicy`.

Чтобы выполнить команду публикации, необходимо получить доступ к созданию политик в Azure. Конкретные требования к авторизации описаны на странице [Обзор политики Azure](../overview.md) . Наиболее подходящей встроенной ролью является **участник политики ресурсов**.

```azurepowershell-interactive
Publish-GuestConfigurationPolicy -Path '.\policyDefinitions'
```

`Publish-GuestConfigurationPolicy` Командлет принимает путь из конвейера PowerShell. Эта функция означает, что вы можете создать файлы политики и опубликовать их в одном наборе команд с последовательностью.

```azurepowershell-interactive
New-GuestConfigurationPolicy `
 -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditBitLocker.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
  -DisplayName 'Audit BitLocker service.' `
  -Description 'Audit if the BitLocker service is not enabled on Windows machine.' `
  -Path './policies' `
 | Publish-GuestConfigurationPolicy
```

С помощью политики, созданной в Azure, последним шагом является назначение инициативы. Узнайте, как назначить инициативу с помощью [портала](../assign-policy-portal.md), [Azure CLI](../assign-policy-azurecli.md)и [Azure PowerShell](../assign-policy-powershell.md).

> [!IMPORTANT]
> Политики конфигурации гостя **всегда** должны быть назначены с помощью инициативы, объединяющей политики _помощью параметров auditifnotexists_ и _DeployIfNotExists_ . Если назначена только политика _помощью параметров auditifnotexists_ , предварительные требования не развертываются, а политика всегда показывает, что серверы "0" соответствуют требованиям.

Для назначения определения политики с _DeployIfNotExistsным_ результатом требуется дополнительный уровень доступа. Чтобы предоставить минимальные привилегии, можно создать пользовательское определение роли, расширяющее **участника политики ресурсов**. В приведенном ниже примере создается роль с именем **участник политики ресурсов, проверять** с дополнительным разрешениями _Microsoft. Authorization/roleAssignments/Write_.

```azurepowershell-interactive
$subscriptionid = '00000000-0000-0000-0000-000000000000'
$role = Get-AzRoleDefinition "Resource Policy Contributor"
$role.Id = $null
$role.Name = "Resource Policy Contributor DINE"
$role.Description = "Can assign Policies that require remediation."
$role.Actions.Clear()
$role.Actions.Add("Microsoft.Authorization/roleAssignments/write")
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add("/subscriptions/$subscriptionid")
New-AzRoleDefinition -Role $role
```

### <a name="using-parameters-in-custom-guest-configuration-policies"></a>Использование параметров в пользовательских политиках гостевой конфигурации

Конфигурация гостя поддерживает переопределение свойств конфигурации во время выполнения. Эта функция означает, что значения в MOF-файле в пакете не обязательно должны рассматриваться как статические. Переопределяемые значения предоставляются с помощью политики Azure и не влияют на то, как создаются или компилируются конфигурации.

Командлеты `New-GuestConfigurationPolicy` и `Test-GuestConfigurationPolicyPackage` включают параметр с именем **Parameters**. Этот параметр принимает определение Hashtable, включая все сведения о каждом параметре, и создает необходимые разделы каждого файла, используемого для определения политики Azure.

В следующем примере создается определение политики для аудита службы, где пользователь выбирает из списка во время назначения политики.

```azurepowershell-interactive
$PolicyParameterInfo = @(
    @{
        Name = 'ServiceName'                                            # Policy parameter name (mandatory)
        DisplayName = 'windows service name.'                           # Policy parameter display name (mandatory)
        Description = "Name of the windows service to be audited."      # Policy parameter description (optional)
        ResourceType = "Service"                                        # DSC configuration resource type (mandatory)
        ResourceId = 'windowsService'                                   # DSC configuration resource property name (mandatory)
        ResourcePropertyName = "Name"                                   # DSC configuration resource property name (mandatory)
        DefaultValue = 'winrm'                                          # Policy parameter default value (optional)
        AllowedValues = @('BDESVC','TermService','wuauserv','winrm')    # Policy parameter allowed values (optional)
    }
)

New-GuestConfigurationPolicy
    -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditBitLocker.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
    -DisplayName 'Audit Windows Service.' `
    -Description 'Audit if a Windows Service is not enabled on Windows machine.' `
    -Path '.\policyDefinitions' `
    -Parameters $PolicyParameterInfo `
    -Version 1.0.0
```

## <a name="policy-lifecycle"></a>Жизненный цикл политики

Если вы хотите отпустить обновление политики, есть два поля, требующие внимания.

- **Версия**: при запуске `New-GuestConfigurationPolicy` командлета необходимо указать номер версии, превышающий текущую публикацию. Свойство обновляет версию назначения гостевой конфигурации, чтобы агент распознал обновленный пакет.
- **контенсаш**: это свойство автоматически обновляется `New-GuestConfigurationPolicy` командлетом. Это хэш-значение пакета, созданного `New-GuestConfigurationPackage`. Свойство должно быть правильным для публикуемого `.zip` файла. Если обновляется только свойство **контентури** , расширение не принимает пакет содержимого.

Самый простой способ выпуска обновленного пакета — повторить процедуру, описанную в этой статье, и указать обновленный номер версии. Этот процесс гарантирует, что все свойства были правильно обновлены.

## <a name="converting-windows-group-policy-content-to-azure-policy-guest-configuration"></a>Преобразование содержимого групповая политика Windows в конфигурацию гостя политики Azure

Конфигурация гостя при аудите компьютеров Windows является реализацией синтаксиса настройки требуемого состояния PowerShell. Сообщество DSC опубликовало средства для преобразования экспортированных шаблонов групповая политика в формат DSC. С помощью этого средства вместе с командлетами гостевой конфигурации, описанными выше, можно преобразовать содержимое Windows групповая политика и пакет или опубликовать его для политики Azure для аудита. Дополнительные сведения об использовании этого средства см. в статье [Краткое руководство. преобразование групповая политика в DSC](/powershell/scripting/dsc/quickstarts/gpo-quickstart).
После преобразования содержимого описанные выше действия по созданию пакета и его публикации в качестве политики Azure будут такими же, как и для любого содержимого DSC.

## <a name="optional-signing-guest-configuration-packages"></a>Необязательно. Подписывание пакетов конфигурации гостей

Настраиваемые политики гостевой конфигурации используют хэш SHA256 для проверки того, что пакет политики не изменился.
При необходимости клиенты также могут использовать сертификат для подписывания пакетов и принудительно настроить гостевой модуль настройки, разрешающий только подписанное содержимое.

Чтобы включить этот сценарий, необходимо выполнить два действия. Запустите командлет, чтобы подписать пакет содержимого, и добавьте тег на компьютеры, требующие подписания кода.

Чтобы использовать функцию проверки подписи, запустите `Protect-GuestConfigurationPackage` командлет, чтобы подписать пакет перед его публикацией. Для этого командлета требуется сертификат подписи кода.

```azurepowershell-interactive
$Cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object {($_.Subject-eq "CN=mycert") }
Protect-GuestConfigurationPackage -Path .\package\AuditWindowsService\AuditWindowsService.zip -Certificate $Cert -Verbose
```

Параметры `Protect-GuestConfigurationPackage` командлета:

- **Путь**: полный путь к пакету конфигурации гостя.
- **Сертификат**: сертификат подписи кода для подписи пакета. Этот параметр поддерживается только при подписывании содержимого для Windows.

Агент Гуестконфигуратион ждет, что открытый ключ сертификата находится в разделе "Доверенные корневые центры сертификации" на компьютерах Windows и в пути `/usr/local/share/ca-certificates/extra` на компьютерах Linux. Чтобы узел проверял подписанное содержимое, установите открытый ключ сертификата на компьютере перед применением настраиваемой политики. Этот процесс можно выполнить с помощью любой методики в виртуальной машине или с помощью политики Azure. Пример шаблона [приведен здесь](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-push-certificate-windows).
Политика доступа Key Vault должна разрешить поставщику ресурсов вычислений доступ к сертификатам во время развертывания. Подробные инструкции см. [в разделе настройка Key Vault для виртуальных машин в Azure Resource Manager](../../../virtual-machines/windows/key-vault-setup.md#use-templates-to-set-up-key-vault).

Ниже приведен пример экспорта открытого ключа из сертификата подписи для импорта на компьютер.

```azurepowershell-interactive
$Cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object {($_.Subject-eq "CN=mycert3") } | Select-Object -First 1
$Cert | Export-Certificate -FilePath "$env:temp\DscPublicKey.cer" -Force
```

После публикации содержимого добавьте тег с именем `GuestConfigPolicyCertificateValidation` и значением `enabled` для всех виртуальных машин, где требуется подписывание кода. См. [Примеры тегов](../samples/built-in-policies.md#tags) для доставки тегов в масштабе с помощью политики Azure. После создания тега определение политики, созданное с помощью `New-GuestConfigurationPolicy` командлета, обеспечивает требование с помощью модуля настройки гостевой конфигурации.

## <a name="troubleshooting-guest-configuration-policy-assignments-preview"></a>Устранение неполадок назначений политик гостевой конфигурации (Предварительная версия)

Средство можно просмотреть в предварительной версии, чтобы помочь в устранении неполадок назначений гостевой конфигурации политики Azure. Средство находится на этапе предварительной версии и Опубликовано в коллекция PowerShell в качестве имени модуля [средство устранения неполадок конфигурации гостя](https://www.powershellgallery.com/packages/GuestConfigurationTroubleshooter/).

Для получения дополнительных сведений о командлетах в этом средстве используйте команду Get-Help в PowerShell, чтобы отобразить встроенные инструкции. Так как средство получает частые обновления, это лучший способ получить самую свежую информацию.

## <a name="next-steps"></a>Следующие шаги

- Узнайте, как проводить аудит виртуальных машин с помощью [гостевой конфигурации](../concepts/guest-configuration.md).
- Узнайте, как [программно создавать политики](programmatically-create.md).
- Узнайте, как [получить данные о соответствии](get-compliance-data.md).
