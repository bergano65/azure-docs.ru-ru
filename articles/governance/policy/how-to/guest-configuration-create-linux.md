---
title: Создание политик гостевой конфигурации для Windows
description: Узнайте, как создать политику гостевой конфигурации в Политике Azure для Linux.
ms.date: 08/17/2020
ms.topic: how-to
ms.openlocfilehash: 8bf01d8f69439f7b4d60fba76de0b7abf636c274
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/18/2020
ms.locfileid: "88547726"
---
# <a name="how-to-create-guest-configuration-policies-for-linux"></a>Создание политик гостевой конфигурации для Windows

Перед созданием настраиваемых политик ознакомьтесь с обзорной информацией о [гостевой конфигурации Политики Azure](../concepts/guest-configuration.md).
 
Дополнительные сведения о создании политик гостевой конфигурации для Windows см. на странице [Создание политик гостевой конфигурации для Windows](./guest-configuration-create.md).

При аудите Linux в гостевой конфигурации используется [Chef InSpec](https://www.inspec.io/). Профиль InSpec определяет условие, в котором должен находиться компьютер. Если при оценке конфигурации произошел сбой, то срабатывает политика **auditIfNotExists** и компьютер считается **несовместимым**.

[Гостевую конфигурацию Политики Azure](../concepts/guest-configuration.md) можно использовать только для аудита параметров компьютера. Исправление параметров на виртуальных машинах пока недоступно.

Используйте следующие действия, чтобы создать собственную конфигурацию для проверки состояния компьютера Azure или компьютера, отличного от Azure.

> [!IMPORTANT]
> Настраиваемые политики с гостевой конфигурацией доступны в предварительной версии.
>
> Для аудита виртуальных машин Azure требуется расширение гостевой конфигурации. Чтобы развернуть расширение в большом масштабе на всех компьютерах Linux, назначьте следующее определение политики:
> - [Развертывание необходимых компонентов для политики гостевой конфигурации на виртуальных машинах Linux](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffb27e9e0-526e-4ae1-89f2-a2a0bf0f8a50)

## <a name="install-the-powershell-module"></a>Установка модуля PowerShell

Модуль гостевой конфигурации автоматизирует процесс создания настраиваемого содержимого, в том числе:

- создание артефакта содержимого гостевой конфигурации (ZIP-файл);
- автоматическое тестирование артефакта;
- создание определения политики;
- публикация политики.

Модуль можно установить на компьютере под управлением ОС Windows, macOS или Linux с помощью PowerShell 6.2 или более поздней версии, установленного локально, или с помощью [Azure Cloud Shell](https://shell.azure.com), а также с помощью [основного образа Docker для Azure PowerShell](https://hub.docker.com/r/azuresdk/azure-powershell-core).

> [!NOTE]
> Компиляция конфигураций не поддерживается в Linux.

### <a name="base-requirements"></a>Основные требования

Операционные системы, в которых можно установить модуль:

- Linux
- macOS
- Windows

> [!NOTE]
> Для командлета Test-Гуестконфигуратионпаккаже требуется OpenSSL версии 1,0 из-за зависимости от OMI. Это приводит к ошибке в любой среде с OpenSSL 1,1 или более поздней версии.

Для модуля ресурсов гостевой конфигурации требуется следующее программное обеспечение:

- PowerShell 6.2 или более поздней версии. Если она еще не установлена, выполните [эти инструкции](/powershell/scripting/install/installing-powershell).
- Azure PowerShell 1.5.0 или более поздней версии. Если она еще не установлена, выполните [эти инструкции](/powershell/azure/install-az-ps).
  - Требуются только модули AZ "az. Accounts" и "az. Resources".

### <a name="install-the-module"></a>Установка модуля

Порядок установки модуля **GuestConfiguration** в PowerShell

1. В командной строке PowerShell выполните следующую команду:

   ```azurepowershell-interactive
   # Install the Guest Configuration DSC resource module from PowerShell Gallery
   Install-Module -Name GuestConfiguration
   ```

1. Убедитесь, что модуль импортирован.

   ```azurepowershell-interactive
   # Get a list of commands for the imported GuestConfiguration module
   Get-Command -Module 'GuestConfiguration'
   ```

## <a name="guest-configuration-artifacts-and-policy-for-linux"></a>Артефакты и политика гостевой конфигурации для Linux

Даже в средах Linux гостевая конфигурация использует настройку требуемого состояния в качестве абстракции языка. Реализация основана на машинном коде (C++), поэтому для нее не требуется загружать PowerShell. Однако для этого требуется MOF-файл конфигурации, описывающий сведения о среде.
DSC выступает в качестве оболочки для стандартизации, чтобы стандартизировать выполнение, способ указания параметров и способ возврата выходных данных в службу. При работе с настраиваемым содержимым InSpec требуется небольшое знание DSC.

#### <a name="configuration-requirements"></a>Требования настройки

Имя настраиваемой конфигурации должно быть одинаковым везде. Имя ZIP-файла для пакета содержимого, имя конфигурации в MOF-файле и имя назначения гостя в шаблоне Azure Resource Manager (шаблон ARM) должны быть одинаковыми.

### <a name="custom-guest-configuration-configuration-on-linux"></a>Настраиваемая гостевая конфигурации в Linux

Гостевая конфигурация в Linux использует ресурс `ChefInSpecResource` для предоставления подсистеме имени [профиля InSpec](https://www.inspec.io/docs/reference/profiles/). **Имя** является единственным обязательным свойством ресурса. Создайте файл YaML и файл сценария Ruby, как описано ниже.

Сначала создайте файл YaML, используемый InSpec. Этот файл содержит основные сведения о среде. Ниже приведен пример кода.

```YaML
name: linux-path
title: Linux path
maintainer: Test
summary: Test profile
license: MIT
version: 1.0.0
supports:
    - os-family: unix
```

Сохраните этот файл с именем `inspec.yml` в папку `linux-path` в каталоге проекта.

Затем создайте файл Ruby с абстракцией языка InSpec, используемой для аудита компьютера.

```Ruby
describe file('/tmp') do
    it { should exist }
end
```

Сохраните этот файл с именем `linux-path.rb` в новой папке `controls` в каталоге `linux-path`.

Наконец, создайте конфигурацию, импортируйте модуль ресурсов **PSDesiredStateConfiguration** и скомпилируйте конфигурацию.

```powershell
# Define the configuration and import GuestConfiguration
Configuration AuditFilePathExists
{
    Import-DscResource -ModuleName 'GuestConfiguration'

    Node AuditFilePathExists
    {
        ChefInSpecResource 'Audit Linux path exists'
        {
            Name = 'linux-path'
        }
    }
}

# Compile the configuration to create the MOF files
import-module PSDesiredStateConfiguration
AuditFilePathExists -out ./Config
```

Сохраните этот файл с именем `config.ps1` в папке проекта. Запустите его в PowerShell, выполнив команду `./config.ps1` в терминале. Будет создан новый MOF-файл.

Команда `Node AuditFilePathExists` не является технически необходимой, однако она создает файл с именем `AuditFilePathExists.mof`, а не с именем по умолчанию `localhost.mof`. Когда имя MOF-файла соответствует конфигурации, это позволяет с легкостью упорядочить множество файлов в большом масштабе.

Теперь у вас должна быть структура проекта, аналогичная показанной ниже.

```file
/ AuditFilePathExists
    / Config
        AuditFilePathExists.mof
    / linux-path
        linux-path.yml
        / controls
            linux-path.rb 
```

Вспомогательные файлы должны быть упакованы вместе. Готовый пакет используется гостевой конфигурацией для создания определений в службе "Политика Azure".

Командлет `New-GuestConfigurationPackage` создает пакет. Параметры командлета `New-GuestConfigurationPackage` при создании содержимого Linux:

- **Name** (Имя). Имя пакета гостевой конфигурации.
- **Конфигурация.** Полный путь к скомпилированному документу конфигурации.
- **Путь**. Путь к выходной папке. Это необязательный параметр. Если этот параметр не указан, пакет создается в текущем каталоге.
- **ChefProfilePath**. Полный путь к профилю InSpec. Этот параметр поддерживается только при создании содержимого для аудита Linux.

Выполните следующую команду, чтобы создать пакет с использованием конфигурации, заданной на предыдущем шаге.

```azurepowershell-interactive
New-GuestConfigurationPackage `
  -Name 'AuditFilePathExists' `
  -Configuration './Config/AuditFilePathExists.mof' `
  -ChefInSpecProfilePath './'
```

После создания пакета конфигурации, но перед его публикацией в Azure, можно протестировать пакет с рабочей станции или в среде CI/CD. Командлет гостевой конфигурации `Test-GuestConfigurationPackage` включает в среду разработки тот же агент, который используется на компьютерах Azure. С помощью этого решения можно выполнить локальное тестирование интеграции перед выпуском в платных облачных средах.

Поскольку агент фактически оценивает локальную среду, в большинстве случаев командлет тестирования следует выполнять на той же платформе операционной системы, на которой вы планируете проводить аудит.

Параметры командлета `Test-GuestConfigurationPackage`:

- **Name** (Имя). Имя политики гостевой конфигурации.
- **Parameter**. Параметры политики, указанные в формате хэш-таблицы.
- **Путь**. Полный путь к пакету гостевой конфигурации.

Выполните следующую команду, чтобы протестировать пакет, созданный на предыдущем шаге.

```azurepowershell-interactive
Test-GuestConfigurationPackage `
  -Path ./AuditFilePathExists/AuditFilePathExists.zip
```

Этот командлет также поддерживает ввод данных из конвейера PowerShell. Передайте выходные данные командлета `New-GuestConfigurationPackage` в командлет `Test-GuestConfigurationPackage`.

```azurepowershell-interactive
New-GuestConfigurationPackage -Name AuditFilePathExists -Configuration ./Config/AuditFilePathExists.mof -ChefProfilePath './' | Test-GuestConfigurationPackage
```

Следующим шагом является публикация файла в хранилище BLOB-объектов. Приведенный ниже скрипт содержит функцию, которую можно использовать для автоматизации этой задачи. Для команд, используемых в функции `publish`, требуется модуль `Az.Storage`.

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
  -filePath ./AuditFilePathExists.zip `
  -blobName 'AuditFilePathExists'
```
После создания и отправки пакета настраиваемой политики гостевой конфигурации создайте определение политики гостевой конфигурации. Командлет `New-GuestConfigurationPolicy` принимает пакет настраиваемой политики и создает определение политики.

Командлет `New-GuestConfigurationPolicy` принимает следующие параметры:

- **ContentUri**. Общедоступный URI HTTP(S) пакета содержимого гостевой конфигурации.
- **DisplayName**. Отображаемое имя политики.
- **Описание**: описание политики.
- **Parameter**. Параметры политики, указанные в формате хэш-таблицы.
- **Версия.** Версия политики.
- **Путь**. Целевой путь, по которому создаются определения политик.
- **Platform**. Целевая платформа (Windows или Linux) для политики гостевой конфигурации и пакета содержимого.
- **Tag** —добавляет один или несколько фильтров тегов к определению политики.
- **Category** — задает поле метаданных категории в определении политики.

В следующем примере создаются определения политики из пакета настраиваемой политики в папке, к которой указан путь.

```azurepowershell-interactive
New-GuestConfigurationPolicy `
    -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditFilePathExists.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
    -DisplayName 'Audit Linux file path.' `
    -Description 'Audit that a file path exists on a Linux machine.' `
    -Path './policies' `
    -Platform 'Linux' `
    -Version 1.0.0 `
    -Verbose
```

Командлет `New-GuestConfigurationPolicy` создает следующие файлы:

- **auditIfNotExists.json**;
- **deployIfNotExists.json**;
- **Initiative.json**.

В выходных данных командлета возвращается объект, содержащий отображаемое имя инициативы и путь к файлам политики.

Опубликуйте определения политики с помощью командлета `Publish-GuestConfigurationPolicy`. Командлет содержит только параметр **Path**, указывающий расположение JSON-файлов, созданных `New-GuestConfigurationPolicy`.

Чтобы выполнить команду публикации, необходим доступ к созданию политик в Azure. Требования к авторизации описаны на странице [Общие сведения о Политике Azure](../overview.md). Наиболее подходящей встроенной ролью является **Участник политики ресурсов**.

```azurepowershell-interactive
Publish-GuestConfigurationPolicy `
  -Path '.\policyDefinitions'
```

 Командлет `Publish-GuestConfigurationPolicy` принимает путь из конвейера PowerShell. Эта функция позволяет создать файлы политики и опубликовать их в одном наборе последовательных команд.

 ```azurepowershell-interactive
 New-GuestConfigurationPolicy `
  -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditFilePathExists.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
  -DisplayName 'Audit Linux file path.' `
  -Description 'Audit that a file path exists on a Linux machine.' `
  -Path './policies' `
 | Publish-GuestConfigurationPolicy
 ```

После создания политики в Azure последним шагом является назначение инициативы. Узнайте о том, как назначить инициативу с помощью [портала](../assign-policy-portal.md), [Azure CLI](../assign-policy-azurecli.md) и [Azure PowerShell](../assign-policy-powershell.md).

> [!IMPORTANT]
> Политики гостевой конфигурации **всегда** следует назначать с помощью инициативы, объединяющей политики _AuditIfNotExists_ и _DeployIfNotExists_. Если назначена только политика _AuditIfNotExists_, необходимые компоненты не развертываются, а в сведениях о политике всегда показано, что "0" серверов соответствуют требованиям.

Для назначения определения политики с помощью _DeployIfNotExists_ требуется дополнительный уровень доступа. Чтобы предоставить минимальные разрешения, можно создать пользовательское определение роли, расширяющее возможности роли **Участник политики ресурсов**. В приведенном ниже примере создается роль с именем **Resource Policy Contributor DINE** с дополнительным разрешением _Microsoft.Authorization/roleAssignments/write_.

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

### <a name="using-parameters-in-custom-guest-configuration-policies"></a>Использование параметров в настраиваемых политиках гостевой конфигурации

Гостевая конфигурация поддерживает переопределение свойств конфигурации во время выполнения. Эта возможность позволяет использовать в MOF-файле пакета не только статические значения. Переопределяемые значения предоставляются с помощью Политики Azure и не влияют на то, как создаются или компилируются конфигурации.

При использовании InSpec параметры обычно обрабатываются как входные данные во время выполнения или как код с помощью атрибутов. В гостевой конфигурации этот процесс скрыт, поэтому при назначении политики могут быть предоставлены входные данные. На компьютере автоматически создается файл атрибутов. Вам не нужно создавать и добавлять файл в проект. Добавление параметров в проект аудита Linux включает два этапа.

Определите входные данные в файле Ruby, в котором вы задаете сценарий аудита на компьютере. Ниже приведен пример кода.

```Ruby
attr_path = attribute('path', description: 'The file path to validate.')

describe file(attr_path) do
    it { should exist }
end
```

Командлеты `New-GuestConfigurationPolicy` и `Test-GuestConfigurationPolicyPackage` включают параметр с именем **Parameter**. Этот параметр принимает хэш-таблицу, содержащую все сведения о каждом параметре, и автоматически создает все необходимые разделы файлов, используемых для создания каждого определения Политики Azure.

В следующем примере создается определение политики для аудита пути к файлу, где пользователь предоставляет путь во время назначения политики.

```azurepowershell-interactive
$PolicyParameterInfo = @(
    @{
        Name = 'FilePath'                             # Policy parameter name (mandatory)
        DisplayName = 'File path.'                    # Policy parameter display name (mandatory)
        Description = "File path to be audited."      # Policy parameter description (optional)
        ResourceType = "ChefInSpecResource"           # Configuration resource type (mandatory)
        ResourceId = 'Audit Linux path exists'        # Configuration resource property name (mandatory)
        ResourcePropertyName = "AttributesYmlContent" # Configuration resource property name (mandatory)
        DefaultValue = '/tmp'                         # Policy parameter default value (optional)
    }
)

# The hashtable also supports a property named 'AllowedValues' with an array of strings to limit input to a list

New-GuestConfigurationPolicy
    -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditFilePathExists.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
    -DisplayName 'Audit Linux file path.' `
    -Description 'Audit that a file path exists on a Linux machine.' `
    -Path './policies' `
    -Parameter $PolicyParameterInfo `
    -Version 1.0.0
```

Для политик Linux Включите свойство **AttributesYmlContent** в конфигурацию и при необходимости перезапишите значения. Агент гостевой конфигурации автоматически создает файл YAML, используемый InSpec для хранения атрибутов. См. пример ниже.

```powershell
Configuration AuditFilePathExists
{
    Import-DscResource -ModuleName 'GuestConfiguration'

    Node AuditFilePathExists
    {
        ChefInSpecResource 'Audit Linux path exists'
        {
            Name = 'linux-path'
            AttributesYmlContent = "path: /tmp"
        }
    }
}
```

## <a name="policy-lifecycle"></a>Жизненный цикл политики

Для выпуска обновления определения политики необходимо обратить внимание на два поля.

- **Версия.** При запуске командлета `New-GuestConfigurationPolicy` необходимо указать номер версии, который больше текущей опубликованной версии. Это свойство обновляет версию назначения гостевой конфигурации, чтобы агент распознал обновленный пакет.
- **contentHash**. Это свойство обновляется автоматически командлетом `New-GuestConfigurationPolicy`. Это значение хэша пакета, созданного командлетом `New-GuestConfigurationPackage`. Это свойство должно соответствовать публикуемому файлу `.zip`. Если обновляется только свойство **contentUri**, расширение не примет пакет содержимого.

Самый простой способ для выпуска обновленного пакета — повторить процедуру, описанную в этой статье, и указать новый номер версии. Этот позволит гарантировать, что все свойства были обновлены правильно.


### <a name="filtering-guest-configuration-policies-using-tags"></a>Фильтрация политик гостевой конфигурации с помощью тегов

Политики, созданные командлетами в модуле гостевой конфигурации, при необходимости могут включать фильтр для тегов. Параметр **-Tag** `New-GuestConfigurationPolicy` поддерживает массив хэш-таблиц, содержащих отдельные теги. Теги будут добавлены в раздел `If` определения политики. Их не удастся изменить с помощью назначения политики.

Ниже приведен пример фрагмента определения политики, который будет фильтровать теги.

```json
"if": {
  "allOf" : [
    {
      "allOf": [
        {
          "field": "tags.Owner",
          "equals": "BusinessUnit"
        },
        {
          "field": "tags.Role",
          "equals": "Web"
        }
      ]
    },
    {
      // Original Guest Configuration content will follow
    }
  ]
}
```

## <a name="optional-signing-guest-configuration-packages"></a>Необязательное действие: подписывание пакетов гостевой конфигурации

Настраиваемые политики гостевой конфигурации используют хэш SHA256 для проверки того, что пакет политики не изменился.
При необходимости клиенты также могут использовать сертификат для подписывания пакетов и настроить расширение гостевой конфигурации на использование только подписанного содержимого.

Чтобы реализовать этот сценарий, необходимо выполнить два действия. Запустите командлет, чтобы подписать пакет содержимого, и добавьте тег на компьютеры, требующие подписания кода.

Чтобы использовать функцию проверки подписи, запустите командлет `Protect-GuestConfigurationPackage` для подписания пакета перед его публикацией. Для этого командлета требуется сертификат подписывания кода.

Командлет `Protect-GuestConfigurationPackage` принимает следующие параметры:

- **Путь**. Полный путь к пакету гостевой конфигурации.
- **PublicGpgKeyPath**. Путь к открытому ключу GPG. Этот параметр поддерживается только при подписывании содержимого для Linux.

Хороший справочник по созданию ключей GPG для использования с компьютерами Linux приведен в статье на сайте GitHub, посвященной [созданию нового ключа GPG](https://help.github.com/en/articles/generating-a-new-gpg-key).

Агент гостевой конфигурации ожидает наличия открытого ключа сертификата в пути `/usr/local/share/ca-certificates/extra` на компьютерах Linux. Чтобы узел проверял подписанное содержимое, установите открытый ключ сертификата на компьютер перед применением настраиваемой политики. Это можно выполнить с помощью любой методики в виртуальной машине или с помощью Политики Azure. Пример шаблона [приведен здесь](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-push-certificate-windows).
Политика доступа Key Vault должна разрешать поставщику вычислительных ресурсов доступ к сертификатам во время развертывания. Подробные инструкции см. в разделе [Настройка хранилища ключей Key Vault для виртуальных машин в Azure Resource Manager](../../../virtual-machines/windows/key-vault-setup.md#use-templates-to-set-up-key-vault).

После публикации содержимого добавьте тег с именем `GuestConfigPolicyCertificateValidation` и значением `enabled` на все виртуальные машины, где требуется подписывание кода. См. [примеры тегов](../samples/built-in-policies.md#tags), чтобы узнать, как организовать доставку тегов в большом масштабе с помощью службы "Политика Azure". Когда тег будет создан, определение политики, созданное с помощью командлета `New-GuestConfigurationPolicy`, обеспечивает соблюдение требования за счет расширения гостевой конфигурации.

## <a name="troubleshooting-guest-configuration-policy-assignments-preview"></a>Устранение неполадок при назначении политики гостевой конфигурации (предварительная версия)

Средство устранения неполадок с назначениями гостевой конфигурации в службе "Политика Azure" доступно в предварительной версии. Оно опубликовано в коллекции PowerShell как модуль с именем [Guest Configuration Troubleshooter](https://www.powershellgallery.com/packages/GuestConfigurationTroubleshooter/).

Чтобы получить дополнительные сведения о командлетах в этом средстве, используйте команду Get-Help в PowerShell для отображения встроенных инструкций. Поскольку средство довольно часто обновляется, этот способ лучше всего подходит для получения актуальной информации.

## <a name="next-steps"></a>Дальнейшие действия

- Узнайте подробнее об аудите виртуальных машин с помощью [гостевой конфигурации](../concepts/guest-configuration.md).
- Узнайте о [программном создании политик](./programmatically-create.md).
- Узнайте, как [получать данные о соответствии](./get-compliance-data.md).
