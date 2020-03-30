---
title: Как создать политики конфигурации гостей для Linux
description: Узнайте, как создать политику конфигурации гостевых гостей Azure Policy для Linux.
ms.date: 03/20/2020
ms.topic: how-to
ms.openlocfilehash: f93aafc8f2c016218b1b7fea82558ea6ba4b4ff8
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80365399"
---
# <a name="how-to-create-guest-configuration-policies-for-linux"></a>Как создать политики конфигурации гостей для Linux

Перед созданием пользовательских политик прочитайте обзорную информацию в [конфигурации гостей политики Azure.](../concepts/guest-configuration.md)
 
Чтобы узнать о создании политик конфигурации гостей для Windows, смотрите [страницу Как создать политики конфигурации гостевых для Windows](./guest-configuration-create.md)

При аудите Linux, Guest Configuration использует [шеф-повара InSpec](https://www.inspec.io/). Профиль InSpec определяет состояние, в которое должна находиться машина. Если оценка конфигурации не удается, срабатывает **аудиторский эффект политикиIfNotExists** и машина считается **несоответствующей.**

[Конфигурация гостей Azure Policy](../concepts/guest-configuration.md) может использоваться только для проверки настроек внутри машин. Восстановление настроек внутри машин пока недоступно.

Используйте следующие действия для создания собственной конфигурации для проверки состояния машины Azure или не-Azure.

> [!IMPORTANT]
> Пользовательские политики с конфигурацией гостей — это функция предварительного просмотра.

## <a name="install-the-powershell-module"></a>Установка модуля PowerShell

Создание артефакта конфигурации гостей, автоматическое тестирование артефакта, создание определения политики и публикация политики полностью автоматизировано с помощью модуля конфигурации гостей в PowerShell. Модуль может быть установлен на машине под управлением Windows, macOS или Linux с PowerShell 6.2 или позже работает локально, или с [Azure Cloud Shell](https://shell.azure.com), или с [изображением Azure PowerShell Core Docker.](https://hub.docker.com/r/azuresdk/azure-powershell-core)

> [!NOTE]
> Компиляция конфигураций не поддерживается на Linux.

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

## <a name="guest-configuration-artifacts-and-policy-for-linux"></a>Артефакты и политика конфигурации гостей для Linux

Даже в средах Linux Guest Configuration использует конфигурацию «Пожеланное состояние» в качестве языковой абстракции. Реализация основана на родном коде (СЗ), поэтому она не требует загрузки PowerShell. Тем не менее, это требует конфигурации MOF, описывающей детали среды. DSC выступает в качестве обертки для InSpec для стандартизации того, как она выполняется, как параметры предоставляются, и как выход возвращается в службу. При работе с пользовательским контентом InSpec требуется мало знаний о DSC.

#### <a name="configuration-requirements"></a>Требования настройки

Название пользовательской конфигурации должно быть последовательным везде. Имя файла .zip для пакета содержимого, имя конфигурации в файле MOF и имя назначения гостя в шаблоне менеджера ресурсов должно быть одинаковым.

### <a name="custom-guest-configuration-configuration-on-linux"></a>Пользовательская конфигурация гостевой конфигурации на Linux

Конфигурация гостей на `ChefInSpecResource` Linux использует ресурс, чтобы предоставить движку название [профиля InSpec.](https://www.inspec.io/docs/reference/profiles/) **Имя** является единственным необходимым свойством ресурса. Создайте файл YaML и файл сценария Ruby, как описано ниже.

Во-первых, создайте файл YaML, используемый InSpec. Файл предоставляет основную информацию об окружающей среде. Приведен пример ниже:

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

Сохраните этот файл в `linux-path` папке, названной в каталоге проекта.

Затем создайте файл Ruby с абстракцией языка InSpec, используемой для аудита машины.

```Ruby
describe file('/tmp') do
    it { should exist }
end
```

Сохраните этот файл в `controls` новой `linux-path` папке, названной в каталоге.

Наконец, создайте конфигурацию, импортируйте модуль `ChefInSpecResource` ресурса **GuestConfiguration** и используйте ресурс для настройки имени профиля InSpec.

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
AuditFilePathExists -out ./Config
```

Команда `Node AuditFilePathExists` технически не требуется, но она `AuditFilePathExists.mof` производит файл с `localhost.mof`именем, а не по умолчанию, . После этого имя файла .mof следует конфигурации, упрощает организацию многих файлов при работе в масштабе.

Теперь вы должны иметь структуру проекта, как ниже:

```file
/ AuditFilePathExists
    / Config
        AuditFilePathExists.mof
    / linux-path
        linux-path.yml
        / controls
            linux-path.rb 
```

Вспомогательные файлы должны быть упакованы вместе. Завершенный пакет используется гостевой конфигурацией для создания определений политики Azure.

Cmdlet `New-GuestConfigurationPackage` создает пакет. Параметры `New-GuestConfigurationPackage` cmdlet при создании Linux-контента:

- **Имя**: Название пакета гостевой конфигурации.
- **Конфигурация**: Составленная конфигурация документ полный путь.
- **Путь**: Путь папки вывода. Это необязательный параметр. Если не указано, пакет создается в текущем каталоге.
- **ChefProfilePath**: Полный путь к профилю InSpec. Этот параметр поддерживается только при создании контента для аудита Linux.

Выполнить следующую команду для создания пакета с использованием конфигурации, приведенной на предыдущем этапе:

```azurepowershell-interactive
New-GuestConfigurationPackage `
  -Name 'AuditFilePathExists' `
  -Configuration './Config/AuditFilePathExists.mof'
  -ChefProfilePath './'
```

После создания пакета Configuration, но перед публикацией в Azure, вы можете протестировать пакет с рабочей станции или среды CI/CD. Cmdlet `Test-GuestConfigurationPackage` GuestConfiguration содержит тот же агент в среде разработки, что и в машинах Azure. Используя это решение, вы можете выполнить тестирование интеграции локально, прежде чем выйти в облик облачных средах.

Поскольку агент фактически оценивает локальную среду, в большинстве случаев необходимо запустить Test- cmdlet на той же платформе ОС, что и вы планируете аудит.

Параметры `Test-GuestConfigurationPackage` cmdlet:

- **Имя**: Имя политики конфигурации гостей.
- **Параметр**: Параметры политики, представленные в формате хэштабели.
- **Путь**: Полный путь пакета конфигурации гостя.

Выполнить следующую команду для тестирования пакета, созданного предыдущим шагом:

```azurepowershell-interactive
Test-GuestConfigurationPackage `
  -Path ./AuditFilePathExists.zip
```

Cmdlet также поддерживает входные данные от конвейера PowerShell. Труба выход `New-GuestConfigurationPackage` смдлета `Test-GuestConfigurationPackage` к cmdlet.

```azurepowershell-interactive
New-GuestConfigurationPackage -Name AuditFilePathExists -Configuration ./Config/AuditFilePathExists.mof -ChefProfilePath './' | Test-GuestConfigurationPackage
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
  -filePath ./AuditFilePathExists.zip `
  -blobName 'AuditFilePathExists'
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
    -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditFilePathExists.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
    -DisplayName 'Audit Linux file path.' `
    -Description 'Audit that a file path exists on a Linux machine.' `
    -Path './policies' `
    -Platform 'Linux' `
    -Version 1.0.0 `
    -Verbose
```

Следующие файлы `New-GuestConfigurationPolicy`создаются:

- **auditifNotExists.json**
- **deployifNotExists.json**
- **Initiative.json**

Выход cmdlet возвращает объект, содержащий имя отображения инициативы и путь файлов политики.

Наконец, опубликуйте `Publish-GuestConfigurationPolicy` определения политики с помощью cmdlet.
Cmdlet имеет только параметр **Пути,** который указывает на расположение `New-GuestConfigurationPolicy`файлов JSON, созданных .

Для выполнения команды Publish требуется доступ для создания политик в Azure. Конкретные требования к авторизации задокументированы на странице [обзора политики Azure.](../overview.md) Наилучшей встроенной ролью является **вкладчик политики ресурсов.**

```azurepowershell-interactive
Publish-GuestConfigurationPolicy `
  -Path '.\policyDefinitions'
```

 Cmdlet `Publish-GuestConfigurationPolicy` принимает путь от трубопровода PowerShell. Эта функция означает, что вы можете создавать файлы политики и публиковать их в одном наборе команд с трубами.

 ```azurepowershell-interactive
 New-GuestConfigurationPolicy `
  -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditFilePathExists.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
  -DisplayName 'Audit Linux file path.' `
  -Description 'Audit that a file path exists on a Linux machine.' `
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

С InSpec параметры обычно обрабатываются как входные либо во время выполнения, либо в качестве кода с использованием атрибутов. Конфигурация гостей запутывает этот процесс, поэтому при присвоении политики может быть предоставлен амв: ввода. Файл атрибутов автоматически создается внутри машины. Вам не нужно создавать и добавлять файл в проект. Есть два шага к добавлению параметров в ваш проект аудита Linux.

Определите ввод в файле Ruby, где вы сравнять сценарий, что провести аудит на машине. Приведен пример ниже.

```Ruby
attr_path = attribute('path', description: 'The file path to validate.')

describe file(attr_path) do
    it { should exist }
end
```

Cmdlets `New-GuestConfigurationPolicy` и `Test-GuestConfigurationPolicyPackage` включают параметр под названием **Параметры**. Этот параметр принимает хэшбит, включающий все сведения о каждом параметре, и автоматически создает все необходимые разделы файлов, используемых для создания каждого определения политики Azure.

Следующий пример создает определение политики для аудита пути файла, где пользователь предоставляет путь во время назначения политики.

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

Для политик Linux включите **свойство AttributesYmlContent** в конфигурацию и перезапишите значения по мере необходимости. Агент конфигурации гостей автоматически создает файл YAML, используемый InSpec для хранения атрибутов. См. пример ниже.

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

Чтобы выпустить обновление определения политики, есть два поля, которые требуют внимания.

- **Версия**: При `New-GuestConfigurationPolicy` запуске cmdlet необходимо указать номер версии, превышающее то, что в настоящее время публикуется. Свойство обновляет версию назначения конфигурации гостей, чтобы агент распознал обновленный пакет.
- **contentHash**: Это свойство `New-GuestConfigurationPolicy` обновляется автоматически cmdlet. Это хэш-значение пакета, созданного `New-GuestConfigurationPackage`. Свойство должно быть `.zip` правильным для файла, который вы публикуете. Если обновляется только свойство **contentUri,** расширение не принимает пакет содержимого.

Самый простой способ выпустить обновленный пакет — повторить процесс, описанный в этой статье, и предоставить обновленный номер версии. Этот процесс гарантирует, что все свойства были правильно обновлены.

## <a name="optional-signing-guest-configuration-packages"></a>Дополнительно: Подписание пакетов конфигурации гостей

Пользовательские политики конфигурации гостей используют хэш SHA256 для проверки пакета политики, который не изменился.
В качестве опционов клиенты могут также использовать сертификат для подписания пакетов и заставить расширение конфигурации гостей разрешить только подписанный контент.

Для включения этого сценария необходимо выполнить два шага. Запустите cmdlet, чтобы подписать пакет содержимого, и придайте тег к машинам, которые должны требовать, чтобы код был подписан.

Чтобы использовать функцию проверки `Protect-GuestConfigurationPackage` подписи, запустите cmdlet, чтобы подписать пакет, прежде чем он будет опубликован. Для этого cmdlet требуется сертификат «Подпись кода».

Параметры `Protect-GuestConfigurationPackage` cmdlet:

- **Путь**: Полный путь пакета конфигурации гостя.
- **PublicGpgKeyPath**: Общественный путь ключевой GPG. Этот параметр поддерживается только при подписании контента для Linux.

Хорошая ссылка для создания GPG ключей для использования с Linux машины обеспечивается статья на GitHub, [Генерация нового ключа GPG](https://help.github.com/en/articles/generating-a-new-gpg-key).

Агент GuestConfiguration ожидает, что открытый ключ `/usr/local/share/ca-certificates/extra` сертификата будет присутствовать на пути на машинах Linux. Для проверки подписанного содержимого узла установите открытый ключ сертификата на машине перед применением пользовательской политики. Этот процесс можно сделать с помощью любого метода внутри VM, или с помощью политики Azure. Пример шаблона [приведен здесь](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-push-certificate-windows).
Политика доступа Key Vault должна позволить поставщику ресурсов Compute получить доступ к сертификатам во время развертывания. Подробные шаги можно [узнать в управлении ресурсами Azure.](../../../virtual-machines/windows/key-vault-setup.md#use-templates-to-set-up-key-vault)

После публикации содержимого приготовьте `GuestConfigPolicyCertificateValidation` тег `enabled` с именем и значением ко всем виртуальным машинам, где требуется подписание кода. Ознакомьтесь с [примерами тегов,](../samples/built-in-policies.md#tags) как теги могут быть доставлены в масштабе с помощью политики Azure. Как только этот тег на месте, `New-GuestConfigurationPolicy` определение политики, генерируемое с помощью cmdlet, позволяет требование через расширение конфигурации гостевых гостей.

## <a name="troubleshooting-guest-configuration-policy-assignments-preview"></a>Устранение задач для просмотра задач гостевой конфигурации (Предварительный просмотр)

В предварительном просмотре доступен инструмент для оказания помощи в устранении назначений конфигурации гостевых задач Azure Policy. Инструмент находится в предварительном просмотре и был опубликован в галерее PowerShell в качестве названия модуля [Guest Configuration Troubleshooter.](https://www.powershellgallery.com/packages/GuestConfigurationTroubleshooter/)

Для получения дополнительной информации о cmdlets в этом инструменте, используйте команду Get-Help в PowerShell, чтобы показать встроенное руководство. Как инструмент получает частые обновления, что является лучшим способом получить самую самую полную информацию.

## <a name="next-steps"></a>Дальнейшие действия

- Подробнее о проверке ВМ с [конфигурацией guest](../concepts/guest-configuration.md).
- Понять, как [программно создавать политики.](programmatically-create.md)
- Узнайте, как [получить данные о соответствии.](get-compliance-data.md)
