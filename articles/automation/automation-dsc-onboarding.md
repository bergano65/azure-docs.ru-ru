---
title: Включение компьютеров для управления с помощью конфигурации состояния службы автоматизации Azure
description: Настройка компьютеров для управления с помощью конфигурации состояния службы автоматизации Azure
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.topic: conceptual
ms.date: 12/10/2019
manager: carmonm
ms.openlocfilehash: 52cd72d1144fa2acad993e927d49545d645d596f
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/09/2020
ms.locfileid: "82993739"
---
# <a name="enable-machines-for-management-by-azure-automation-state-configuration"></a>Включение компьютеров для управления с помощью конфигурации состояния службы автоматизации Azure

В этом разделе описывается, как настроить компьютеры для управления с помощью конфигурации состояния службы автоматизации Azure. Дополнительные сведения об этой службе см. в статье [Общие сведения о настройке состояния службы автоматизации Azure](automation-dsc-overview.md).

## <a name="enable-azure-vms"></a>Включение виртуальных машин Azure

Настройка состояния службы автоматизации Azure позволяет легко включить виртуальные машины Azure для управления конфигурацией с помощью портал Azure, Azure Resource Manager шаблонов или PowerShell. В этом случае, если администратору не пришлось удаленно в виртуальной машине, расширение настройки требуемого состояния виртуальной машины Azure регистрирует виртуальную машину в конфигурации состояния службы автоматизации Azure. Так как расширение Azure выполняется асинхронно, шаги по отслеживанию его хода выполнения или устранению неполадок см. в разделе [Устранение неполадок при настройке состояния на виртуальной машине](#troubleshoot-vm-setup-for-state-configuration).

> [!NOTE]
>При развертывании DSC на узле Linux используется папка **/tmp** . Модули, такие `nxautomation` как, временно загружаются для проверки перед их установкой в соответствующие расположения. Чтобы обеспечить правильную установку модулей, агенту Log Analytics для Linux требуются разрешения на чтение и запись в папке **/tmp** .<br><br>
>Агент Log Analytics для Linux запускается от имени `omsagent` пользователя. Чтобы предоставить >разрешение на запись `omsagent` пользователю, выполните команду. `setfacl -m u:omsagent:rwx /tmp`

### <a name="enable-a-vm-using-azure-portal"></a>Включение виртуальной машины с помощью портал Azure

Чтобы разрешить виртуальной машине Azure настраивать состояние с помощью [портал Azure](https://portal.azure.com/):

1. Перейдите к учетной записи службы автоматизации Azure, в которой можно включить виртуальные машины. 

2. На странице Конфигурация состояния перейдите на вкладку **узлы** и нажмите кнопку **Добавить**.

3. Выберите виртуальную машину для включения.

4. Если на компьютере не установлено расширение требуемого состояния PowerShell и работает состояние электропитания, нажмите кнопку **подключить**.

5. В разделе **Регистрация**введите [значения Local Configuration Manager PowerShell DSC](/powershell/scripting/dsc/managing-nodes/metaConfig) , необходимые для вашего варианта использования. При необходимости можно ввести конфигурацию узла для назначения виртуальной машине.

![Включение виртуальной машины](./media/automation-dsc-onboarding/DSC_Onboarding_6.png)

### <a name="enable-a-vm-using-azure-resource-manager-templates"></a>Включение виртуальной машины с помощью шаблонов Azure Resource Manager

Вы можете установить и включить виртуальную машину для настройки состояния с помощью шаблонов Azure Resource Manager. Пример шаблона, который позволяет существующей виртуальной машине настраивать состояние, см. в разделе [сервер, управляемый службой настройки требуемого состояния](https://azure.microsoft.com/resources/templates/101-automation-configuration/) . Если вы управляете масштабируемым набором виртуальных машин, см. Пример шаблона в [конфигурации масштабируемого набора виртуальных машин под управлением службы автоматизации Azure](https://azure.microsoft.com/resources/templates/201-vmss-automation-dsc/).

### <a name="enable-machines-using-powershell"></a>Включение компьютеров с помощью PowerShell

Вы можете использовать командлет [Register-азаутоматиондскноде](/powershell/module/az.automation/register-azautomationdscnode) в PowerShell, чтобы разрешить виртуальным машинам настраивать состояние. 

> [!NOTE]
>В `Register-AzAutomationDscNode` настоящее время командлет реализуется только для компьютеров под Windows, так как он запускает только расширение Windows.

### <a name="register-vms-across-azure-subscriptions"></a>Регистрация виртуальных машин в подписках Azure

Лучший способ регистрации виртуальных машин из других подписок Azure — использовать расширение DSC в шаблоне развертывания Azure Resource Manager. Примеры приведены в [расширении настройки требуемого состояния с помощью шаблонов Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/extensions/dsc-template).

Чтобы найти ключ регистрации и URL-адрес регистрации для использования в качестве параметров в шаблоне, см. раздел [Включение безопасного использования компьютеров с помощью регистрации](#enable-machines-securely-using-registration).

## <a name="enable-physicalvirtual-windows-machines"></a>Включение физических и виртуальных машин Windows

Вы можете включить серверы Windows, работающие локально или в других облачных средах (включая экземпляры AWS EC2), в конфигурацию состояния службы автоматизации Azure. Серверы должны иметь [исходящий доступ к Azure](automation-dsc-overview.md#network-planning).

1. Убедитесь, что на компьютерах установлена последняя версия [WMF 5](https://aka.ms/wmf5latest) , чтобы включить конфигурацию состояния. Кроме того, на компьютере, используемом для включения компьютеров, должен быть установлен WMF 5.
1. Следуйте указаниям в инструкции по [созданию DSC метаконфигураций](#generate-dsc-metaconfigurations) , чтобы создать папку, содержащую требуемый метаконфигураций DSC. 
1. Используйте следующий командлет, чтобы удаленно применить PowerShell DSC метаконфигураций к компьютерам для включения. 

   ```powershell
   Set-DscLocalConfigurationManager -Path C:\Users\joe\Desktop\DscMetaConfigs -ComputerName MyServer1, MyServer2
   ```

1. Если вы не можете применить PowerShell DSC метаконфигураций удаленно, скопируйте папку **метаконфигураций** на те компьютеры, которые вы включаете. Затем добавьте код для вызова [Set-DscLocalConfigurationManager](https://docs.microsoft.com/powershell/module/psdesiredstateconfiguration/set-dsclocalconfigurationmanager?view=powershell-5.1) локально на компьютерах.
1. С помощью портал Azure или командлетов убедитесь, что компьютеры отображаются как узлы конфигурации состояния, зарегистрированные в учетной записи службы автоматизации Azure.

## <a name="enable-physicalvirtual-linux-machines"></a>Включение физических и виртуальных машин Linux

Можно включить серверы Linux, работающие локально или в других облачных средах, для настройки состояния. Серверы должны иметь [исходящий доступ к Azure](automation-dsc-overview.md#network-planning).

1. Убедитесь, что на компьютерах установлена последняя версия [настройки требуемого состояния PowerShell для Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux) , чтобы включить конфигурацию состояния.
2. Если [локальная Configuration Manager POWERSHELL DSC по умолчанию](/powershell/scripting/dsc/managing-nodes/metaConfig4) соответствует Вашему варианту использования и вы хотите включить компьютеры, чтобы они как в, так и в состоянии получать данные о конфигурации состояния:

   - На каждом компьютере Linux, который необходимо включить `Register.py` , используйте для включения компьютера с локальными Configuration Manager по умолчанию PowerShell DSC.

     `/opt/microsoft/dsc/Scripts/Register.py <Automation account registration key> <Automation account registration URL>`

   - Сведения о ключе регистрации и URL-адресе регистрации для учетной записи службы автоматизации см. в статье [Включение безопасного использования компьютеров с помощью регистрации](#enable-machines-securely-using-registration).

3. Если параметры локального Configuration Manager PowerShell DSC (LCM) не соответствуют Вашему варианту использования или вы хотите разрешить компьютерам, которые сообщают только в конфигурацию состояния службы автоматизации Azure, выполните шаги 4-7. В противном случае перейдите непосредственно к шагу 7.

4. Следуйте указаниям в разделе [Создание DSC метаконфигураций](#generate-dsc-metaconfigurations) , чтобы создать папку, содержащую требуемый метаконфигураций DSC.

5. Убедитесь, что на компьютере, используемом для настройки состояния, установлена последняя версия [WMF 5](https://aka.ms/wmf5latest) .

6. Добавьте следующий код, чтобы применить PowerShell DSC метаконфигураций удаленно к компьютерам для включения.

    ```powershell
    $SecurePass = ConvertTo-SecureString -String '<root password>' -AsPlainText -Force
    $Cred = New-Object System.Management.Automation.PSCredential 'root', $SecurePass
    $Opt = New-CimSessionOption -UseSsl -SkipCACheck -SkipCNCheck -SkipRevocationCheck

    # need a CimSession for each Linux machine to onboard
    $Session = New-CimSession -Credential $Cred -ComputerName <your Linux machine> -Port 5986 -Authentication basic -SessionOption $Opt

    Set-DscLocalConfigurationManager -CimSession $Session -Path C:\Users\joe\Desktop\DscMetaConfigs
    ```

7. Если вы не можете применить PowerShell DSC метаконфигураций удаленно, скопируйте метаконфигураций, соответствующий удаленным компьютерам, из папки, описанной в шаге 4, на компьютеры Linux.

8. Добавьте код для локального `Set-DscLocalConfigurationManager.py` вызова на каждом компьютере Linux, чтобы включить настройку состояния.

   `/opt/microsoft/dsc/Scripts/SetDscLocalConfigurationManager.py -configurationmof <path to metaconfiguration file>`

9. С помощью портал Azure или командлетов убедитесь, что компьютеры, которые должны быть включены, теперь отображаются как узлы DSC, зарегистрированные в учетной записи службы автоматизации Azure.

## <a name="generate-dsc-metaconfigurations"></a>Создать метаконфигураций DSC

Чтобы разрешить любой компьютер для настройки состояния, можно создать [метаконфигурации DSC](/powershell/scripting/dsc/managing-nodes/metaConfig). Эта конфигурация сообщает агенту DSC о необходимости извлечения и/или отчета в конфигурации состояния службы автоматизации Azure. Можно создать метаконфигурации DSC для конфигурации состояния службы автоматизации Azure с помощью конфигурации DSC PowerShell или командлетов Azure Automation PowerShell.

> [!NOTE]
> DSC метаконфигураций содержит секреты, необходимые для включения компьютера в учетной записи службы автоматизации для управления. Обеспечьте должную защиту создаваемых метаконфигураций или удаляйте их сразу после использования.

Поддержка прокси-сервера для метаконфигураций осуществляется с помощью LCM, которая является подсистемой DSC Windows PowerShell. LCM выполняется на всех целевых узлах и отвечает за вызов ресурсов конфигурации, включенных в скрипт метаконфигурации DSC. Можно включить поддержку прокси-сервера в метаконфигурации, включив определения URL-адреса прокси-сервера и учетные данные прокси- `ConfigurationRepositoryWeb`сервера `ResourceRepositoryWeb`, если `ReportServerWeb` это необходимо в блоках, и. См. раздел [Настройка локального Configuration Manager](https://docs.microsoft.com/powershell/scripting/dsc/managing-nodes/metaconfig?view=powershell-7).

### <a name="generate-dsc-metaconfigurations-using-a-dsc-configuration"></a>Создание метаконфигураций DSC с помощью конфигурации DSC

1. Откройте VSCode (или ваш любимый редактор) в качестве администратора на компьютере в локальной среде. На этом компьютере должна быть установлена последняя версия [WMF 5](https://aka.ms/wmf5latest) .
1. Локально выполните следующий скрипт. Этот сценарий содержит конфигурацию PowerShell DSC для создания метаконфигураций и команду, запускающую этот процесс.

    > [!NOTE]
    > В именах конфигураций узла конфигурации состояния учитывается регистр в портал Azure. В случае несовпадения узел не отображается на вкладке **узлы** .

   ```powershell
   # The DSC configuration that will generate metaconfigurations
   [DscLocalConfigurationManager()]
   Configuration DscMetaConfigs
   {
        param
        (
            [Parameter(Mandatory=$True)]
            [String]$RegistrationUrl,

            [Parameter(Mandatory=$True)]
            [String]$RegistrationKey,

            [Parameter(Mandatory=$True)]
            [String[]]$ComputerName,

            [Int]$RefreshFrequencyMins = 30,

            [Int]$ConfigurationModeFrequencyMins = 15,

            [String]$ConfigurationMode = 'ApplyAndMonitor',

            [String]$NodeConfigurationName,

            [Boolean]$RebootNodeIfNeeded= $False,

            [String]$ActionAfterReboot = 'ContinueConfiguration',

            [Boolean]$AllowModuleOverwrite = $False,

            [Boolean]$ReportOnly
        )

        if(!$NodeConfigurationName -or $NodeConfigurationName -eq '')
        {
            $ConfigurationNames = $null
        }
        else
        {
            $ConfigurationNames = @($NodeConfigurationName)
        }

        if($ReportOnly)
        {
            $RefreshMode = 'PUSH'
        }
        else
        {
            $RefreshMode = 'PULL'
        }

        Node $ComputerName
        {
            Settings
            {
                RefreshFrequencyMins           = $RefreshFrequencyMins
                RefreshMode                    = $RefreshMode
                ConfigurationMode              = $ConfigurationMode
                AllowModuleOverwrite           = $AllowModuleOverwrite
                RebootNodeIfNeeded             = $RebootNodeIfNeeded
                ActionAfterReboot              = $ActionAfterReboot
                ConfigurationModeFrequencyMins = $ConfigurationModeFrequencyMins
            }

            if(!$ReportOnly)
            {
            ConfigurationRepositoryWeb AzureAutomationStateConfiguration
                {
                    ServerUrl          = $RegistrationUrl
                    RegistrationKey    = $RegistrationKey
                    ConfigurationNames = $ConfigurationNames
                }

                ResourceRepositoryWeb AzureAutomationStateConfiguration
                {
                    ServerUrl       = $RegistrationUrl
                    RegistrationKey = $RegistrationKey
                }
            }

            ReportServerWeb AzureAutomationStateConfiguration
            {
                ServerUrl       = $RegistrationUrl
                RegistrationKey = $RegistrationKey
            }
        }
   }

    # Create the metaconfigurations
    # NOTE: DSC Node Configuration names are case sensitive in the portal.
    # TODO: edit the below as needed for your use case
   $Params = @{
        RegistrationUrl = '<fill me in>';
        RegistrationKey = '<fill me in>';
        ComputerName = @('<some VM to onboard>', '<some other VM to onboard>');
        NodeConfigurationName = 'SimpleConfig.webserver';
        RefreshFrequencyMins = 30;
        ConfigurationModeFrequencyMins = 15;
        RebootNodeIfNeeded = $False;
        AllowModuleOverwrite = $False;
        ConfigurationMode = 'ApplyAndMonitor';
        ActionAfterReboot = 'ContinueConfiguration';
        ReportOnly = $False;  # Set to $True to have machines only report to AA DSC but not pull from it
   }

   # Use PowerShell splatting to pass parameters to the DSC configuration being invoked
   # For more info about splatting, run: Get-Help -Name about_Splatting
   DscMetaConfigs @Params
   ```

1. Введите регистрационный ключ и URL-адрес для учетной записи службы автоматизации, а также имена компьютеров, которые нужно включить. Все остальные параметры являются необязательными. Сведения о ключе регистрации и URL-адресе регистрации для учетной записи службы автоматизации см. в статье [Включение безопасного использования компьютеров с помощью регистрации](#enable-machines-securely-using-registration).

1. Если требуется, чтобы компьютеры сообщают сведения о состоянии DSC в конфигурацию состояния службы автоматизации Azure, но не извлекают конфигурацию или модули PowerShell `ReportOnly` , установите для параметра значение true.

1. Если `ReportOnly` параметр не установлен, компьютеры сообщают сведения о состоянии DSC в конфигурации состояния службы автоматизации Azure и опрашивающей конфигурации или модули PowerShell. Задайте соответствующие параметры в `ConfigurationRepositoryWeb`блоках `ResourceRepositoryWeb`, и `ReportServerWeb` .

1. Выполните скрипт. Теперь у вас должна быть Рабочая папка с именем **DscMetaConfigs**, содержащая PowerShell DSC метаконфигураций, чтобы разрешить компьютерам (от имени администратора).

    ```powershell
    Set-DscLocalConfigurationManager -Path ./DscMetaConfigs
    ```

### <a name="generate-dsc-metaconfigurations-using-azure-automation-cmdlets"></a>Создание метаконфигураций DSC с помощью командлетов службы автоматизации Azure

Если параметры по умолчанию LCM DSC PowerShell соответствуют Вашему варианту использования и вы хотите разрешить компьютерам получать данные и передавать их в конфигурацию состояния службы автоматизации Azure, вы можете создать необходимые DSC метаконфигураций более просто с помощью командлетов службы автоматизации Azure.

1. Откройте консоль PowerShell или VSCode с правами администратора на компьютере в локальной среде.
2. Подключитесь к Azure Resource Manager с помощью [Connect-азаккаунт](https://docs.microsoft.com/powershell/module/Az.Accounts/Connect-AzAccount?view=azps-3.7.0).
3. Скачайте PowerShell DSC метаконфигураций для компьютеров, которые вы хотите включить, из учетной записи службы автоматизации, в которой выполняется настройка узлов.

   ```powershell
   # Define the parameters for Get-AzAutomationDscOnboardingMetaconfig using PowerShell Splatting
   $Params = @{
       ResourceGroupName = 'ContosoResources'; # The name of the Resource Group that contains your Azure Automation account
       AutomationAccountName = 'ContosoAutomation'; # The name of the Azure Automation account where you want a node on-boarded to
       ComputerName = @('web01', 'web02', 'sql01'); # The names of the computers that the metaconfiguration will be generated for
       OutputFolder = "$env:UserProfile\Desktop\";
   }
   # Use PowerShell splatting to pass parameters to the Azure Automation cmdlet being invoked
   # For more info about splatting, run: Get-Help -Name about_Splatting
   Get-AzAutomationDscOnboardingMetaconfig @Params
   ```

1. Теперь у вас должна быть папка **DscMetaConfigs** с PowerShell DSC метаконфигураций, чтобы разрешить компьютерам (от имени администратора).

    ```powershell
    Set-DscLocalConfigurationManager -Path $env:UserProfile\Desktop\DscMetaConfigs
    ```

## <a name="enable-machines-securely-using-registration"></a>Безопасное Включение компьютеров с помощью регистрации

Вы можете безопасно включить компьютеры для учетной записи службы автоматизации Azure с помощью протокола регистрации DSC WMF 5. Этот протокол позволяет узлу DSC проходить проверку подлинности на опрашивающем сервере отчетов или в PowerShell DSC, включая настройку состояния службы автоматизации Azure. Узел регистрируется на сервере по URL-адресу регистрации и проходит проверку подлинности с помощью ключа регистрации. Во время регистрации узел DSC и запрос на вытягивание или сервер отчетов DSC согласовывают уникальный сертификат для узла, который будет использоваться для проверки подлинности после регистрации на сервере. Этот процесс предотвращает олицетворение друг друга узлов, например, если узел скомпрометирован и злонамеренно работает. После регистрации регистрационный ключ не используется для повторной проверки подлинности и удаляется из узла.

Данные, необходимые для протокола регистрации State Configuration, можно найти в подразделе **Ключи** раздела **Параметры учетной записи** на портале Azure. 

![URL-адрес и ключи службы автоматизации Azure](./media/automation-dsc-onboarding/DSC_Onboarding_4.png)

- URL-адрес регистрации — это поле URL-адреса на странице "ключи".
- Регистрационный ключ — это значение поля **первичный ключ доступа** или **вторичный ключ доступа** на странице ключи. Можно использовать любой из этих ключей.

Для повышения безопасности на странице ключи можно в любое время повторно создать первичный и вторичный ключи доступа учетной записи службы автоматизации. Повторное создание ключа предотвращает использование предыдущих ключей для регистрации последующих узлов.

## <a name="re-register-a-node"></a>Повторная регистрация узла

После регистрации компьютера в качестве узла DSC в конфигурации состояния службы автоматизации Azure существует несколько причин, по которым может потребоваться повторная регистрация этого узла в будущем.

- **Продление сертификата.** Для версий Windows Server до Windows Server 2019 каждый узел автоматически согласовывает уникальный сертификат для проверки подлинности по истечении одного года. Если срок действия сертификата истекает без продления, узел не сможет связаться со службой автоматизации Azure и помечается как `Unresponsive`. В настоящее время протокол регистрации PowerShell DSC не может автоматически обновлять сертификаты, когда срок их действия приближается к истечению, и необходимо повторно зарегистрировать эти узлы после наступления года. Перед повторной регистрацией убедитесь, что на каждом узле выполняется WMF 5 RTM. 

    Повторная регистрация в течение 90 дней с момента истечения срока действия сертификата или в любой момент после истечения срока действия сертификата приводит к формированию и применению нового сертификата. Решение этой проблемы включено в Windows Server 2019 и более поздних версий.

- **Изменения в значениях LCM DSC.** Может потребоваться изменить [значения LCM POWERSHELL DSC](/powershell/scripting/dsc/managing-nodes/metaConfig4) , заданные во время начальной регистрации узла, например `ConfigurationMode`. Сейчас эти значения агента DSC можно изменять только с помощью повторной регистрации. Единственным исключением является значение конфигурации узла, назначенное узлу. Вы можете изменить это в Azure Automation DSC напрямую.

Вы можете повторно зарегистрировать узел точно так же, как вы зарегистрировали узел изначально, используя любой из методов, описанных в этом документе. Не нужно отменять регистрацию узла в конфигурации состояния службы автоматизации Azure перед его повторной регистрацией.

## <a name="troubleshoot-vm-setup-for-state-configuration"></a>Устранение неполадок при настройке состояния виртуальной машины

Конфигурация состояния позволяет легко включить виртуальные машины Azure для управления конфигурацией. Расширение DSC для виртуальных машин Azure используется для регистрации виртуальных машин в службе "Настройка состояния службы автоматизации Azure". Так как расширение настройки требуемого состояния виртуальной машины Azure выполняется асинхронно, важно отслеживать ход выполнения и устранять неполадки.

> [!NOTE]
> Любой способ включения виртуальных машин Azure Windows в конфигурацию состояния, в которой используется расширение настройки требуемого состояния виртуальной машины Azure, может занять до часа, пока Azure Automation не покажет виртуальные машины как зарегистрированные. Эта задержка возникает из-за установки WMF 5 на виртуальной машине с помощью расширения настройки требуемого состояния виртуальной машины Azure, которое требуется для включения конфигурации состояния виртуальных машин.

Чтобы устранить неполадки или просмотреть состояние расширения настройки требуемого состояния виртуальной машины Azure, сделайте следующее:

1. В портал Azure перейдите к виртуальной машине, которая включена.
2. В разделе **Параметры**щелкните **расширения** . 
3. Теперь выберите **DSC** или **дскфорлинукс**в зависимости от операционной системы. 
4. Для получения дополнительных сведений щелкните **Просмотреть подробные сведения о состоянии**.

Дополнительные сведения об устранении неполадок см. в разделе [Устранение неполадок конфигурации состояния службы автоматизации Azure](./troubleshoot/desired-state-configuration.md).

## <a name="next-steps"></a>Дальнейшие действия

- Чтобы приступить к работе, см. статью [Приступая к работе с конфигурацией состояния службы автоматизации Azure](automation-dsc-getting-started.md).
- Дополнительные сведения о компиляции конфигураций DSC с целью назначения их целевым узлам см. [в разделе Компиляция конфигураций в конфигурации состояния службы автоматизации Azure](automation-dsc-compile.md).
- Справочник по командлетам PowerShell см. в документации по [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
).
- Сведения о ценах см. в разделе [цены на настройку состояния службы автоматизации Azure](https://azure.microsoft.com/pricing/details/automation/).
- Пример использования конфигурации состояния службы автоматизации Azure в конвейере непрерывного развертывания см. в статье [Пример использования: непрерывное развертывание на виртуальных машинах с помощью конфигурации состояния службы автоматизации Azure и шоколадного](automation-dsc-cd-chocolatey.md)процесса.
