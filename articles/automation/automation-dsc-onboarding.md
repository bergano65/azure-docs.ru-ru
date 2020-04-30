---
title: Подключение компьютеров для управления с помощью службы "Настройка состояния службы автоматизации Azure"
description: Настройка компьютеров для управления с помощью конфигурации состояния службы автоматизации Azure
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.topic: conceptual
ms.date: 12/10/2019
manager: carmonm
ms.openlocfilehash: d4e008a0bd43f10b01d78a1c388f1ca6fee983ab
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81457745"
---
# <a name="onboarding-machines-for-management-by-azure-automation-state-configuration"></a>Подключение компьютеров для управления с помощью службы "Настройка состояния службы автоматизации Azure"

## <a name="why-manage-machines-with-azure-automation-state-configuration"></a>В чем преимущества управления компьютерами с помощью службы "Настройка состояния службы автоматизации Azure"?

Настройка состояния службы автоматизации Azure — это служба управления конфигурацией для узлов Desired State Configuration (DSC) в любом облачном или локальном центре обработки данных. Доступ к нему осуществляется в портал Azure путем выбора **конфигурации состояния (DSC)** в разделе **Управление конфигурацией**. 

Эта служба обеспечивает возможность быстрого и простого масштабирования на тысячах компьютеров из центрального и безопасного расположения. Вы можете легко подключать компьютеры, назначать им декларативные конфигурации и просматривать отчеты, показывающие соответствие каждого компьютера указанному вами состоянию.

Служба настройки состояния службы автоматизации Azure предназначена для DSC, какие модули Runbook в службе автоматизации Azure входят в скрипты PowerShell. Другими словами, служба автоматизации Azure помогает управлять сценариями PowerShell так же, как и конфигурациями DSC. Дополнительные сведения о преимуществах использования "Настройка состояния службы автоматизации Azure" см. в статье с [обзором этой службы](automation-dsc-overview.md).

Службу "Настройка состояния службы автоматизации Azure" можно использовать для управления разными компьютерами. Их типы перечислены ниже.

- Виртуальные машины Azure
- Виртуальные машины Azure (классические).
- Физические или виртуальные машины Windows в локальной среде или в облаке, отличном от Azure (включая экземпляры AWS EC2).
- Физические или виртуальные машины под управлением Linux, расположенные локально, в Azure или облачной службе, отличной от Azure.

Если вы не готовы управлять конфигурацией компьютера из облака, можно использовать конфигурацию состояния службы автоматизации Azure в качестве конечной точки только для отчетов. Эта функция позволяет задавать (отправлять) конфигурации через DSC и просматривать сведения о отчетах в службе автоматизации Azure.

> [!NOTE]
> Управление виртуальными машинами Azure с конфигурацией состояния службы автоматизации Azure включается без дополнительной платы, если установленная версия расширения Desired State Configuration для виртуальной машины Azure превышает 2,70. Дополнительные сведения см. на [**странице с ценами на автоматизацию**](https://azure.microsoft.com/pricing/details/automation/).

В следующих разделах этой статьи показано, как можно подключить перечисленные выше компьютеры к конфигурации состояния службы автоматизации Azure.

>[!NOTE]
>Эта статья была изменена и теперь содержит сведения о новом модуле Az для Azure PowerShell. Вы по-прежнему можете использовать модуль AzureRM, исправления ошибок для которого будут продолжать выпускаться как минимум до декабря 2020 г. Дополнительные сведения о совместимости модуля Az с AzureRM см. в статье [Introducing the new Azure PowerShell Az module](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0) (Знакомство с новым модулем Az для Azure PowerShell). Инструкции по установке модуля Az в гибридной рабочей роли Runbook см. в статье об [установке модуля Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Чтобы обновить модули в учетной записи службы автоматизации, см. руководство по [обновлению модулей Azure PowerShell в службе автоматизации Azure](automation-update-azure-modules.md).

## <a name="onboarding-azure-vms"></a>Подключение виртуальных машин Azure

Настройка состояния службы автоматизации Azure позволяет легко подключать виртуальные машины Azure к управлению конфигурацией с помощью портал Azure, Azure Resource Manager шаблонов или PowerShell. В этом случае, если администратору не пришлось удаленно в виртуальной машине, расширение настройки требуемого состояния виртуальной машины Azure регистрирует виртуальную машину в конфигурации состояния службы автоматизации Azure. Так как расширение Azure выполняется асинхронно, шаги по отслеживанию его хода выполнения или устранению неполадок приведены в разделе [Устранение неполадок с подключением виртуальных машин Azure](#troubleshooting-azure-virtual-machine-onboarding) этой статьи.

> [!NOTE]
>При развертывании DSC на узле Linux используется папка **/tmp** . Модули, такие `nxautomation` как, временно загружаются для проверки перед их установкой в соответствующие расположения. Чтобы обеспечить правильную установку модулей, агенту Log Analytics для Linux требуются разрешения на чтение и запись в папке **/tmp** .<br><br>
>Агент Log Analytics для Linux запускается от имени `omsagent` пользователя. Чтобы предоставить >разрешение на запись `omsagent` пользователю, выполните команду. `setfacl -m u:omsagent:rwx /tmp`

### <a name="onboard-a-vm-using-azure-portal"></a>Подключение виртуальной машины с помощью портал Azure

Чтобы подключить виртуальную машину Azure к конфигурации состояния службы автоматизации Azure с помощью [портал Azure](https://portal.azure.com/):

1. Перейдите к учетной записи службы автоматизации Azure, в которой нужно подключить виртуальные машины. 

2. На странице Конфигурация состояния перейдите на вкладку **узлы** и нажмите кнопку **Добавить**.

3. Выберите виртуальную машину для подключения.

4. Если на компьютере не установлено расширение требуемого состояния PowerShell и работает состояние электропитания, нажмите кнопку **подключить**.

5. В разделе **Регистрация**введите [значения Local Configuration Manager PowerShell DSC](/powershell/scripting/dsc/managing-nodes/metaConfig) , необходимые для вашего варианта использования. При необходимости можно ввести конфигурацию узла для назначения виртуальной машине.

![Подключение](./media/automation-dsc-onboarding/DSC_Onboarding_6.png)

### <a name="onboard-a-vm-using-azure-resource-manager-templates"></a>Подключение виртуальной машины с помощью шаблонов Azure Resource Manager

Вы можете развернуть виртуальную машину и подключить ее к конфигурации состояния службы автоматизации Azure с помощью шаблонов Azure Resource Manager. Пример шаблона, который позволяет подключить существующую виртуальную машину к конфигурации состояния службы автоматизации Azure, см. в разделе [сервер, управляемый службой настройки требуемого состояния](https://azure.microsoft.com/resources/templates/101-automation-configuration/) . Если вы управляете масштабируемым набором виртуальных машин, см. Пример шаблона в [конфигурации масштабируемого набора виртуальных машин под управлением службы автоматизации Azure](https://azure.microsoft.com/resources/templates/201-vmss-automation-dsc/).

### <a name="onboard-machines-using-powershell"></a>Подключение компьютеров с помощью PowerShell

Вы можете использовать командлет [Register-азаутоматиондскноде](/powershell/module/az.automation/register-azautomationdscnode) в PowerShell для подключения виртуальных машин к конфигурации состояния службы автоматизации Azure. 

> [!NOTE]
>В `Register-AzAutomationDscNode` настоящее время командлет реализуется только для компьютеров под Windows, так как он запускает только расширение Windows.

### <a name="register-vms-across-azure-subscriptions"></a>Регистрация виртуальных машин в подписках Azure

Лучший способ регистрации виртуальных машин из других подписок Azure — использовать расширение DSC в шаблоне развертывания Azure Resource Manager. Примеры приведены в [расширении настройки требуемого состояния с помощью шаблонов Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/extensions/dsc-template).

Сведения о ключе регистрации и URL-адресе регистрации для использования в качестве параметров в шаблоне см. в разделе [безопасное подключение с помощью регистрации](#onboarding-securely-using-registration) этой статьи.

## <a name="onboarding-physicalvirtual-windows-machines"></a>Подключение физических и виртуальных машин Windows

Вы можете подключать серверы Windows, работающие локально или в других облачных средах (включая экземпляры AWS EC2), в конфигурацию состояния службы автоматизации Azure. Серверы должны иметь [исходящий доступ к Azure](automation-dsc-overview.md#network-planning).

1. Убедитесь, что на компьютерах установлена последняя версия [WMF 5](https://aka.ms/wmf5latest) , чтобы подключить ее к конфигурации состояния службы автоматизации Azure. Кроме того, на компьютере, используемом для операции адаптации, должен быть установлен WMF 5.
1. Следуйте указаниям в разделе [Создание DSC метаконфигураций](#generating-dsc-metaconfigurations) , чтобы создать папку, содержащую требуемый метаконфигураций DSC. 
1. Используйте следующий командлет, чтобы удаленно применить PowerShell DSC метаконфигураций к компьютерам, которые требуется подключить. 

   ```powershell
   Set-DscLocalConfigurationManager -Path C:\Users\joe\Desktop\DscMetaConfigs -ComputerName MyServer1, MyServer2
   ```

1. Если вы не можете применить PowerShell DSC метаконфигураций удаленно, скопируйте папку **метаконфигураций** на нужные компьютеры. Затем добавьте код для вызова [Set-DscLocalConfigurationManager](https://docs.microsoft.com/powershell/module/psdesiredstateconfiguration/set-dsclocalconfigurationmanager?view=powershell-5.1) локально на компьютерах.
1. С помощью портал Azure или командлетов убедитесь, что компьютеры, которые должны быть подключены, отображаются как узлы конфигурации состояния, зарегистрированные в учетной записи службы автоматизации Azure.

## <a name="onboarding-physicalvirtual-linux-machines"></a>Адаптация физических и виртуальных машин Linux

Вы можете подключить серверы Linux, работающие локально или в других облачных средах, к конфигурации состояния службы автоматизации Azure. Серверы должны иметь [исходящий доступ к Azure](automation-dsc-overview.md#network-planning).

1. Убедитесь, что на компьютерах установлена последняя версия [настройки требуемого состояния PowerShell для Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux) , чтобы подключить ее к конфигурации состояния службы автоматизации Azure.
2. Если [локальная Configuration Manager POWERSHELL DSC по умолчанию](/powershell/scripting/dsc/managing-nodes/metaConfig4) соответствует Вашему варианту использования и вы хотите подключить компьютеры, чтобы они оба запрашиваются и собирают данные в конфигурацию состояния службы автоматизации Azure:

   - На каждом компьютере Linux, чтобы подключить его к конфигурации состояния службы автоматизации Azure `Register.py` , используйте для подключения с помощью PowerShell DSC Local Configuration Manager по умолчанию.

     `/opt/microsoft/dsc/Scripts/Register.py <Automation account registration key> <Automation account registration URL>`

   - Сведения о ключе регистрации и URL-адресе регистрации для учетной записи службы автоматизации см. в разделе [безопасное подключение с помощью регистрации](#onboarding-securely-using-registration) этой статьи.

3. Если по умолчанию PowerShell DSC Local Configuration Manager (LCM) не соответствует Вашему варианту использования или вы хотите подключить компьютеры, которые сообщают только в конфигурацию состояния службы автоматизации Azure, выполните шаги 4-7. В противном случае перейдите непосредственно к шагу 7.

4. Следуйте указаниям в разделе [Создание DSC метаконфигураций](#generating-dsc-metaconfigurations) , чтобы создать папку с необходимым метаконфигураций DSC.

5. Убедитесь, что на компьютере, используемом для адаптации, установлена последняя версия [WMF 5](https://aka.ms/wmf5latest) .

6. Добавьте следующий код, чтобы применить PowerShell DSC метаконфигураций удаленно к компьютерам, которые вы хотите подключить.

    ```powershell
    $SecurePass = ConvertTo-SecureString -String '<root password>' -AsPlainText -Force
    $Cred = New-Object System.Management.Automation.PSCredential 'root', $SecurePass
    $Opt = New-CimSessionOption -UseSsl -SkipCACheck -SkipCNCheck -SkipRevocationCheck

    # need a CimSession for each Linux machine to onboard
    $Session = New-CimSession -Credential $Cred -ComputerName <your Linux machine> -Port 5986 -Authentication basic -SessionOption $Opt

    Set-DscLocalConfigurationManager -CimSession $Session -Path C:\Users\joe\Desktop\DscMetaConfigs
    ```

7. Если вы не можете применить PowerShell DSC метаконфигураций удаленно, скопируйте метаконфигураций, соответствующий удаленным компьютерам, из папки, описанной в шаге 4, на компьютеры Linux.

8. Добавьте код для локального `Set-DscLocalConfigurationManager.py` вызова на каждом компьютере Linux, чтобы подключить его к конфигурации состояния службы автоматизации Azure.

   `/opt/microsoft/dsc/Scripts/SetDscLocalConfigurationManager.py -configurationmof <path to metaconfiguration file>`

9. С помощью портал Azure или командлетов убедитесь, что компьютеры для подключения теперь отображаются как узлы DSC, зарегистрированные в вашей учетной записи службы автоматизации Azure.

## <a name="generating-dsc-metaconfigurations"></a>Создание метаконфигураций DSC

Чтобы подключить любой компьютер к конфигурации состояния службы автоматизации Azure, можно создать [метаконфигурации DSC](/powershell/scripting/dsc/managing-nodes/metaConfig). Эта конфигурация сообщает агенту DSC о необходимости извлечения и/или отчета в конфигурации состояния службы автоматизации Azure. Можно создать метаконфигурации DSC для конфигурации состояния службы автоматизации Azure с помощью конфигурации DSC PowerShell или командлетов Azure Automation PowerShell.

> [!NOTE]
> Метаконфигурации DSC содержат секретные данные, необходимые при подключении компьютера к учетной записи службы автоматизации для управления. Обеспечьте должную защиту создаваемых метаконфигураций или удаляйте их сразу после использования.

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

1. Введите регистрационный ключ и URL-адрес для учетной записи автоматизации, а также имена виртуальных машин, которые необходимо внедрить. Все остальные параметры являются необязательными. Сведения о ключе регистрации и URL-адресе регистрации для учетной записи службы автоматизации см. в разделе [безопасное подключение с использованием регистрации](#onboarding-securely-using-registration) .

1. Если требуется, чтобы компьютеры сообщают сведения о состоянии DSC в конфигурацию состояния службы автоматизации Azure, но не извлекают конфигурацию или модули PowerShell `ReportOnly` , установите для параметра значение true.

1. Если `ReportOnly` параметр не установлен, компьютеры сообщают сведения о состоянии DSC в конфигурации состояния службы автоматизации Azure и опрашивающей конфигурации или модули PowerShell. Задайте соответствующие параметры в `ConfigurationRepositoryWeb`блоках `ResourceRepositoryWeb`, и `ReportServerWeb` .

1. Выполните скрипт. Теперь у вас должна быть папка рабочего каталога с именем **DscMetaConfigs**, содержащая PowerShell DSC метаконфигураций для компьютеров для подключения (с правами администратора).

    ```powershell
    Set-DscLocalConfigurationManager -Path ./DscMetaConfigs
    ```

### <a name="generate-dsc-metaconfigurations-using-azure-automation-cmdlets"></a>Создание метаконфигураций DSC с помощью командлетов службы автоматизации Azure

Если параметры по умолчанию LCM DSC PowerShell соответствуют Вашему варианту использования и вы хотите подключить машины как из, так и из отчета в конфигурацию состояния службы автоматизации Azure, вы можете создать необходимые DSC метаконфигураций более просто с помощью командлетов службы автоматизации Azure.

1. Откройте консоль PowerShell или VSCode с правами администратора на компьютере в локальной среде.
2. Подключитесь к Azure Resource Manager с помощью [Connect-азаккаунт](https://docs.microsoft.com/powershell/module/Az.Accounts/Connect-AzAccount?view=azps-3.7.0).
3. Скачайте PowerShell DSC метаконфигураций для компьютеров, которые вы хотите подключить, из учетной записи службы автоматизации, в которой выполняется настройка узлов.

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

1. Теперь у вас должна быть папка **DscMetaConfigs** с PowerShell DSC метаконфигураций для компьютеров для подключения (с правами администратора).

    ```powershell
    Set-DscLocalConfigurationManager -Path $env:UserProfile\Desktop\DscMetaConfigs
    ```

## <a name="onboarding-securely-using-registration"></a>Безопасная адаптация с помощью регистрации

Компьютеры могут безопасно подключаться к учетной записи службы автоматизации Azure через протокол регистрации DSC WMF 5. Этот протокол позволяет узлу DSC проходить проверку подлинности на опрашивающем сервере отчетов или в PowerShell DSC, включая настройку состояния службы автоматизации Azure. Узел регистрируется на сервере по URL-адресу регистрации и проходит проверку подлинности с помощью ключа регистрации. Во время регистрации узел DSC и запрос на вытягивание или сервер отчетов DSC согласовывают уникальный сертификат для узла, который будет использоваться для проверки подлинности после регистрации на сервере. В этом случае подключенные узлы не смогут олицетворять друг друга, например, если узел скомпрометирован и злонамеренно работает. После регистрации регистрационный ключ не используется для повторной проверки подлинности и удаляется из узла.

Данные, необходимые для протокола регистрации State Configuration, можно найти в подразделе **Ключи** раздела **Параметры учетной записи** на портале Azure. 

![URL-адрес и ключи службы автоматизации Azure](./media/automation-dsc-onboarding/DSC_Onboarding_4.png)

- URL-адрес регистрации — это поле URL-адреса на странице "ключи".
- Регистрационный ключ — это значение поля **первичный ключ доступа** или **вторичный ключ доступа** на странице ключи. Можно использовать любой из этих ключей.

Для повышения безопасности на странице ключи можно в любое время повторно создать первичный и вторичный ключи доступа учетной записи службы автоматизации. Повторное создание ключа предотвращает использование предыдущих ключей для регистрации последующих узлов.

## <a name="re-registering-a-node"></a>Повторная регистрация узла

После регистрации компьютера в качестве узла DSC в конфигурации состояния службы автоматизации Azure существует несколько причин, по которым может потребоваться повторная регистрация этого узла в будущем.

- **Продление сертификата.** Для версий Windows Server до Windows Server 2019 каждый узел автоматически согласовывает уникальный сертификат для проверки подлинности по истечении одного года. Если срок действия сертификата истекает без продления, узел не сможет связаться со службой автоматизации Azure и помечается как `Unresponsive`. В настоящее время протокол регистрации PowerShell DSC не может автоматически обновлять сертификаты, когда срок их действия приближается к истечению, и необходимо повторно зарегистрировать эти узлы после наступления года. Перед повторной регистрацией убедитесь, что на каждом узле выполняется WMF 5 RTM. 

    Повторная регистрация в течение 90 дней с момента истечения срока действия сертификата или в любой момент после истечения срока действия сертификата приводит к формированию и применению нового сертификата. Решение этой проблемы включено в Windows Server 2019 и более поздних версий.

- **Изменения в значениях LCM DSC.** Может потребоваться изменить [значения LCM POWERSHELL DSC](/powershell/scripting/dsc/managing-nodes/metaConfig4) , заданные во время начальной регистрации узла, например `ConfigurationMode`. Сейчас эти значения агента DSC можно изменять только с помощью повторной регистрации. Единственным исключением является значение конфигурации узла, назначенное узлу. Вы можете изменить это в Azure Automation DSC напрямую.

Вы можете повторно зарегистрировать узел точно так же, как вы зарегистрировали узел изначально, используя любой из методов адаптации, описанных в этом документе. Не нужно отменять регистрацию узла в конфигурации состояния службы автоматизации Azure перед его повторной регистрацией.

## <a name="troubleshooting-azure-virtual-machine-onboarding"></a>Устранение неполадок при подключении виртуальной машины Azure

Служба "Настройка состояния службы автоматизации Azure" позволяет легко подключать виртуальные машины Microsoft Azure для управления конфигурациями. Расширение DSC для виртуальных машин Azure используется для регистрации виртуальных машин в службе "Настройка состояния службы автоматизации Azure". Так как расширение настройки требуемого состояния виртуальной машины Azure выполняется асинхронно, важно отслеживать ход выполнения и устранять неполадки.

> [!NOTE]
> Любой метод подключения виртуальной машины Azure Windows к конфигурации состояния службы автоматизации Azure, в которой используется расширение настройки требуемого состояния виртуальной машины Azure, может занять до часа, пока служба автоматизации Azure не отобразит его как зарегистрированный. Эта задержка возникает из-за установки WMF 5 на виртуальной машине с помощью расширения настройки требуемого состояния виртуальной машины Azure, которое требуется для подключения виртуальной машины к конфигурации состояния службы автоматизации Azure.

Чтобы устранить неполадки или просмотреть состояние расширения настройки требуемого состояния виртуальной машины Azure, сделайте следующее:

1. В портал Azure перейдите к подключенной виртуальной машине.
2. В разделе **Параметры**щелкните **расширения** . 
3. Теперь выберите **DSC** или **дскфорлинукс**в зависимости от операционной системы. 
4. Для получения дополнительных сведений щелкните **Просмотреть подробные сведения о состоянии**.

Дополнительные сведения об устранении неполадок см. в [статье Устранение неполадок с помощью настройки требуемого состояния службы автоматизации Azure (DSC)](./troubleshoot/desired-state-configuration.md).

## <a name="next-steps"></a>Дальнейшие шаги

- Чтобы приступить к работе, см. статью [Приступая к работе с конфигурацией состояния службы автоматизации Azure](automation-dsc-getting-started.md).
- Дополнительные сведения о компиляции конфигураций DSC с целью назначения их целевым узлам см. [в разделе Компиляция конфигураций в конфигурации состояния службы автоматизации Azure](automation-dsc-compile.md).
- Справочник по командлетам PowerShell см. в документации по [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
).
- Сведения о ценах см. в разделе [цены на настройку состояния службы автоматизации Azure](https://azure.microsoft.com/pricing/details/automation/).
- Пример использования конфигурации состояния службы автоматизации Azure в конвейере непрерывного развертывания см. в статье [Пример использования: непрерывное развертывание на виртуальных машинах с помощью конфигурации состояния службы автоматизации Azure и шоколадного](automation-dsc-cd-chocolatey.md)процесса.
