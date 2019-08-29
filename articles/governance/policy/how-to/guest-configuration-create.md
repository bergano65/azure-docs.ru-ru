---
title: Создание политик конфигурации гостя
description: Узнайте, как создать политику гостевой конфигурации политики Azure для виртуальных машин Windows или Linux.
author: DCtheGeek
ms.author: dacoulte
ms.date: 07/26/2019
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.openlocfilehash: 12b88e14ed1d20ad26c9c8832877da08d3d98523
ms.sourcegitcommit: aaa82f3797d548c324f375b5aad5d54cb03c7288
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/29/2019
ms.locfileid: "70146125"
---
# <a name="how-to-create-guest-configuration-policies"></a>Создание политик конфигурации гостя

Конфигурация гостя использует модуль ресурсов [DSC](/powershell/dsc), чтобы создать конфигурацию для аудита виртуальных машин Azure. Конфигурация DSC определяет условие, в котором должна находиться виртуальная машина. Если при вычислении конфигурации происходит сбой, активируется **Аудит** влияния политики, а виртуальная машина считается несоответствующим.

[Гостевую конфигурацию политики Azure](/azure/governance/policy/concepts/guest-configuration) можно использовать только для аудита параметров внутри виртуальных машин. Исправление параметров в виртуальных машинах пока недоступно.

Используйте следующие действия, чтобы создать собственную конфигурацию для проверки состояния виртуальной машины Azure.

> [!IMPORTANT]
> Настраиваемые политики с гостевой конфигурацией — это предварительная версия функции.

## <a name="add-the-guestconfiguration-resource-module"></a>Добавление модуля ресурсов Гуестконфигуратион

Чтобы создать политику конфигурации гостевой системы, необходимо добавить модуль ресурсов. Этот модуль ресурсов можно использовать с локально установленным PowerShell с [Azure Cloud Shell](https://shell.azure.com)или с [образом Azure PowerShell DOCKER](https://hub.docker.com/rsdk-powershell/).

### <a name="base-requirements"></a>Основные требования

Для модуля ресурсов гостевой настройки требуется следующее программное обеспечение:

- PowerShell. Если она еще не установлена, выполните [эти инструкции](/powershell/scripting/install/installing-powershell).
- Azure PowerShell 1.5.0 или более поздней версии. Если она еще не установлена, выполните [эти инструкции](/powershell/azure/install-az-ps).

### <a name="install-the-module"></a>Установка модуля

Конфигурация гостя использует модуль ресурсов **гуестконфигуратион** для создания конфигураций DSC и их публикации в политике Azure:

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

## <a name="create-custom-guest-configuration-configuration"></a>Создание пользовательской конфигурации гостевой конфигурации

Первым шагом в создании настраиваемой политики для гостевой конфигурации является создание конфигурации DSC. Общие сведения о понятиях и терминологии DSC см. в статье [Общие сведения о POWERSHELL DSC](/powershell/dsc/overview/overview).

### <a name="custom-guest-configuration-configuration-on-linux"></a>Настраиваемая конфигурация гостевой конфигурации в Linux

Конфигурация DSC для гостевой конфигурации в Linux использует `ChefInSpecResource` ресурс для предоставления подсистеме имени определения [Chef](https://www.chef.io/inspec/) . **Имя** является единственным обязательным свойством ресурса.

В следующем примере создается конфигурация с именем **базовый план**, импортируется модуль ресурсов **гуестконфигуратион** , `ChefInSpecResource` а в качестве значения параметра Определение спецификации используется имя **Linux-Patch-Baseline**:

```azurepowershell-interactive
# Define the DSC configuration and import GuestConfiguration
Configuration baseline
{
    Import-DscResource -ModuleName 'GuestConfiguration'

    ChefInSpecResource 'Audit Linux patch baseline'
    {
        Name = 'linux-patch-baseline'
    }
}

# Compile the configuration to create the MOF files
baseline
```

Дополнительные сведения см. [в разделе запись, компиляция и применение конфигурации](/powershell/dsc/configurations/write-compile-apply-configuration).

### <a name="custom-guest-configuration-configuration-on-windows"></a>Настраиваемая конфигурация гостевой конфигурации в Windows

Конфигурация DSC политики Azure используется только гостевым агентом конфигурации, она не конфликтует с конфигурацией требуемого состояния Windows PowerShell.

В следующем примере создается конфигурация с именем **аудитбитлоккер**, импортируется модуль ресурсов **гуестконфигуратион** `Service` и используется ресурс для аудита работающей службы.

```azurepowershell-interactive
# Define the DSC configuration and import GuestConfiguration
Configuration AuditBitLocker
{
    Import-DscResource -ModuleName 'PSDscResources'

    Service 'Ensure BitLocker service is present and running'
    {
        Name = 'BDESVC'
        Ensure = 'Present'
        State = 'Running'
    }
}

# Compile the configuration to create the MOF files
AuditBitLocker
```

Дополнительные сведения см. [в разделе запись, компиляция и применение конфигурации](/powershell/dsc/configurations/write-compile-apply-configuration).

## <a name="create-guest-configuration-custom-policy-package"></a>Создать пакет настраиваемой политики гостевой конфигурации

После компиляции MOF вспомогательные файлы должны быть упакованы вместе. Завершенный пакет используется гостевой конфигурацией для создания определений политик Azure. Пакет состоит из следующих компонентов:

- Скомпилированная конфигурация DSC в MOF
- Папка для модулей
  - Модуль Гуестконфигуратион
  - Модуль Дскнативересаурцес
  - Linux Папка с определением Chef и дополнительным содержимым
  - Windows Модули ресурсов DSC, которые не встроены

`New-GuestConfigurationPackage` Командлет создает пакет. Для создания пользовательского пакета используется следующий формат:

```azurepowershell-interactive
New-GuestConfigurationPackage -Name '{PackageName}' -Configuration '{PathToMOF}' `
    -Path '{OutputFolder}' -Verbose
```

`New-GuestConfigurationPackage` Параметры командлета:

- **Имя.** Имя гостевого пакета конфигурации.
- **Конфигурация.** Полный путь к скомпилированному документу конфигурации DSC.
- **Путь**. Путь к выходной папке. Это необязательный параметр. Если этот параметр не указан, пакет создается в текущем каталоге.
- **Чефпрофилепас**: Полный путь к профилю с неполными характеристиками. Этот параметр поддерживается только при создании содержимого для аудита Linux.

Готовый пакет должен храниться в расположении, доступном для управляемых виртуальных машин. Примеры включают репозитории GitHub, репозиторий Azure или службу хранилища Azure. Если вы предпочитаете не делать этот пакет общедоступным, можно включить [маркер SAS](../../../storage/common/storage-dotnet-shared-access-signature-part-1.md) в URL-адрес. Вы также можете реализовать [конечную точку службы](../../../storage/common/storage-network-security.md#grant-access-from-a-virtual-network) для виртуальных машин в частной сети, хотя эта конфигурация применяется только к пакету и не взаимодействует со службой.

### <a name="working-with-secrets-in-guest-configuration-packages"></a>Работа с секретами в пакетах конфигурации гостей

В гостевой конфигурации политики Azure оптимальным способом управления секретами, используемыми во время выполнения, является хранение их в Azure Key Vault. Эта схема реализуется в пользовательских ресурсах DSC.

Сначала создайте управляемое пользователем удостоверение в Azure. Удостоверение используется виртуальными машинами для доступа к секретам, хранящимся в Key Vault. Подробные инструкции см. [в разделе Создание, перечисление или удаление назначенного пользователем управляемого удостоверения с помощью Azure PowerShell](../../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md).

Затем создайте экземпляр Key Vault. Подробные инструкции см. [в разделе Установка и получение секрета в PowerShell](../../../key-vault/quick-create-powershell.md).
Назначьте разрешения для экземпляра, чтобы предоставить назначенному пользователю доступ к секретам, хранящимся в Key Vault. Подробные инструкции см. [в разделе Установка и получение секретного кода — .NET](../../../key-vault/quick-create-net.md#give-the-service-principal-access-to-your-key-vault).

Затем назначьте вашему виртуальному компьютеру удостоверение, назначенное пользователем. Подробные инструкции см. [в статье Настройка управляемых удостоверений для ресурсов Azure на виртуальной машине Azure с помощью PowerShell](../../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md#user-assigned-managed-identity).
При масштабировании назначьте это удостоверение с помощью Azure Resource Manager с помощью политики Azure. Подробные инструкции см. в статье [Настройка управляемых удостоверений для ресурсов Azure на виртуальной машине Azure с помощью шаблона](../../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md#assign-a-user-assigned-managed-identity-to-an-azure-vm).

Наконец, в настраиваемом ресурсе используйте идентификатор клиента, созданный выше, для доступа к Key Vault с помощью маркера, доступного на компьютере. `client_id` и URL-адрес экземпляра Key Vault можно передать в ресурс в качестве [свойств](/powershell/dsc/resources/authoringresourcemof#creating-the-mof-schema), чтобы ресурс не обновлялся для нескольких сред или если значения необходимо изменить.

Следующий пример кода можно использовать в настраиваемом ресурсе для получения секретов из Key Vault с помощью назначенного пользователем удостоверения. Значение, возвращаемое из запроса в Key Vault, является обычным текстом. Рекомендуется хранить его в объекте учетных данных.

```azurepowershell-interactive
# the following values should be input as properties
$client_id = 'e3a78c9b-4dd2-46e1-8bfa-88c0574697ce'
$keyvault_url = 'https://keyvaultname.vault.azure.net/secrets/mysecret'

$access_token = ((Invoke-WebRequest -Uri "http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&client_id=$client_id&resource=https%3A%2F%2Fvault.azure.net" -Method GET -Headers @{Metadata='true'}).Content | ConvertFrom-Json).access_token

$value = ((Invoke-WebRequest -Uri $($keyvault_url+'?api-version=2016-10-01') -Method GET -Headers @{Authorization="Bearer $access_token"}).content | convertfrom-json).value |  ConvertTo-SecureString -asplaintext -force

$credential = New-Object System.Management.Automation.PSCredential('secret',$value)
```

## <a name="test-a-guest-configuration-package"></a>Тестирование пакета гостевой конфигурации

После создания пакета конфигурации, но перед его публикацией в Azure можно протестировать функциональность пакета с рабочей станции или среды CI/CD. Модуль гуестконфигуратион включает командлет `Test-GuestConfigurationPackage` , который загружает в среду разработки тот же агент, который используется в виртуальных машинах Azure. С помощью этого решения можно выполнить локальное тестирование интеграции перед тем, как выпустить спецификацию "тестирование/контроль качества/Рабочая среда".

```azurepowershell-interactive
Test-GuestConfigurationPackage -Path .\package\AuditWindowsService\AuditWindowsService.zip -Verbose
```

`Test-GuestConfigurationPackage` Параметры командлета:

- **Имя.** Имя политики конфигурации гостя.
- **Параметр**: Параметры политики, указанные в формате Hashtable.
- **Путь**. Полный путь к пакету конфигурации гостя.

Командлет также поддерживает входные данные из конвейера PowerShell. Передать выходные данные `New-GuestConfigurationPackage` командлета `Test-GuestConfigurationPackage` в командлет.

```azurepowershell-interactive
New-GuestConfigurationPackage -Name AuditWindowsService -Configuration .\DSCConfig\localhost.mof -Path .\package -Verbose | Test-GuestConfigurationPackage -Verbose
```

Дополнительные сведения о тестировании с параметрами см. в разделе ниже [Использование параметров в пользовательских политиках гостевой конфигурации](/azure/governance/policy/how-to/guest-configuration-create#using-parameters-in-custom-guest-configuration-policies).

## <a name="create-the-azure-policy-definition-and-initiative-deployment-files"></a>Создание файлов определения политики Azure и инициативы развертывания

После создания и отправки пакета настраиваемой политики гостевой конфигурации в расположение, доступное для виртуальных машин, создайте определение политики гостевой конфигурации для политики Azure. Командлет принимает общедоступный пакет настраиваемой политики гостевой конфигурации и создает определение политики **помощью параметров auditifnotexists** и **deployIfNotExists.** `New-GuestConfigurationPolicy` Также создается определение инициативы политики, включающее в себя оба определения политик.

В следующем примере создаются определения политики и инициативы в указанном пути из пакета настраиваемой политики гостевой конфигурации для Windows, а также имя, описание и версия.

```azurepowershell-interactive
New-GuestConfigurationPolicy
    -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditBitLocker.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
    -DisplayName 'Audit BitLocker Service.' `
    -Description 'Audit if BitLocker is not enabled on Windows machine.' `
    -Path '.\policyDefinitions' `
    -Platform 'Windows' `
    -Version 1.2.3.4 `
    -Verbose
```

`New-GuestConfigurationPolicy` Параметры командлета:

- **Контентури**: Общедоступный HTTP (s) URI пакета содержимого гостевой конфигурации.
- **Отображаемое имя**: Отображаемое имя политики.
- **Описание** Описание политики.
- **Параметр**: Параметры политики, указанные в формате Hashtable.
- **Версия.** Версия политики.
- **Путь**. Целевой путь, по которому создаются определения политик.
- **Платформа**: Целевая платформа (Windows/Linux) для политики конфигурации виртуальной машины и пакета содержимого.

Следующие файлы создаются `New-GuestConfigurationPolicy`:

- **Помощью параметров auditifnotexists. JSON**
- **deployIfNotExists. JSON**
- **Initiative. JSON**

Выходные данные командлета возвращают объект, содержащий отображаемое имя инициативы и путь к файлам политики.

Если вы хотите использовать эту команду для формирования шаблона проекта настраиваемой политики, можно внести изменения в эти файлы. В качестве примера можно изменить раздел if, чтобы определить наличие определенного тега для виртуальных машин. Дополнительные сведения о создании политик см. в разделе [программное создание политик](./programmatically-create.md).

### <a name="using-parameters-in-custom-guest-configuration-policies"></a>Использование параметров в пользовательских политиках гостевой конфигурации

Конфигурация гостя поддерживает переопределение свойств конфигурации во время выполнения. Эта функция означает, что значения в MOF-файле в пакете не обязательно должны рассматриваться как статические. Переопределяемые значения предоставляются с помощью политики Azure и не влияют на то, как создаются или компилируются конфигурации.

Командлеты `New-GuestConfigurationPolicy` и `Test-GuestConfigurationPolicyPackage` включают параметр с именем **Parameters**.
Этот параметр принимает определение Hashtable, включая все сведения о каждом параметре, и автоматически создает все необходимые разделы файлов, используемых для создания каждого определения политики Azure.

В следующем примере создается политика Azure для аудита службы, где пользователь выбирает из списка служб во время назначения политики.

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
    -Platform 'Windows' `
    -Version 1.2.3.4 `
    -Verbose
```

Для политик Linux Включите свойство `AttributesYmlContent` в конфигурацию и перепишите значения соответствующим образом. Агент конфигурации гостя автоматически создает файл YaML, используемый в параметре спец для хранения атрибутов. См. пример ниже.

```azurepowershell-interactive
Configuration FirewalldEnabled {

    Import-DscResource -ModuleName 'GuestConfiguration'

    Node FirewalldEnabled {

        ChefInSpecResource FirewalldEnabled {
            Name = 'FirewalldEnabled'
            AttributesYmlContent = "DefaultFirewalldProfile: [public]"
        }
    }
}
```

Для каждого дополнительного параметра добавьте хэш-таблицу в массив. В файлах политики вы увидите свойства, добавленные в конфигурацию гостя виртуальной машины, которая определяет тип ресурса, имя, свойство и значение.

```json
{
    "apiVersion": "2018-11-20",
    "type": "Microsoft.Compute/virtualMachines/providers/guestConfigurationAssignments",
    "name": "[concat(parameters('vmName'), '/Microsoft.GuestConfiguration/', parameters('configurationName'))]",
    "location": "[parameters('location')]",
    "properties": {
        "guestConfiguration": {
            "name": "[parameters('configurationName')]",
            "version": "1.*",
            "configurationParameter": [{
                "name": "[Service]windowsService;Name",
                "value": "[parameters('ServiceName')]"
            }]
        }
    }
}
```

## <a name="publish-to-azure-policy"></a>Опубликовать в политике Azure

Модуль ресурсов **гуестконфигуратион** предлагает способ создания определений политик и определения инициативы в Azure с одним шагом через `Publish-GuestConfigurationPolicy` командлет.
Командлет содержит только параметр **path** , указывающий на расположение трех файлов JSON, созданных `New-GuestConfigurationPolicy`.

```azurepowershell-interactive
Publish-GuestConfigurationPolicy -Path '.\policyDefinitions' -Verbose
```

`Publish-GuestConfigurationPolicy` Командлет принимает путь из конвейера PowerShell. Эта функция означает, что вы можете создать файлы политики и опубликовать их в одном наборе команд с последовательностью.

```azurepowershell-interactive
New-GuestConfigurationPolicy -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditBitLocker.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' -DisplayName 'Audit BitLocker service.' -Description 'Audit if the BitLocker service is not enabled on Windows machine.' -Path '.\policyDefinitions' -Platform 'Windows' -Version 1.2.3.4 -Verbose | ForEach-Object {$_.Path} | Publish-GuestConfigurationPolicy -Verbose
```

С помощью определений политики и инициативы, созданных в Azure, последним шагом является назначение инициативы. Узнайте, как назначить инициативу с помощью [портала](../assign-policy-portal.md), [Azure CLI](../assign-policy-azurecli.md)и [Azure PowerShell](../assign-policy-powershell.md).

> [!IMPORTANT]
> Политики конфигурации гостя **всегда** должны быть назначены с помощью инициативы, объединяющей политики _помощью параметров auditifnotexists_ и _DeployIfNotExists_ . Если назначена только политика _помощью параметров auditifnotexists_ , предварительные требования не развертываются, а политика всегда показывает, что серверы "0" соответствуют требованиям.

## <a name="policy-lifecycle"></a>Жизненный цикл политики

После публикации настраиваемой политики Azure с помощью пакета настраиваемого содержимого необходимо обновить два поля, которые должны быть обновлены, если вы хотите опубликовать новый выпуск.

- **Версия.** При запуске `New-GuestConfigurationPolicy` командлета необходимо указать номер версии, превышающий текущую публикацию.  Свойство обновляет версию назначения гостевой конфигурации в новом файле политики, чтобы расширение знало, что пакет был обновлен.
- **контенсаш**: Это свойство обновляется автоматически `New-GuestConfigurationPolicy` командлетом.  Это хэш-значение пакета, созданного `New-GuestConfigurationPackage`.  Свойство должно быть правильным для `.zip` публикуемого файла.  Если обновляется только `contentUri` свойство, например в случае, когда кто-то может изменить определение политики на портале вручную, расширение не будет принимать пакет содержимого.

Самый простой способ выпуска обновленного пакета — повторить процедуру, описанную в этой статье, и указать обновленный номер версии.
Этот процесс гарантирует, что все свойства были правильно обновлены.

## <a name="converting-windows-group-policy-content-to-azure-policy-guest-configuration"></a>Преобразование содержимого групповая политика Windows в конфигурацию гостя политики Azure

Конфигурация гостя при аудите компьютеров Windows является реализацией синтаксиса настройки требуемого состояния PowerShell.
Сообщество DSC опубликовало средства для преобразования экспортированных шаблонов групповая политика в формат DSC.
С помощью этого средства вместе с командлетами гостевой конфигурации, описанными выше, можно преобразовать содержимое Windows групповая политика и пакет или опубликовать его для политики Azure для аудита.
Дополнительные сведения об использовании этого средства см. в статье [краткое руководство. Преобразуйте групповая политика в](/powershell/dsc/quickstarts/gpo-quickstart)DSC.
После преобразования содержимого описанные выше действия по созданию пакета и его публикации в качестве политики Azure будут такими же, как и для любого содержимого DSC.

## <a name="optional-signing-guest-configuration-packages"></a>НЕОБЯЗАТЕЛЬНО. Подписывание пакетов конфигурации гостей

Пользовательские политики конфигурации гостя по умолчанию используют хэш SHA256 для проверки того, что пакет политики не изменился с момента публикации на момент, когда он был прочитан сервером, для которого выполняется аудит.
При необходимости клиенты также могут использовать сертификат для подписывания пакетов и принудительно настроить гостевой модуль настройки, разрешающий только подписанное содержимое.

Чтобы включить этот сценарий, необходимо выполнить два действия. Запустите командлет, чтобы подписать пакет содержимого, и добавьте тег к виртуальным машинам, для которых требуется подпись кода.

Чтобы использовать функцию проверки подписи, запустите `Protect-GuestConfigurationPackage` командлет, чтобы подписать пакет перед его публикацией. Для этого командлета требуется сертификат подписи кода.

```azurepowershell-interactive
$Cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object {($_.Subject-eq "CN=mycert") }
Protect-GuestConfigurationPackage -Path .\package\AuditWindowsService\AuditWindowsService.zip -Certificate $Cert -Verbose
```

`Protect-GuestConfigurationPackage` Параметры командлета:

- **Путь**. Полный путь к пакету конфигурации гостя.
- **Сертификат**: Сертификат подписи кода для подписи пакета. Этот параметр поддерживается только при подписывании содержимого для Windows.
- **Приватегпгкэйпас**: Путь к закрытому ключу GPG. Этот параметр поддерживается только при подписывании содержимого для Linux.
- **Публикгпгкэйпас**: Путь к открытому ключу GPG. Этот параметр поддерживается только при подписывании содержимого для Linux.

Агент гуестконфигуратион ждет, что открытый ключ сертификата находится в разделе "Доверенные корневые центры сертификации" на компьютерах Windows и в пути `/usr/local/share/ca-certificates/extra` на компьютерах Linux. Чтобы узел проверял подписанное содержимое, установите открытый ключ сертификата на виртуальной машине перед применением настраиваемой политики. Этот процесс можно выполнить с помощью любой методики в виртуальной машине или с помощью политики Azure. Пример шаблона [приведен здесь](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-push-certificate-windows).
Политика доступа Key Vault должна разрешить поставщику ресурсов вычислений доступ к сертификатам во время развертывания. Подробные инструкции см. [в разделе настройка Key Vault для виртуальных машин в Azure Resource Manager](../../../virtual-machines/windows/key-vault-setup.md#use-templates-to-set-up-key-vault).

Ниже приведен пример экспорта открытого ключа из сертификата подписи для импорта в виртуальную машину.

```azurepowershell-interactive
$Cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object {($_.Subject-eq "CN=mycert3") } | Select-Object -First 1
$Cert | Export-Certificate -FilePath "$env:temp\DscPublicKey.cer" -Force
```

Хороший справочник по созданию ключей GPG для использования с виртуальными машинами Linux представлен в статье на сайте GitHub с [созданием нового ключа GPG](https://help.github.com/en/articles/generating-a-new-gpg-key).

После публикации содержимого добавьте тег с именем `GuestConfigPolicyCertificateValidation` и значением `enabled` для всех виртуальных машин, где требуется подписывание кода. Этот тег можно доставлять в масштабе с помощью политики Azure. См. [тег Apply и пример значения по умолчанию](../samples/apply-tag-default-value.md) .
После создания тега определение политики, созданное с помощью `New-GuestConfigurationPolicy` командлета, обеспечивает требование с помощью модуля настройки гостевой конфигурации.

## <a name="next-steps"></a>Следующие шаги

- Узнайте, как проводить аудит виртуальных машин с помощью [гостевой конфигурации](../concepts/guest-configuration.md).
- Узнайте, как [программно создавать политики](programmatically-create.md).
- Узнайте, как [получить данные о соответствии](getting-compliance-data.md).
