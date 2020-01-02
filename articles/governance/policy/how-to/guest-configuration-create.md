---
title: Создание политик конфигурации гостя
description: Узнайте, как создать политику гостевой конфигурации политики Azure для виртуальных машин Windows или Linux с Azure PowerShell.
ms.date: 11/21/2019
ms.topic: how-to
ms.openlocfilehash: d31c03f05f3a27207eb4c184b78cb531f8bb43d6
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/05/2019
ms.locfileid: "74873086"
---
# <a name="how-to-create-guest-configuration-policies"></a>Создание политик конфигурации гостя

Конфигурация гостя использует модуль ресурсов [Desired State Configuration](/powershell/scripting/dsc/overview/overview) (DSC), чтобы создать конфигурацию для аудита компьютеров Azure. Конфигурация DSC определяет условие, в котором должен находиться компьютер. Если при вычислении конфигурации произошел сбой, то активируется действие политики **помощью параметров auditifnotexists** , и компьютер **считается несовместимым.**

[Гостевую конфигурацию политики Azure](../concepts/guest-configuration.md) можно использовать только для аудита параметров внутри компьютеров. Исправление параметров в виртуальных машинах пока недоступно.

Используйте следующие действия, чтобы создать собственную конфигурацию для проверки состояния компьютера Azure.

> [!IMPORTANT]
> Настраиваемые политики с гостевой конфигурацией — это предварительная версия функции.

## <a name="add-the-guestconfiguration-resource-module"></a>Добавление модуля ресурсов Гуестконфигуратион

Чтобы создать политику конфигурации гостевой системы, необходимо добавить модуль ресурсов. Этот модуль ресурсов можно использовать с локально установленным PowerShell с [Azure Cloud Shell](https://shell.azure.com)или с [образом DOCKER Azure PowerShell Core](https://hub.docker.com/r/azuresdk/azure-powershell-core).

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

## <a name="create-custom-guest-configuration-configuration-and-resources"></a>Создание настраиваемой конфигурации гостевой конфигурации и ресурсов

Первым шагом в создании настраиваемой политики для гостевой конфигурации является создание конфигурации DSC. Общие сведения о понятиях и терминологии DSC см. в статье [Общие сведения о POWERSHELL DSC](/powershell/scripting/dsc/overview/overview).

Если для вашей конфигурации требуются только ресурсы, встроенные в установку агента гостевой конфигурации, необходимо создать только MOF-файл конфигурации. Если необходимо выполнить дополнительный скрипт, необходимо создать настраиваемый модуль ресурсов.

### <a name="requirements-for-guest-configuration-custom-resources"></a>Требования к настраиваемым ресурсам настройки гостевой системы

Когда гостевая конфигурация выполняет аудит компьютера, сначала выполняется `Test-TargetResource`, чтобы определить, находится ли он в правильном состоянии. Логическое значение, возвращаемое функцией, определяет, является ли состояние Azure Resource Manager для назначения гостя соответствующим или несоответствующим. Если логическое значение `$false` для любого ресурса в конфигурации, то поставщик будет запущен `Get-TargetResource`. Если логическое значение `$true`, то `Get-TargetResource` не вызывается.

Функция `Get-TargetResource` имеет особые требования к гостевой конфигурации, которые не требуются для настройки требуемого состояния Windows.

- Возвращаемая хэш-таблица должна включать свойство с именем **reasons**.
- Свойство reasons должно быть массивом.
- Каждый элемент в массиве должен быть хэш-таблицей с ключами с именами **Code** и **Phrase**.

Свойство reasons используется службой для стандартизации представления информации о том, что компьютер не соответствует требованиям. Каждый элемент можно представить по причинам «причина», если ресурс не соответствует требованиям. Свойство является массивом, так как ресурс может не соответствовать требованиям по более чем одной причине.

Служба ожидает **код** и **фразу** свойств. При создании настраиваемого ресурса задайте текст (обычно stdout), который будет отображаться как причина, по которой ресурс не соответствует значению **фразы**. **Код** имеет определенные требования к форматированию, поэтому отчеты могут четко отображать сведения о ресурсе, который использовался для проведения аудита. Это решение делает конфигурацию гостя расширяемой. Для аудита компьютера можно выполнить любую команду, если выходные данные могут быть захвачены и возвращены в виде строкового значения для свойства **фразы** .

- **Code** (строка): имя ресурса, повторяющееся, а затем короткое имя без пробелов в качестве идентификатора причины. Эти три значения должны быть заключены в двоеточие без пробелов.
  - Примером может быть `registry:registry:keynotpresent`
- **Phrase** (строка): понятный для человека текст, поясняющий причину несоответствия параметра.
  - Примером может быть `The registry key $key is not present on the machine.`

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

#### <a name="scaffolding-a-guest-configuration-project"></a>Формирование шаблонов проекта гостевой конфигурации

Для разработчиков, которые хотели бы ускорить процесс начала и работы с примером кода, проект сообщества с именем **проекта гостевой конфигурации** существует как шаблон для модуля [Пластер](https://github.com/powershell/plaster) PowerShell. Это средство можно использовать для формирования шаблонов проекта, включая рабочую конфигурацию и образец ресурса, а также набор [Pester](https://github.com/pester/pester) тестов для проверки проекта. Этот шаблон также включает средства выполнения задач для Visual Studio Code автоматизации создания и проверки гостевого пакета конфигурации. Дополнительные сведения см. в разделе [проект гостевой конфигурации](https://github.com/microsoft/guestconfigurationproject)проекта GitHub.

### <a name="custom-guest-configuration-configuration-on-linux"></a>Настраиваемая конфигурация гостевой конфигурации в Linux

Конфигурация DSC для гостевой конфигурации в Linux использует ресурс `ChefInSpecResource`, чтобы предоставить подсистему имя определения Chef в [спецификации](https://www.chef.io/inspec/) . **Имя** является единственным обязательным свойством ресурса.

В следующем примере создается конфигурация с именем **Baseline**, импортируется модуль ресурсов **гуестконфигуратион** , а в `ChefInSpecResource` ресурсу задается имя определения спецификации в **Linux-Patch-Baseline**:

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

Дополнительные сведения см. [в разделе запись, компиляция и применение конфигурации](/powershell/scripting/dsc/configurations/write-compile-apply-configuration).

### <a name="custom-guest-configuration-configuration-on-windows"></a>Настраиваемая конфигурация гостевой конфигурации в Windows

Конфигурация DSC политики Azure используется только гостевым агентом конфигурации, она не конфликтует с конфигурацией требуемого состояния Windows PowerShell.

В следующем примере создается конфигурация с именем **аудитбитлоккер**, импортируется модуль ресурсов **гуестконфигуратион** и используется ресурс `Service` для аудита работающей службы.

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

Дополнительные сведения см. [в разделе запись, компиляция и применение конфигурации](/powershell/scripting/dsc/configurations/write-compile-apply-configuration).

## <a name="create-guest-configuration-custom-policy-package"></a>Создать пакет настраиваемой политики гостевой конфигурации

После компиляции MOF вспомогательные файлы должны быть упакованы вместе. Завершенный пакет используется гостевой конфигурацией для создания определений политик Azure. Пакет состоит из следующих компонентов:

- Скомпилированная конфигурация DSC в MOF
- Папка для модулей
  - Модуль Гуестконфигуратион
  - Модуль Дскнативересаурцес
  - Linux Папка с определением Chef и дополнительным содержимым
  - Windows Модули ресурсов DSC, которые не встроены

Командлет `New-GuestConfigurationPackage` создает пакет. Для создания пользовательского пакета используется следующий формат:

```azurepowershell-interactive
New-GuestConfigurationPackage -Name '{PackageName}' -Configuration '{PathToMOF}' `
    -Path '{OutputFolder}' -Verbose
```

Параметры командлета `New-GuestConfigurationPackage`:

- **Имя**: имя гостевого пакета конфигурации.
- **Конфигурация**: закомпилированный полный путь к документу конфигурации DSC.
- **Путь**: путь к выходной папке. Это необязательный параметр. Если этот параметр не указан, пакет создается в текущем каталоге.
- **Чефпрофилепас**: полный путь к профилю с неполными характеристиками. Этот параметр поддерживается только при создании содержимого для аудита Linux.

Готовый пакет должен храниться в расположении, доступном для управляемых виртуальных машин. Примеры включают репозитории GitHub, репозиторий Azure или службу хранилища Azure. Если вы предпочитаете не делать этот пакет общедоступным, можно включить [маркер SAS](../../../storage/common/storage-dotnet-shared-access-signature-part-1.md) в URL-адрес.
Вы также можете реализовать [конечную точку службы](../../../storage/common/storage-network-security.md#grant-access-from-a-virtual-network) для компьютеров в частной сети, хотя эта конфигурация применяется только к пакету и не взаимодействует со службой.

### <a name="working-with-secrets-in-guest-configuration-packages"></a>Работа с секретами в пакетах конфигурации гостей

В гостевой конфигурации политики Azure оптимальным способом управления секретами, используемыми во время выполнения, является хранение их в Azure Key Vault. Эта схема реализуется в пользовательских ресурсах DSC.

1. Сначала создайте управляемое пользователем удостоверение в Azure.

   Удостоверение используется компьютерами для доступа к секретам, хранящимся в Key Vault. Подробные инструкции см. [в разделе Создание, перечисление или удаление назначенного пользователем управляемого удостоверения с помощью Azure PowerShell](../../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md).

1. Создайте экземпляр Key Vault.

   Подробные инструкции см. [в разделе Установка и получение секрета в PowerShell](../../../key-vault/quick-create-powershell.md).
   Назначьте разрешения для экземпляра, чтобы предоставить назначенному пользователю доступ к секретам, хранящимся в Key Vault. Подробные инструкции см. [в разделе Установка и получение секретного кода — .NET](../../../key-vault/quick-create-net.md#give-the-service-principal-access-to-your-key-vault).

1. Назначьте компьютеру удостоверение, назначенное пользователем.

   Подробные инструкции см. [в статье Настройка управляемых удостоверений для ресурсов Azure на виртуальной машине Azure с помощью PowerShell](../../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md#user-assigned-managed-identity).
   При масштабировании назначьте это удостоверение с помощью Azure Resource Manager с помощью политики Azure. Подробные инструкции см. в статье [Настройка управляемых удостоверений для ресурсов Azure на виртуальной машине Azure с помощью шаблона](../../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md#assign-a-user-assigned-managed-identity-to-an-azure-vm).

1. Наконец, в настраиваемом ресурсе используйте идентификатор клиента, созданный выше, для доступа к Key Vault с помощью маркера, доступного на компьютере.

   `client_id` и URL-адрес экземпляра Key Vault можно передать в ресурс в качестве [свойств](/powershell/scripting/dsc/resources/authoringresourcemof#creating-the-mof-schema), чтобы ресурс не обновлялся для нескольких сред или если значения необходимо изменить.

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

После создания пакета конфигурации, но перед его публикацией в Azure можно протестировать функциональность пакета с рабочей станции или среды CI/CD. Модуль Гуестконфигуратион включает командлет `Test-GuestConfigurationPackage`, который загружает в среду разработки тот же агент, который используется на компьютерах Azure. С помощью этого решения можно выполнить локальное тестирование интеграции перед тем, как выпустить спецификацию "тестирование/контроль качества/Рабочая среда".

```azurepowershell-interactive
Test-GuestConfigurationPackage -Path .\package\AuditWindowsService\AuditWindowsService.zip -Verbose
```

Параметры командлета `Test-GuestConfigurationPackage`:

- **Имя**: имя политики конфигурации гостя.
- **Параметр**: параметры политики, указанные в формате Hashtable.
- **Путь**: полный путь к пакету конфигурации гостя.

Командлет также поддерживает входные данные из конвейера PowerShell. Передаем выходные данные командлета `New-GuestConfigurationPackage` в командлет `Test-GuestConfigurationPackage`.

```azurepowershell-interactive
New-GuestConfigurationPackage -Name AuditWindowsService -Configuration .\DSCConfig\localhost.mof -Path .\package -Verbose | Test-GuestConfigurationPackage -Verbose
```

Дополнительные сведения о тестировании с параметрами см. в разделе ниже [Использование параметров в пользовательских политиках гостевой конфигурации](#using-parameters-in-custom-guest-configuration-policies).

## <a name="create-the-azure-policy-definition-and-initiative-deployment-files"></a>Создание файлов определения политики Azure и инициативы развертывания

После создания и отправки пакета настраиваемой политики гостевой конфигурации в расположение, доступное для компьютеров, создайте определение политики гостевой конфигурации для политики Azure. Командлет `New-GuestConfigurationPolicy` принимает общедоступный пакет настраиваемой политики гостевой конфигурации и создает определение политики **помощью параметров auditifnotexists** и **deployIfNotExists** . Также создается определение инициативы политики, включающее в себя оба определения политик.

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

Параметры командлета `New-GuestConfigurationPolicy`:

- **Контентури**: общедоступный HTTP (s) URI пакета содержимого гостевой конфигурации.
- **DisplayName**: отображаемое имя политики.
- **Описание**: описание политики.
- **Параметр**: параметры политики, указанные в формате Hashtable.
- **Версия**: версия политики.
- **Путь**: путь назначения, по которому создаются определения политик.
- **Платформа**: Целевая платформа (Windows/Linux) для политики конфигурации гостя и пакета содержимого.

Следующие файлы создаются с помощью `New-GuestConfigurationPolicy`:

- **Помощью параметров auditifnotexists. JSON**
- **deployIfNotExists. JSON**
- **Initiative. JSON**

Выходные данные командлета возвращают объект, содержащий отображаемое имя инициативы и путь к файлам политики.

Если вы хотите использовать эту команду для формирования шаблона проекта настраиваемой политики, можно внести изменения в эти файлы. В качестве примера можно изменить раздел if, чтобы определить наличие определенного тега для компьютеров. Дополнительные сведения о создании политик см. в разделе [программное создание политик](./programmatically-create.md).

### <a name="using-parameters-in-custom-guest-configuration-policies"></a>Использование параметров в пользовательских политиках гостевой конфигурации

Конфигурация гостя поддерживает переопределение свойств конфигурации во время выполнения. Эта функция означает, что значения в MOF-файле в пакете не обязательно должны рассматриваться как статические. Переопределяемые значения предоставляются с помощью политики Azure и не влияют на то, как создаются или компилируются конфигурации.

Командлеты `New-GuestConfigurationPolicy` и `Test-GuestConfigurationPolicyPackage` включают параметр с именем **Parameters**. Этот параметр принимает определение Hashtable, включая все сведения о каждом параметре, и автоматически создает все необходимые разделы файлов, используемых для создания каждого определения политики Azure.

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

Для политик Linux Включите свойство **аттрибутесимлконтент** в конфигурацию и запишите соответствующие значения. Агент конфигурации гостя автоматически создает файл YaML, используемый в параметре спец для хранения атрибутов. Ознакомьтесь с примером ниже.

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

Модуль ресурсов **гуестконфигуратион** предлагает способ создания определений политик и определения инициативы в Azure с одним шагом через командлет `Publish-GuestConfigurationPolicy`.
Командлет содержит параметр **path** , указывающий на расположение трех файлов JSON, созданных `New-GuestConfigurationPolicy`.

```azurepowershell-interactive
Publish-GuestConfigurationPolicy -Path '.\policyDefinitions' -Verbose
```

Командлет `Publish-GuestConfigurationPolicy` принимает путь из конвейера PowerShell. Эта функция означает, что вы можете создать файлы политики и опубликовать их в одном наборе команд с последовательностью.

```azurepowershell-interactive
New-GuestConfigurationPolicy -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditBitLocker.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' -DisplayName 'Audit BitLocker service.' -Description 'Audit if the BitLocker service is not enabled on Windows machine.' -Path '.\policyDefinitions' -Platform 'Windows' -Version 1.2.3.4 -Verbose | ForEach-Object {$_.Path} | Publish-GuestConfigurationPolicy -Verbose
```

С помощью определений политики и инициативы, созданных в Azure, последним шагом является назначение инициативы. Узнайте, как назначить инициативу с помощью [портала](../assign-policy-portal.md), [Azure CLI](../assign-policy-azurecli.md)и [Azure PowerShell](../assign-policy-powershell.md).

> [!IMPORTANT]
> Политики конфигурации гостя **всегда** должны быть назначены с помощью инициативы, объединяющей политики _помощью параметров auditifnotexists_ и _DeployIfNotExists_ . Если назначена только политика _помощью параметров auditifnotexists_ , предварительные требования не развертываются, а политика всегда показывает, что серверы "0" соответствуют требованиям.

## <a name="policy-lifecycle"></a>Жизненный цикл политики

После публикации настраиваемой политики Azure с помощью пакета настраиваемого содержимого необходимо обновить два поля, которые должны быть обновлены, если вы хотите опубликовать новый выпуск.

- **Версия**: при запуске командлета `New-GuestConfigurationPolicy` необходимо указать номер версии, превышающий текущую публикацию. Свойство обновляет версию назначения гостевой конфигурации в новом файле политики, чтобы расширение знало, что пакет был обновлен.
- **контенсаш**: это свойство автоматически обновляется командлетом `New-GuestConfigurationPolicy`. Это хэш-значение пакета, созданного `New-GuestConfigurationPackage`. Для публикуемого файла `.zip` свойство должно быть правильным. Если обновляется только свойство **контентури** , например в случае, когда кто-либо может изменить определение политики на портале вручную, расширение не будет принимать пакет содержимого.

Самый простой способ выпуска обновленного пакета — повторить процедуру, описанную в этой статье, и указать обновленный номер версии. Этот процесс гарантирует, что все свойства были правильно обновлены.

## <a name="converting-windows-group-policy-content-to-azure-policy-guest-configuration"></a>Преобразование содержимого групповая политика Windows в конфигурацию гостя политики Azure

Конфигурация гостя при аудите компьютеров Windows является реализацией синтаксиса настройки требуемого состояния PowerShell. Сообщество DSC опубликовало средства для преобразования экспортированных шаблонов групповая политика в формат DSC. С помощью этого средства вместе с командлетами гостевой конфигурации, описанными выше, можно преобразовать содержимое Windows групповая политика и пакет или опубликовать его для политики Azure для аудита. Дополнительные сведения об использовании этого средства см. в статье [Краткое руководство. преобразование групповая политика в DSC](/powershell/scripting/dsc/quickstarts/gpo-quickstart).
После преобразования содержимого описанные выше действия по созданию пакета и его публикации в качестве политики Azure будут такими же, как и для любого содержимого DSC.

## <a name="optional-signing-guest-configuration-packages"></a>Необязательно. Подписывание пакетов конфигурации гостей

Пользовательские политики конфигурации гостя по умолчанию используют хэш SHA256 для проверки того, что пакет политики не изменился с момента его публикации в момент, когда он был прочитан сервером, для которого выполняется аудит.
При необходимости клиенты также могут использовать сертификат для подписывания пакетов и принудительно настроить гостевой модуль настройки, разрешающий только подписанное содержимое.

Чтобы включить этот сценарий, необходимо выполнить два действия. Запустите командлет, чтобы подписать пакет содержимого, и добавьте тег на компьютеры, требующие подписания кода.

Чтобы использовать функцию проверки подписи, запустите командлет `Protect-GuestConfigurationPackage`, чтобы подписать пакет перед его публикацией. Для этого командлета требуется сертификат подписи кода.

```azurepowershell-interactive
$Cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object {($_.Subject-eq "CN=mycert") }
Protect-GuestConfigurationPackage -Path .\package\AuditWindowsService\AuditWindowsService.zip -Certificate $Cert -Verbose
```

Параметры командлета `Protect-GuestConfigurationPackage`:

- **Путь**: полный путь к пакету конфигурации гостя.
- **Сертификат**: сертификат подписи кода для подписи пакета. Этот параметр поддерживается только при подписывании содержимого для Windows.
- **Приватегпгкэйпас**: закрытый путь к ключу GPG. Этот параметр поддерживается только при подписывании содержимого для Linux.
- **Публикгпгкэйпас**: путь к открытому ключу GPG. Этот параметр поддерживается только при подписывании содержимого для Linux.

Агент Гуестконфигуратион ждет, что открытый ключ сертификата находится в разделе "Доверенные корневые центры сертификации" на компьютерах Windows и в пути `/usr/local/share/ca-certificates/extra` на компьютерах Linux. Чтобы узел проверял подписанное содержимое, установите открытый ключ сертификата на компьютере перед применением настраиваемой политики. Этот процесс можно выполнить с помощью любой методики в виртуальной машине или с помощью политики Azure. Пример шаблона [приведен здесь](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-push-certificate-windows).
Политика доступа Key Vault должна разрешить поставщику ресурсов вычислений доступ к сертификатам во время развертывания. Подробные инструкции см. [в разделе настройка Key Vault для виртуальных машин в Azure Resource Manager](../../../virtual-machines/windows/key-vault-setup.md#use-templates-to-set-up-key-vault).

Ниже приведен пример экспорта открытого ключа из сертификата подписи для импорта на компьютер.

```azurepowershell-interactive
$Cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object {($_.Subject-eq "CN=mycert3") } | Select-Object -First 1
$Cert | Export-Certificate -FilePath "$env:temp\DscPublicKey.cer" -Force
```

Хороший справочник по созданию ключей GPG для использования с компьютерами Linux приведен в статье на сайте GitHub, [создающей новый ключ GPG](https://help.github.com/en/articles/generating-a-new-gpg-key).

После публикации содержимого добавьте тег с именем `GuestConfigPolicyCertificateValidation` и значением `enabled` на все виртуальные машины, где требуется подписывание кода. Этот тег можно доставлять в масштабе с помощью политики Azure. См. [тег Apply и пример значения по умолчанию](../samples/apply-tag-default-value.md) . После создания тега определение политики, созданное с помощью командлета `New-GuestConfigurationPolicy`, обеспечивает требование с помощью модуля настройки гостевой конфигурации.

## <a name="preview-troubleshooting-guest-configuration-policy-assignments"></a>ОБРАЗЦА Устранение неполадок назначений политик гостевой конфигурации

Средство можно просмотреть в предварительной версии, чтобы помочь в устранении неполадок назначений гостевой конфигурации политики Azure. Средство находится на этапе предварительной версии и Опубликовано в коллекция PowerShell в качестве имени модуля [средство устранения неполадок конфигурации гостя](https://www.powershellgallery.com/packages/GuestConfigurationTroubleshooter/).

Для получения дополнительных сведений о командлетах в этом средстве используйте команду Get-Help в PowerShell, чтобы отобразить встроенные инструкции. Так как средство получает частые обновления, это лучший способ получить самую свежую информацию.

## <a name="next-steps"></a>Дальнейшие действия

- Узнайте, как проводить аудит виртуальных машин с помощью [гостевой конфигурации](../concepts/guest-configuration.md).
- Узнайте, как [программно создавать политики](programmatically-create.md).
- Узнайте, как [получить данные о соответствии](get-compliance-data.md).