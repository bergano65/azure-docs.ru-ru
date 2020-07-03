---
title: Создание политик конфигурации гостя для Linux
description: Узнайте, как создать политику гостевой конфигурации политики Azure для Linux.
ms.date: 03/20/2020
ms.topic: how-to
ms.openlocfilehash: 219b38bd81cae8d16241d1ee16cfdd2f400ae91e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82024988"
---
# <a name="how-to-create-guest-configuration-policies-for-linux"></a>Создание политик конфигурации гостя для Linux

Перед созданием настраиваемых политик прочитайте обзорную информацию в [конфигурации гостя политики Azure](../concepts/guest-configuration.md).
 
Дополнительные сведения о создании политик гостевой конфигурации для Windows см. на странице [Создание политик конфигурации гостей для Windows](./guest-configuration-create.md) .

При аудите Linux в гостевой конфигурации используется [Chefая спецификация](https://www.inspec.io/). Профиль "Спец" определяет условие, в котором должен находиться компьютер. Если при вычислении конфигурации произошел сбой, то активируется действие политики **помощью параметров auditifnotexists** , и компьютер **считается несовместимым.**

[Гостевую конфигурацию политики Azure](../concepts/guest-configuration.md) можно использовать только для аудита параметров внутри компьютеров. Исправление параметров в виртуальных машинах пока недоступно.

Используйте следующие действия, чтобы создать собственную конфигурацию для проверки состояния виртуальной машины Azure или платформы, отличной от Azure.

> [!IMPORTANT]
> Настраиваемые политики с гостевой конфигурацией — это предварительная версия функции.
>
> Для проведения аудита на виртуальных машинах Azure требуется расширение конфигурации гостевой виртуальной машины.
> Чтобы развернуть расширение в масштабе на всех компьютерах Linux, назначьте следующее определение политики:
>   - [Развертывание необходимых компонентов для политики гостевой конфигурации на виртуальных машинах Linux](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffb27e9e0-526e-4ae1-89f2-a2a0bf0f8a50)

## <a name="install-the-powershell-module"></a>Установка модуля PowerShell

Создание артефакта конфигурации гостя, автоматическое тестирование артефакта, создание определения политики и публикация политики, полностью автоматизируются с помощью модуля гостевой настройки в PowerShell. Модуль можно установить на компьютере под управлением Windows, macOS или Linux с помощью PowerShell 6,2 или более поздней версии, локально или [Azure Cloud Shell](https://shell.azure.com)или с [образом Azure PowerShell Core DOCKER](https://hub.docker.com/r/azuresdk/azure-powershell-core).

> [!NOTE]
> Компиляция конфигураций не поддерживается в Linux.

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

## <a name="guest-configuration-artifacts-and-policy-for-linux"></a>Артефакты и политика гостевой настройки для Linux

Даже в средах Linux Гостевая конфигурация использует настройку требуемого состояния в качестве абстракции языка. Реализация основана на машинном коде (C++), поэтому для нее не требуется загрузка PowerShell. Однако для этого требуется MOF-файл конфигурации, описывающий сведения о среде. DSC выступает в качестве оболочки для стандартизации, чтобы стандартизировать выполнение, способ указания параметров и способ возврата выходных данных в службу. При работе с пользовательским содержимым требуется небольшое знание DSC.

#### <a name="configuration-requirements"></a>Требования настройки

Имя пользовательской конфигурации должно быть одинаковым по всему миру. Имя ZIP-файла для пакета содержимого, имя конфигурации в MOF-файле и имя назначения гостя в шаблоне диспетчер ресурсов должны совпадать.

### <a name="custom-guest-configuration-configuration-on-linux"></a>Настраиваемая конфигурация гостевой конфигурации в Linux

Гостевая конфигурация в Linux использует `ChefInSpecResource` ресурс для предоставления подсистеме имени [профиля](https://www.inspec.io/docs/reference/profiles/). **Имя** является единственным обязательным свойством ресурса. Создайте файл YaML и файл скрипта Ruby, как описано ниже.

Сначала создайте файл YaML, используемый в спецификации. Файл содержит основные сведения о среде. Ниже приведен пример.

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

Сохраните этот файл с именем `inspec.yml` в папке `linux-path` в каталоге проекта.

Затем создайте файл Ruby с абстракцией языка программирования, используемой для аудита компьютера.

```Ruby
describe file('/tmp') do
    it { should exist }
end
```

Сохраните этот файл с именем `linux-path.rb` в новой папке `controls` внутри `linux-path` каталога.

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

Сохраните этот файл с именем `config.ps1` в папке проекта. Запустите его в PowerShell, выполнив `./config.ps1` в терминале. Будет создан новый MOF-файл.

`Node AuditFilePathExists` Команда не является технически необходимой, но создает файл с именем `AuditFilePathExists.mof` , а не по умолчанию, `localhost.mof`. Наличие имени MOF-файла соответствует конфигурации, что позволяет легко организовать множество файлов при масштабе работы.



Теперь у вас должна быть структура проекта, как показано ниже.

```file
/ AuditFilePathExists
    / Config
        AuditFilePathExists.mof
    / linux-path
        linux-path.yml
        / controls
            linux-path.rb 
```

Вспомогательные файлы должны быть упакованы вместе. Завершенный пакет используется гостевой конфигурацией для создания определений политик Azure.

`New-GuestConfigurationPackage` Командлет создает пакет. Параметры `New-GuestConfigurationPackage` командлета при создании содержимого Linux:

- **Имя**: имя гостевого пакета конфигурации.
- **Конфигурация**: полный путь к документу скомпилированной конфигурации.
- **Путь**: путь к выходной папке. Это необязательный параметр. Если этот параметр не указан, пакет создается в текущем каталоге.
- **Чефпрофилепас**: полный путь к профилю с неполными характеристиками. Этот параметр поддерживается только при создании содержимого для аудита Linux.

Выполните следующую команду, чтобы создать пакет с использованием конфигурации, заданной на предыдущем шаге.

```azurepowershell-interactive
New-GuestConfigurationPackage `
  -Name 'AuditFilePathExists' `
  -Configuration './Config/AuditFilePathExists.mof' `
  -ChefInSpecProfilePath './'
```

После создания пакета конфигурации, но перед его публикацией в Azure можно протестировать пакет с рабочей станции или среды CI/CD. Командлет `Test-GuestConfigurationPackage` гуестконфигуратион включает в среду разработки тот же агент, который используется на компьютерах Azure. С помощью этого решения можно выполнить локальное тестирование интеграции перед выпуском в выставляемые облачные среды.

Так как агент фактически оценивает локальную среду, в большинстве случаев необходимо выполнить командлет Test-на той же платформе операционной системы, что и вы планируете проводить аудит.

Параметры `Test-GuestConfigurationPackage` командлета:

- **Имя**: имя политики конфигурации гостя.
- **Параметр**: параметры политики, указанные в формате Hashtable.
- **Путь**: полный путь к пакету конфигурации гостя.

Выполните следующую команду, чтобы протестировать пакет, созданный на предыдущем шаге:

```azurepowershell-interactive
Test-GuestConfigurationPackage `
  -Path ./AuditFilePathExists/AuditFilePathExists.zip
```

Командлет также поддерживает входные данные из конвейера PowerShell. Передать выходные данные `New-GuestConfigurationPackage` командлета в `Test-GuestConfigurationPackage` командлет.

```azurepowershell-interactive
New-GuestConfigurationPackage -Name AuditFilePathExists -Configuration ./Config/AuditFilePathExists.mof -ChefProfilePath './' | Test-GuestConfigurationPackage
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
  -filePath ./AuditFilePathExists.zip `
  -blobName 'AuditFilePathExists'
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
    -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditFilePathExists.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
    -DisplayName 'Audit Linux file path.' `
    -Description 'Audit that a file path exists on a Linux machine.' `
    -Path './policies' `
    -Platform 'Linux' `
    -Version 1.0.0 `
    -Verbose
```

Следующие файлы создаются `New-GuestConfigurationPolicy`:

- **Помощью параметров auditifnotexists. JSON**
- **deployIfNotExists. JSON**
- **Initiative. JSON**

Выходные данные командлета возвращают объект, содержащий отображаемое имя инициативы и путь к файлам политики.

Наконец, опубликуйте определения политик с помощью `Publish-GuestConfigurationPolicy` командлета.
Командлет содержит только параметр **path** , указывающий на расположение JSON файлов, созданных `New-GuestConfigurationPolicy`.

Чтобы выполнить команду публикации, необходимо получить доступ к созданию политик в Azure. Конкретные требования к авторизации описаны на странице [Обзор политики Azure](../overview.md) . Наиболее подходящей встроенной ролью является **участник политики ресурсов**.

```azurepowershell-interactive
Publish-GuestConfigurationPolicy `
  -Path '.\policyDefinitions'
```

 `Publish-GuestConfigurationPolicy` Командлет принимает путь из конвейера PowerShell. Эта функция означает, что вы можете создать файлы политики и опубликовать их в одном наборе команд с последовательностью.

 ```azurepowershell-interactive
 New-GuestConfigurationPolicy `
  -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditFilePathExists.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
  -DisplayName 'Audit Linux file path.' `
  -Description 'Audit that a file path exists on a Linux machine.' `
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

При использовании функции Spec параметры обычно обрабатываются как входные данные во время выполнения или как код с помощью атрибутов. Гостевая конфигурация скрывает этот процесс, поэтому при назначении политики могут быть предоставлены входные данные. На компьютере автоматически создается файл атрибутов. Вам не нужно создавать и добавлять файл в проект. Добавление параметров в проект аудита Linux состоит из двух этапов.

Определите входные данные в файле Ruby, в котором вы задаете скрипты для аудита на компьютере. Ниже приведен пример.

```Ruby
attr_path = attribute('path', description: 'The file path to validate.')

describe file(attr_path) do
    it { should exist }
end
```

Командлеты `New-GuestConfigurationPolicy` и `Test-GuestConfigurationPolicyPackage` включают параметр с именем **Parameters**. Этот параметр принимает хэш-таблицу, включающую все сведения о каждом параметре, и автоматически создает все необходимые разделы файлов, используемых для создания каждого определения политики Azure.

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
    -Parameters $PolicyParameterInfo `
    -Version 1.0.0
```

Для политик Linux Включите свойство **аттрибутесимлконтент** в конфигурацию и при необходимости перезапишите значения. Агент конфигурации гостя автоматически создает файл YAML, используемый в параметре спец для хранения атрибутов. См. пример ниже.

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

Чтобы освободить обновление определения политики, необходимо обратить внимание на два поля.

- **Версия**: при запуске `New-GuestConfigurationPolicy` командлета необходимо указать номер версии, превышающий текущую публикацию. Свойство обновляет версию назначения гостевой конфигурации, чтобы агент распознал обновленный пакет.
- **контенсаш**: это свойство автоматически обновляется `New-GuestConfigurationPolicy` командлетом. Это хэш-значение пакета, созданного `New-GuestConfigurationPackage`. Свойство должно быть правильным для публикуемого `.zip` файла. Если обновляется только свойство **контентури** , расширение не принимает пакет содержимого.

Самый простой способ выпуска обновленного пакета — повторить процедуру, описанную в этой статье, и указать обновленный номер версии. Этот процесс гарантирует, что все свойства были правильно обновлены.

## <a name="optional-signing-guest-configuration-packages"></a>Необязательно. Подписывание пакетов конфигурации гостей

Настраиваемые политики гостевой конфигурации используют хэш SHA256 для проверки того, что пакет политики не изменился.
При необходимости клиенты также могут использовать сертификат для подписывания пакетов и принудительно настроить гостевой модуль настройки, разрешающий только подписанное содержимое.

Чтобы включить этот сценарий, необходимо выполнить два действия. Запустите командлет, чтобы подписать пакет содержимого, и добавьте тег на компьютеры, требующие подписания кода.

Чтобы использовать функцию проверки подписи, запустите `Protect-GuestConfigurationPackage` командлет, чтобы подписать пакет перед его публикацией. Для этого командлета требуется сертификат подписи кода.

Параметры `Protect-GuestConfigurationPackage` командлета:

- **Путь**: полный путь к пакету конфигурации гостя.
- **Публикгпгкэйпас**: путь к открытому ключу GPG. Этот параметр поддерживается только при подписывании содержимого для Linux.

Хороший справочник по созданию ключей GPG для использования с компьютерами Linux приведен в статье на сайте GitHub, [создающей новый ключ GPG](https://help.github.com/en/articles/generating-a-new-gpg-key).

Агент Гуестконфигуратион ждет наличия открытого ключа сертификата в пути `/usr/local/share/ca-certificates/extra` на компьютерах Linux. Чтобы узел проверял подписанное содержимое, установите открытый ключ сертификата на компьютере перед применением настраиваемой политики. Этот процесс можно выполнить с помощью любой методики в виртуальной машине или с помощью политики Azure. Пример шаблона [приведен здесь](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-push-certificate-windows).
Политика доступа Key Vault должна разрешить поставщику ресурсов вычислений доступ к сертификатам во время развертывания. Подробные инструкции см. [в разделе настройка Key Vault для виртуальных машин в Azure Resource Manager](../../../virtual-machines/windows/key-vault-setup.md#use-templates-to-set-up-key-vault).

После публикации содержимого добавьте тег с именем `GuestConfigPolicyCertificateValidation` и значением `enabled` для всех виртуальных машин, где требуется подписывание кода. См. [Примеры тегов](../samples/built-in-policies.md#tags) для доставки тегов в масштабе с помощью политики Azure. После создания тега определение политики, созданное с помощью `New-GuestConfigurationPolicy` командлета, обеспечивает требование с помощью модуля настройки гостевой конфигурации.

## <a name="troubleshooting-guest-configuration-policy-assignments-preview"></a>Устранение неполадок назначений политик гостевой конфигурации (Предварительная версия)

Средство можно просмотреть в предварительной версии, чтобы помочь в устранении неполадок назначений гостевой конфигурации политики Azure. Средство находится на этапе предварительной версии и Опубликовано в коллекция PowerShell в качестве имени модуля [средство устранения неполадок конфигурации гостя](https://www.powershellgallery.com/packages/GuestConfigurationTroubleshooter/).

Для получения дополнительных сведений о командлетах в этом средстве используйте команду Get-Help в PowerShell, чтобы отобразить встроенные инструкции. Так как средство получает частые обновления, это лучший способ получить самую свежую информацию.

## <a name="next-steps"></a>Дальнейшие шаги

- Узнайте, как проводить аудит виртуальных машин с помощью [гостевой конфигурации](../concepts/guest-configuration.md).
- Узнайте, как [программно создавать политики](programmatically-create.md).
- Узнайте, как [получить данные о соответствии](get-compliance-data.md).
