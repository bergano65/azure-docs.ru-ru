---
title: Как создать политики конфигурации гостевых номеров для Windows
description: Узнайте, как создать политику конфигурации гостевых возможностей политики Azure для Windows.
ms.date: 03/20/2020
ms.topic: how-to
ms.openlocfilehash: 7b06aa0a70bfa17d67da9c6af447138f8bb9e712
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81757420"
---
# <a name="how-to-create-guest-configuration-policies-for-windows"></a>Как создать политики конфигурации гостевых номеров для Windows

Перед созданием пользовательских политик рекомендуется ознакомиться с концептуальной информацией обзора на странице [Azure Policy Guest Configuration.](../concepts/guest-configuration.md)
 
Чтобы узнать о создании политик конфигурации гостей для Linux, смотрите [страницу Как создать политики конфигурации гостей для Linux](./guest-configuration-create-linux.md)

При аудите Windows Guest Configuration использует модуль ресурса [Desired State Configuration](/powershell/scripting/dsc/overview/overview) (DSC) для и настройки файла. Конфигурация DSC определяет условие, в которое должна находиться машина.
Если оценка конфигурации не удается, срабатывает **аудиторский эффект политикиIfNotExists** и машина считается **несоответствующей.**

[Конфигурация гостей Azure Policy](../concepts/guest-configuration.md) может использоваться только для проверки настроек внутри машин. Восстановление настроек внутри машин пока недоступно.

Используйте следующие действия для создания собственной конфигурации для проверки состояния машины Azure или не-Azure.

> [!IMPORTANT]
> Пользовательские политики с конфигурацией гостей — это функция предварительного просмотра.
>
> Расширение конфигурации гостей необходимо для проведения аудита в виртуальных машинах Azure.
> Чтобы развернуть расширение в масштабе, назначайте следующие определения политики:
>   - Развертывание необходимых компонентов для политики гостевой конфигурации на виртуальных машинах Windows
>   - Развертывание необходимых компонентов для политики гостевой конфигурации на виртуальных машинах Linux

## <a name="install-the-powershell-module"></a>Установка модуля PowerShell

Создание артефакта конфигурации гостей, автоматическое тестирование артефакта, создание определения политики и публикация политики полностью автоматизировано с помощью модуля конфигурации гостей в PowerShell. Модуль может быть установлен на машине под управлением Windows, macOS или Linux с PowerShell 6.2 или позже работает локально, или с [Azure Cloud Shell](https://shell.azure.com), или с [изображением Azure PowerShell Core Docker.](https://hub.docker.com/r/azuresdk/azure-powershell-core)

> [!NOTE]
> Компиляция конфигураций на Linux пока не поддерживается.

### <a name="base-requirements"></a>Основные требования

Операционные системы, где модуль может быть установлен:

- Linux
- macOS
- Windows

Модуль ресурса Гостевой Конфигурации требует следующего программного обеспечения:

- PowerShell 6.2 или позже. Если она еще не установлена, выполните [эти инструкции](/powershell/scripting/install/installing-powershell).
- Azure PowerShell 1.5.0 или выше. Если она еще не установлена, выполните [эти инструкции](/powershell/azure/install-az-ps).
  - Требуются только модули АЗ 'Az.Accounts' и 'Az.Resources'.

### <a name="install-the-module"></a>Установка модуля

Для установки модуля **GuestConfiguration** в PowerShell:

1. В командной строке PowerShell выполните следующую команду:

   ```azurepowershell-interactive
   # Install the Guest Configuration DSC resource module from PowerShell Gallery
   Install-Module -Name GuestConfiguration
   ```

1. Проверка того, что модуль был импортирован:

   ```azurepowershell-interactive
   # Get a list of commands for the imported GuestConfiguration module
   Get-Command -Module 'GuestConfiguration'
   ```

## <a name="guest-configuration-artifacts-and-policy-for-windows"></a>Артефакты и политика конфигурации гостей для Windows

Конфигурация гостей использует конфигурацию состояния PowerShell Desired в качестве языковой абстракции для написания того, что проводить аудит в Windows. Агент загружает автономный экземпляр PowerShell 6.2, так что нет конфликта с использованием PowerShell DSC в Windows PowerShell 5.1, и нет никаких требований для предварительной установки PowerShell 6.2 или позже.

Для обзора концепций и терминологии DSC [см.](/powershell/scripting/dsc/overview/overview)

### <a name="how-guest-configuration-modules-differ-from-windows-powershell-dsc-modules"></a>Чем модули конфигурации гостей отличаются от модулей Windows PowerShell DSC

Когда конфигурация гостей проверяет машину:

1. Агент сначала `Test-TargetResource` запускается, чтобы определить, находится ли конфигурация в правильном состоянии.
1. Возвращаемые функцией значение boolean определяет, должен ли статус менеджера ресурсов Azure для назначения гостей соответствовать/не совместимый.
1. Поставщик запускает, `Get-TargetResource` чтобы вернуть текущее состояние каждой настройки, так что детали доступны как о том, почему машина не соответствует, так и для подтверждения соответствия текущего состояния.

### <a name="get-targetresource-requirements"></a>Требования Get-TargetResource

Функция `Get-TargetResource` имеет особые требования к конфигурации guest, которые не были необходимы для конфигурации состояния Windows Desired.

- Хэшбит, который возвращается должен включать свойство под названием **Причины**.
- Свойство Причин должно быть массивом.
- Каждый элемент в массиве должен быть хэш-столом с ключами под названием **Code** и **Phrase.**

Свойство Причины используется службой для стандартизации того, как информация представляется, когда машина не соответствует требованиям. Каждый элемент в Причинах можно считать «причиной», что ресурс не соответствует требованиям. Свойство представляет собой массив, поскольку ресурс может быть не соответствует требованиям по более чем одной причине.

**Код** свойств и **фраза** ожидаются службой. При создании пользовательского ресурса установите текст (обычно stdout), который вы хотели бы показать в качестве причины, по которой ресурс не соответствует значению для **фразы.** **Код** имеет конкретные требования к форматированию, поэтому отчетность может четко отображать информацию о ресурсе, используемом для аудита. Это решение делает конфигурацию guest extensible. Любая команда может быть запущена до тех пор, пока вывод может быть возвращен в качестве значения строки для свойства **фразы.**

- **Код** (строка): Имя ресурса, повторяется, а затем короткое имя без пробелов в качестве идентификатора по причине. Эти три значения должны быть разграничены без пробелов.
  - Примером может быть`registry:registry:keynotpresent`
- **Фраза** (строка): Читаемый человеком текст, чтобы объяснить, почему параметр не соответствует.
  - Примером может быть`The registry key $key is not present on the machine.`

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

Свойство Причин ы также должно быть добавлено в схему MOF для ресурса как встроенного класса.

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

Название пользовательской конфигурации должно быть последовательным везде. Имя файла .zip для пакета содержимого, имя конфигурации в файле MOF и имя назначения гостя в шаблоне менеджера ресурсов должно быть одинаковым.

### <a name="scaffolding-a-guest-configuration-project"></a>Проект конфигурации гостевых фигур

Разработчики, которые хотели бы ускорить процесс запуска и работы с образцом кода, могут установить проект сообщества под названием **Guest Configuration Project.** Проект устанавливает шаблон для модуля [Plaster](https://github.com/powershell/plaster) PowerShell. Этот инструмент может быть использован для эшафота проекта, включая рабочую конфигурацию и образец [ресурса,](https://github.com/pester/pester) а также набор тестов Pester для проверки проекта. Шаблон также включает в себя панели для Visual Studio Code для автоматизации построения и проверки пакета конфигурации гостей. Для получения дополнительной информации см. [Guest Configuration Project](https://github.com/microsoft/guestconfigurationproject)

Для получения дополнительной информации о работе с конфигурациями в целом, [см. Напишите, Компиляция и Применить конфигурацию](/powershell/scripting/dsc/configurations/write-compile-apply-configuration).

### <a name="expected-contents-of-a-guest-configuration-artifact"></a>Ожидаемое содержимое артефакта конфигурации гостей

Завершенный пакет используется гостевой конфигурацией для создания определений политики Azure. Пакет состоит из:

- Компилированная конфигурация DSC как MOF
- Папка для модулей
  - Модуль настройки гостев
  - Модуль DscNativeРесурс
  - (Окна) Модули ресурсов DSC, требуемые MOF

Смдлеты PowerShell помогают в создании пакета.
Папка уровня корневого уровня или папка версии не требуется.
Формат пакета должен быть файлом .zip.

### <a name="storing-guest-configuration-artifacts"></a>Хранение артефактов конфигурации гостей

Пакет .zip должен храниться в месте, доступном управляемым виртуальным машинам.
В качестве примеров можно привести репозитории GitHub, репо Azure или хранилище Azure. Если вы предпочитаете не обнародовать пакет, вы можете включить [токен SAS](../../../storage/common/storage-dotnet-shared-access-signature-part-1.md) в URL.
Можно также реализовать [конечную точку обслуживания](../../../storage/common/storage-network-security.md#grant-access-from-a-virtual-network) для машин в частной сети, хотя эта конфигурация применяется только к доступу к пакету и не общению с службой.

## <a name="step-by-step-creating-a-custom-guest-configuration-audit-policy-for-windows"></a>Шаг за шагом, создание пользовательской политики аудита конфигурации гостевых для Windows

Создайте конфигурацию DSC для настроек аудита. Следующий пример сценария PowerShell создает конфигурацию под названием **AuditBitLocker,** импортирует модуль ресурсов **PsDscResources** и использует `Service` ресурс для аудита для работающей службы. Скрипт конфигурации может быть выполнен с windows или macOS машины.

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

Сохраните этот `config.ps1` файл с именем в папке проекта. Запустите его в PowerShell, `./config.ps1` выполнить в терминале. Будет создан новый файл mof.

Команда `Node AuditBitlocker` технически не требуется, но она `AuditBitlocker.mof` производит файл с `localhost.mof`именем, а не по умолчанию, . После этого имя файла .mof следует конфигурации, упрощает организацию многих файлов при работе в масштабе.

После компиляции MOF вспомогательные файлы должны быть упакованы вместе. Завершенный пакет используется гостевой конфигурацией для создания определений политики Azure.

Cmdlet `New-GuestConfigurationPackage` создает пакет. Модули, необходимые для конфигурации, `$Env:PSModulePath`должны быть доступны в . Параметры `New-GuestConfigurationPackage` cmdlet при создании содержимого Windows:

- **Имя**: Название пакета гостевой конфигурации.
- **Конфигурация**: Составленная конфигурация DSC документ полный путь.
- **Путь**: Путь папки вывода. Это необязательный параметр. Если не указано, пакет создается в текущем каталоге.

Выполнить следующую команду для создания пакета с использованием конфигурации, приведенной на предыдущем этапе:

```azurepowershell-interactive
New-GuestConfigurationPackage `
  -Name 'AuditBitlocker' `
  -Configuration './Config/AuditBitlocker.mof'
```

После создания пакета Configuration, но перед публикацией в Azure, вы можете протестировать пакет с рабочей станции или среды CI/CD. Cmdlet `Test-GuestConfigurationPackage` GuestConfiguration содержит тот же агент в среде разработки, что и в машинах Azure. Используя это решение, вы можете сделать интеграционное тестирование локально, прежде чем выйти в облик облачных средах.

Поскольку агент фактически оценивает локальную среду, в большинстве случаев необходимо запустить Test- cmdlet на той же платформе ОС, что и вы планируете аудит. Тест будет использовать только модули, которые включены в пакет содержимого.

Параметры `Test-GuestConfigurationPackage` cmdlet:

- **Имя**: Имя политики конфигурации гостей.
- **Параметр**: Параметры политики, представленные в формате хэштабели.
- **Путь**: Полный путь пакета конфигурации гостя.

Выполнить следующую команду для тестирования пакета, созданного предыдущим шагом:

```azurepowershell-interactive
Test-GuestConfigurationPackage `
  -Path ./AuditBitlocker.zip
```

Cmdlet также поддерживает входные данные от конвейера PowerShell. Труба выход `New-GuestConfigurationPackage` смдлета `Test-GuestConfigurationPackage` к cmdlet.

```azurepowershell-interactive
New-GuestConfigurationPackage -Name AuditBitlocker -Configuration ./Config/AuditBitlocker.mof | Test-GuestConfigurationPackage
```

Следующим шагом является публикация файла для хранения капли. Приведенный ниже скрипт содержит функцию, которую можно использовать для автоматизации этой задачи. Команды, используемые `publish` в функции, `Az.Storage` требуют модуля.

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

После создания и загрузки пользовательского пакета гостевой конфигурации создайте определение политики гостевой конфигурации. Cmdlet `New-GuestConfigurationPolicy` принимает пользовательский пакет политики и создает определение политики.

Параметры `New-GuestConfigurationPolicy` cmdlet:

- **ContentUri**: Публичное http(s) uri пакета содержимого гостевой конфигурации.
- **DisplayName**: Имя отображения политики.
- **Описание**: Описание политики.
- **Параметр**: Параметры политики, представленные в формате хэштабели.
- **Версия**: Политическая версия.
- **Путь**назначения : Путь назначения, где создаются определения политики.
- **Платформа**: Целевая платформа (Windows/Linux) для политики конфигурации гостей и пакета содержимого.

Следующий пример создает определения политики в определенном пути из пользовательского пакета политики:

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

Следующие файлы `New-GuestConfigurationPolicy`создаются:

- **auditifNotExists.json**
- **deployifNotExists.json**
- **Initiative.json**

Выход cmdlet возвращает объект, содержащий имя отображения инициативы и путь файлов политики.

Наконец, опубликуйте `Publish-GuestConfigurationPolicy` определения политики с помощью cmdlet. Cmdlet имеет только параметр **Пути,** который указывает на расположение `New-GuestConfigurationPolicy`файлов JSON, созданных .

Для выполнения команды «Публикация» требуется доступ для создания политик в Azure. Конкретные требования к авторизации задокументированы на странице [обзора политики Azure.](../overview.md) Наилучшей встроенной ролью является **вкладчик политики ресурсов.**

```azurepowershell-interactive
Publish-GuestConfigurationPolicy -Path '.\policyDefinitions'
```

Cmdlet `Publish-GuestConfigurationPolicy` принимает путь от трубопровода PowerShell. Эта функция означает, что вы можете создавать файлы политики и публиковать их в одном наборе команд с трубами.

```azurepowershell-interactive
New-GuestConfigurationPolicy `
 -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditBitLocker.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
  -DisplayName 'Audit BitLocker service.' `
  -Description 'Audit if the BitLocker service is not enabled on Windows machine.' `
  -Path './policies' `
 | Publish-GuestConfigurationPolicy
```

С помощью политики, созданной в Azure, последним шагом является присвоение инициативы. Узнайте, как назначить инициативу с [помощью Portal,](../assign-policy-portal.md) [Azure CLI](../assign-policy-azurecli.md)и [Azure PowerShell.](../assign-policy-powershell.md)

> [!IMPORTANT]
> Политики конфигурации гостей **всегда** должны быть назначены с помощью инициативы, которая сочетает в себе политики _AuditIfNotExists_ и _DeployIfNotExists._ Если назначена только политика _AuditIfNotExists,_ предпосылки не развернуты, а политика всегда показывает, что серверы '0' соответствуют требованиям.

Назначение определения политики эффектом _DeployIfNotExists_ требует дополнительного уровня доступа. Чтобы предоставить наименьшую привилегию, можно создать пользовательское определение роли, которое расширяет **ресурсную политику.** Приведенный ниже пример создает роль под названием **Ресурсная политика Contributor DINE** с дополнительным разрешением _Microsoft.Authorization/roleAssignments/write._

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

### <a name="using-parameters-in-custom-guest-configuration-policies"></a>Использование параметров в пользовательских политиках конфигурации гостей

Конфигурация гостей поддерживает главные свойства конфигурации во время выполнения. Эта функция означает, что значения в файле MOF в пакете не должны считаться статичными. Значения переопределения предоставляются через политику Azure и не влияют на то, как автором или компилируются конфигурации.

Cmdlets `New-GuestConfigurationPolicy` и `Test-GuestConfigurationPolicyPackage` включают параметр под названием **Параметры**. Этот параметр принимает хэштабеля, включающее все сведения о каждом параметре, и создает необходимые разделы каждого файла, используемого для определения политики Azure.

Следующий пример создает определение политики для аудита службы, где пользователь выбирает из списка во время назначения политики.

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

Если вы хотите выпустить обновление политики, есть два поля, которые требуют внимания.

- **Версия**: При `New-GuestConfigurationPolicy` запуске cmdlet необходимо указать номер версии, превышающее то, что в настоящее время публикуется. Свойство обновляет версию назначения конфигурации гостей, чтобы агент распознал обновленный пакет.
- **contentHash**: Это свойство `New-GuestConfigurationPolicy` обновляется автоматически cmdlet. Это хэш-значение пакета, созданного `New-GuestConfigurationPackage`. Свойство должно быть `.zip` правильным для файла, который вы публикуете. Если обновляется только свойство **contentUri,** расширение не принимает пакет содержимого.

Самый простой способ выпустить обновленный пакет — повторить процесс, описанный в этой статье, и предоставить обновленный номер версии. Этот процесс гарантирует, что все свойства были правильно обновлены.

## <a name="converting-windows-group-policy-content-to-azure-policy-guest-configuration"></a>Преобразование содержимого политики Windows в конфигурацию гостей Политики Azure

Конфигурация гостей при аудите компьютеров Windows является реализацией синтаксиса PowerShell Desired State Configuration. Сообщество DSC опубликовало инструментарий для преобразования экспортируемых шаблонов групповой политики в формат DSC. Используя этот инструмент вместе с описанными выше cmdlets Guest Configuration, можно преобразовать содержимое политики Windows Group и пакет/опубликовать его для Azure Policy для аудита. Подробнее об использовании этого инструмента читайте в статье [«Быстрый запуск: Преобразование групповой политики в DSC](/powershell/scripting/dsc/quickstarts/gpo-quickstart).
После преобразования содержимого приведены выше меры для создания пакета и публикации в качестве политики Azure, такие же, как и для любого содержимого DSC.

## <a name="optional-signing-guest-configuration-packages"></a>Дополнительно: Подписание пакетов конфигурации гостей

Пользовательские политики конфигурации гостей используют хэш SHA256 для проверки пакета политики, который не изменился.
В качестве опционов клиенты могут также использовать сертификат для подписания пакетов и заставить расширение конфигурации гостей разрешить только подписанный контент.

Для включения этого сценария необходимо выполнить два шага. Запустите cmdlet, чтобы подписать пакет содержимого, и придайте тег к машинам, которые должны требовать, чтобы код был подписан.

Чтобы использовать функцию проверки `Protect-GuestConfigurationPackage` подписи, запустите cmdlet, чтобы подписать пакет, прежде чем он будет опубликован. Для этого cmdlet требуется сертификат «Подпись кода».

```azurepowershell-interactive
$Cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object {($_.Subject-eq "CN=mycert") }
Protect-GuestConfigurationPackage -Path .\package\AuditWindowsService\AuditWindowsService.zip -Certificate $Cert -Verbose
```

Параметры `Protect-GuestConfigurationPackage` cmdlet:

- **Путь**: Полный путь пакета конфигурации гостя.
- **Сертификат**: Сертификат подписи кода для подписания пакета. Этот параметр поддерживается только при подписании содержимого для Windows.

Агент GuestConfiguration ожидает, что открытый ключ сертификата будет присутствовать в `/usr/local/share/ca-certificates/extra` "Доверенных корневых сертификатах" на компьютерах Windows и на пути на машинах Linux. Для проверки подписанного содержимого узла установите открытый ключ сертификата на машине перед применением пользовательской политики. Этот процесс можно сделать с помощью любого метода внутри VM или с помощью политики Azure. Пример шаблона [приведен здесь](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-push-certificate-windows).
Политика доступа Key Vault должна позволить поставщику ресурсов Compute получить доступ к сертификатам во время развертывания. Подробные шаги можно [узнать в управлении ресурсами Azure.](../../../virtual-machines/windows/key-vault-setup.md#use-templates-to-set-up-key-vault)

Ниже приводится пример экспорта открытого ключа из сертификата подписи, для импорта в машину.

```azurepowershell-interactive
$Cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object {($_.Subject-eq "CN=mycert3") } | Select-Object -First 1
$Cert | Export-Certificate -FilePath "$env:temp\DscPublicKey.cer" -Force
```

После публикации содержимого приготовьте `GuestConfigPolicyCertificateValidation` тег `enabled` с именем и значением ко всем виртуальным машинам, где требуется подписание кода. Ознакомьтесь с [примерами тегов,](../samples/built-in-policies.md#tags) как теги могут быть доставлены в масштабе с помощью политики Azure. Как только этот тег на месте, `New-GuestConfigurationPolicy` определение политики, генерируемое с помощью cmdlet, позволяет требование через расширение конфигурации гостевых гостей.

## <a name="troubleshooting-guest-configuration-policy-assignments-preview"></a>Устранение задач для просмотра задач гостевой конфигурации (Предварительный просмотр)

В предварительном просмотре доступен инструмент для оказания помощи в устранении назначений конфигурации гостевых задач Azure Policy. Инструмент находится в предварительном просмотре и был опубликован в галерее PowerShell в качестве названия модуля [Guest Configuration Troubleshooter.](https://www.powershellgallery.com/packages/GuestConfigurationTroubleshooter/)

Для получения дополнительной информации о cmdlets в этом инструменте, используйте команду Get-Help в PowerShell, чтобы показать встроенное руководство. Как инструмент получает частые обновления, что является лучшим способом получить самую самую полную информацию.

## <a name="next-steps"></a>Следующие шаги

- Подробнее о проверке ВМ с [конфигурацией guest](../concepts/guest-configuration.md).
- Понять, как [программно создавать политики.](programmatically-create.md)
- Узнайте, как [получить данные о соответствии.](get-compliance-data.md)
