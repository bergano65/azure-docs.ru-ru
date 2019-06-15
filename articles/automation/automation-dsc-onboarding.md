---
title: Подключение компьютеров для управления с помощью службы "Настройка состояния службы автоматизации Azure"
description: Настройка компьютеров для управления с помощью службы "Настройка состояния службы автоматизации Azure".
services: automation
ms.service: automation
ms.subservice: dsc
author: bobbytreed
ms.author: robreed
ms.topic: conceptual
ms.date: 08/08/2018
manager: carmonm
ms.openlocfilehash: 8a505e88ff92c5227d3b42da2adaf1dce58e6fbb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65441497"
---
# <a name="onboarding-machines-for-management-by-azure-automation-state-configuration"></a>Подключение компьютеров для управления с помощью службы "Настройка состояния службы автоматизации Azure"

## <a name="why-manage-machines-with-azure-automation-state-configuration"></a>В чем преимущества управления компьютерами с помощью службы "Настройка состояния службы автоматизации Azure"?

Настройки состояния службы автоматизации Azure — это служба управления конфигурации для узлов DSC в любом облаке или локально центра обработки данных.
Она обеспечивает быструю и простую масштабируемость тысяч компьютеров из безопасного центрального расположения.
Вы можете легко переносить компьютеры в облачную среду, присваивать им декларативные конфигурации, а также просматривать отчеты, отражающие соответствие каждого компьютера требуемому состоянию.
Служба настройки состояния службы автоматизации Azure — DSC, каковы модулей Runbook службы автоматизации Azure в сценариях PowerShell.
Другими словами, служба автоматизации Azure помогает управлять сценариями PowerShell так же, как и конфигурациями DSC.
Дополнительные сведения о преимуществах использования "Настройка состояния службы автоматизации Azure" см. в статье с [обзором этой службы](automation-dsc-overview.md).

Службу "Настройка состояния службы автоматизации Azure" можно использовать для управления разными компьютерами. Их типы перечислены ниже.

- Виртуальные машины Azure
- Виртуальные машины Azure (классические).
- Экземпляры EC2 Amazon Web Services (AWS).
- физические или виртуальные машины под управлением Windows, расположенные локально или в облачной службе, отличной от Azure или AWS;
- Физические или виртуальные машины под управлением Linux, расположенные локально, в Azure или облачной службе, отличной от Azure.

Кроме того, если вы не готовы управлять конфигурацией компьютера из облака, служба "Настройка состояния службы автоматизации Azure" может также использоваться как конечная точка только для отчетности.
Это позволяет задать конфигурации (отправка) через DSC и представления, передающий сведения в службе автоматизации Azure.

> [!NOTE]
> За управление виртуальными машинами Azure с помощью State Configuration не взимается дополнительная плата, если установлена версия расширения виртуальной машины DSC выше, чем 2.70. Дополнительные сведения см. на странице [**цен на службу автоматизации**](https://azure.microsoft.com/pricing/details/automation/).

В следующих разделах описываются способы подключения каждого типа компьютеров к службе "Настройка состояния службы автоматизации Azure".

## <a name="azure-virtual-machines"></a>Виртуальные машины Azure

Служба "Настройка состояния службы автоматизации Azure" позволяет легко подключать виртуальные машины Azure для управления конфигурацией с помощью портала Azure, шаблонов Azure Resource Manager или PowerShell. В процессе работы расширение DSC регистрирует виртуальную машину в службе "Настройка состояния службы автоматизации Azure", исключая необходимость выполнения администратором удаленного входа на виртуальную машину.
Так как с виртуальными машинами Azure расширение DSC работает асинхронно, можно воспользоваться алгоритмом отслеживания хода выполнения и устранения неполадок, который приведен ниже в разделе [**Устранение неполадок при подключении виртуальной машины Azure**](#troubleshooting-azure-virtual-machine-onboarding).

### <a name="azure-portal"></a>Портал Azure

На [портале Azure](https://portal.azure.com/)перейдите к учетной записи службы автоматизации Azure, чтобы подключить виртуальные машины. На странице State Configuration на вкладке **Узлы** щелкните **+ Добавить**.

Выберите виртуальную машину Azure для подключения.

Если на компьютере не установлено требуемое расширение состояний PowerShell и состояние питания запущено, щелкните **Подключиться**.

В разделе **Регистрация** введите необходимые вам [значения локального диспетчера конфигураций DSC PowerShell](/powershell/dsc/metaconfig4). Кроме того, можно ввести конфигурацию узла, которая будет назначена виртуальной машине.

![Подключение](./media/automation-dsc-onboarding/DSC_Onboarding_6.png)

### <a name="azure-resource-manager-templates"></a>Шаблоны Azure Resource Manager

Виртуальные машины Azure можно развернуть и подключить к службе "Настройка состояния службы автоматизации Azure" с помощью шаблонов Azure Resource Manager. См. в разделе [сервер под управлением службы Desired State Configuration](https://azure.microsoft.com/resources/templates/101-automation-configuration/) пример шаблона, подключающего существующую виртуальную Машину для настройки состояния службы автоматизации Azure.
Если вы управляете масштабируемого набора виртуальных машин, см. в разделе Пример шаблона [конфигурации набор масштабирования виртуальной Машины под управлением службы автоматизации Azure](https://azure.microsoft.com/resources/templates/201-vmss-automation-dsc/).

### <a name="powershell"></a>PowerShell

На портале Azure виртуальные машины можно подключать с помощью командлета [Register-AzureRmAutomationDscNode](/powershell/module/azurerm.automation/register-azurermautomationdscnode) в PowerShell.

### <a name="registering-virtual-machines-across-azure-subscriptions"></a>Регистрация виртуальных машин в подписках Azure

Для регистрации виртуальных машин из других подписок Azure рекомендуется использовать расширение DSC в шаблоне развертывания Azure Resource Manager.
Примеры приведены в [расширение Desired State Configuration с использованием шаблонов Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/extensions/dsc-template).
Чтобы найти ключ регистрации и регистрации URL-адрес для использования в качестве параметров в шаблоне, см. в следующих [ **безопасная регистрация** ](#secure-registration) раздел.

## <a name="amazon-web-services-aws-virtual-machines"></a>виртуальные машины Amazon Web Services (AWS);

Вы можете легко подключить виртуальные машины Amazon Web Services для управления конфигурацией с помощью "Настройка состояния службы автоматизации Azure", используя набор инструментов DSC AWS. Дополнительные сведения об этом наборе инструментов см. [здесь](https://blogs.msdn.microsoft.com/powershell/2016/04/20/aws-dsc-toolkit/).

## <a name="physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azureaws"></a>физические или виртуальные машины под управлением Windows, расположенные локально или в облачной службе, отличной от Azure или AWS;

Локальные серверы Windows, под управлением или в других облачных средах можно также можно подключить к настройки состояния службы автоматизации Azure, поскольку они имеют [исходящий доступ к Azure](automation-dsc-overview.md#network-planning):

1. Убедитесь, что на компьютерах, которые будут подключены к службе "Настройка состояния службы автоматизации Azure", установлена последняя версия [WMF 5](https://aka.ms/wmf5latest).
1. Создайте папку с необходимыми метаконфигурациями DSC, как указано ниже в разделе [**Создание метаконфигураций DSC**](#generating-dsc-metaconfigurations).
1. Удаленно примените метаконфигурации PowerShell DSC на компьютерах, которые нужно подключить. **Для выполнения этой команды на компьютере должна быть установлена последняя версия [WMF 5](https://aka.ms/wmf5latest)** .

   ```powershell
   Set-DscLocalConfigurationManager -Path C:\Users\joe\Desktop\DscMetaConfigs -ComputerName MyServer1, MyServer2
   ```

1. Если метаконфигурации PowerShell DSC не удалось применить удаленно, скопируйте папку метаконфигураций (см. шаг 2) на каждый компьютер, который нужно подключить. Затем локально вызовите **Set-DscLocalConfigurationManager** на каждом компьютере, который нужно подключить.
1. Используя портал Azure или командлеты, убедитесь, что все компьютеры, которые нужно подключить, теперь отображаются как узлы State Configuration, зарегистрированные в вашей учетной записи службы автоматизации Azure.

## <a name="physicalvirtual-linux-machines-on-premises-or-in-a-cloud-other-than-azure"></a>Linux, физические или виртуальные машины в локальной среде или в облаке, отличном от Azure

Для локальных серверах Linux и в других облачных средах можно также можно подключить к настройки состояния службы автоматизации Azure, поскольку они имеют [исходящий доступ к Azure](automation-dsc-overview.md#network-planning):

1. Убедитесь, что на компьютерах, которые будут подключены к службе "Настройка состояния службы автоматизации Azure", установлена последняя версия [службы настройки требуемого состояния PowerShell для Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux).
1. Если [значения по умолчанию локального диспетчера конфигураций DSC PowerShell](/powershell/dsc/metaconfig4) соответствуют требуемым, а подключаемые компьютеры должны извлекать данные из "Настройка состояния службы автоматизации Azure" **и** передают их туда, сделайте следующее:

   - На каждом компьютере под управлением Linux, который будет подключен к службе Azure Automation State Configuration, используйте файл `Register.py` для подключения с помощью значений по умолчанию локального диспетчера конфигураций DSC PowerShell:

     `/opt/microsoft/dsc/Scripts/Register.py <Automation account registration key> <Automation account registration URL>`

   - Расположение ключа и URL-адреса регистрации для учетной записи службы автоматизации см. в приведенном ниже разделе [**Безопасная регистрация**](#secure-registration).

     Если параметры по умолчанию локального диспетчера конфигураций PowerShell DSC **не** соответствуют требуемым, или хотите внедрить компьютеры таким образом, что они сообщают только для настройки состояния службы автоматизации Azure, выполните шаги 3 – 6. В противном случае сразу перейдите к шагу 6.

1. Создайте папку с необходимыми метаконфигурациями DSC, как указано ниже в разделе [**Создание метаконфигураций DSC**](#generating-dsc-metaconfigurations).
1. Удаленно примените метаконфигурации PowerShell DSC на компьютерах, которые нужно подключить:

    ```powershell
    $SecurePass = ConvertTo-SecureString -String '<root password>' -AsPlainText -Force
    $Cred = New-Object System.Management.Automation.PSCredential 'root', $SecurePass
    $Opt = New-CimSessionOption -UseSsl -SkipCACheck -SkipCNCheck -SkipRevocationCheck

    # need a CimSession for each Linux machine to onboard
    $Session = New-CimSession -Credential $Cred -ComputerName <your Linux machine> -Port 5986 -Authentication basic -SessionOption $Opt

    Set-DscLocalConfigurationManager -CimSession $Session -Path C:\Users\joe\Desktop\DscMetaConfigs
    ```

Для выполнения этой команды на компьютере должна быть установлена последняя версия [WMF 5](https://aka.ms/wmf5latest) .

1. Если не удалось применить метаконфигурации PowerShell DSC удаленно, скопируйте соответствующую из папки на шаге 5 на компьютер Linux метаконфигурации. Затем вызовите `SetDscLocalConfigurationManager.py` локально на каждом компьютере под управлением Linux, который нужно подключить к службе "Настройка состояния службы автоматизации Azure":

   `/opt/microsoft/dsc/Scripts/SetDscLocalConfigurationManager.py -configurationmof <path to metaconfiguration file>`

1. С помощью портала Azure или командлетов убедитесь, что все компьютеры, которые нужно подключить, теперь отображаются как узлы DSC, зарегистрированные в вашей учетной записи службы автоматизации Azure.

## <a name="generating-dsc-metaconfigurations"></a>Создание метаконфигураций DSC

Обычно регистрация любого компьютера в конфигурации состояния службы автоматизации Azure, [метаконфигурации DSC](/powershell/dsc/metaconfig) могут создаваться, сообщающий агента DSC для извлечения из и отчетов для настройки состояния службы автоматизации Azure. Метаконфигурации DSC для службы "Настройка состояния службы автоматизации Azure" можно создавать, используя либо конфигурацию PowerShell DSC, либо командлеты PowerShell в службе автоматизации Azure.

> [!NOTE]
> Метаконфигурации DSC содержат секретные данные, необходимые при подключении компьютера к учетной записи службы автоматизации для управления. Обеспечьте должную защиту создаваемых метаконфигураций или удаляйте их сразу после использования.

### <a name="using-a-dsc-configuration"></a>Использование конфигурации DSC

1. Запустите на компьютере, входящем в локальную среду, VSCode (или другой предпочитаемый редактор) от имени администратора. На этом компьютере должна быть установлена последняя версия [WMF 5](https://aka.ms/wmf5latest) .
1. Локально выполните следующий скрипт. Этот сценарий содержит конфигурацию PowerShell DSC для создания метаконфигураций и команду, запускающую этот процесс.

> [!NOTE]
> Имена конфигурации узла State Configuration чувствительны к регистру на портале. Если регистр не соответствует, узел не будет отображаться на вкладке **Узлы**.

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

1. Введите регистрационный ключ и URL-адрес для учетной записи автоматизации, а также имена виртуальных машин, которые необходимо внедрить. Все остальные параметры являются необязательными. Расположение ключа и URL-адреса регистрации для учетной записи службы автоматизации см. в приведенном ниже разделе [**Безопасная регистрация**](#secure-registration).
1. Если вы хотите, чтобы компьютеры передавали сведения о состоянии DSC в службу "Настройка состояния службы автоматизации Azure", не извлекая конфигурацию или модули PowerShell, присвойте для параметра **ReportOnly** значение Тrue.
1. Выполните скрипт. В рабочем каталоге появится папка **DscMetaConfigs**, содержащая метаконфигурации PowerShell DSC для подключаемых компьютеров (в качестве администратора):

    ```powershell
    Set-DscLocalConfigurationManager -Path ./DscMetaConfigs
    ```

### <a name="using-the-azure-automation-cmdlets"></a>Использование командлетов службы автоматизации Azure

Если значения по умолчанию локального диспетчера конфигураций DSC PowerShell соответствуют требуемым и вы хотите внедрить компьютеры таким образом, чтобы позволить им извлекать данные из службы "Настройка состояния службы автоматизации Azure" и передавать в эту службу отчеты, легко создать необходимые конфигурации DSC позволят командлеты службы автоматизации Azure:

1. Запустите на компьютере, входящем в локальную среду, консоль PowerShell или VSCode от имени администратора.
2. Подключитесь к Azure Resource Manager с помощью `Connect-AzureRmAccount`.
3. Из учетной записи службы автоматизации, к которой будут подключены узлы, загрузите метаконфигурации DSC PowerShell для подключаемых компьютеров:

   ```powershell
   # Define the parameters for Get-AzureRmAutomationDscOnboardingMetaconfig using PowerShell Splatting
   $Params = @{
       ResourceGroupName = 'ContosoResources'; # The name of the Resource Group that contains your Azure Automation Account
       AutomationAccountName = 'ContosoAutomation'; # The name of the Azure Automation Account where you want a node on-boarded to
       ComputerName = @('web01', 'web02', 'sql01'); # The names of the computers that the meta configuration will be generated for
       OutputFolder = "$env:UserProfile\Desktop\";
   }
   # Use PowerShell splatting to pass parameters to the Azure Automation cmdlet being invoked
   # For more info about splatting, run: Get-Help -Name about_Splatting
   Get-AzureRmAutomationDscOnboardingMetaconfig @Params
   ```

1. В рабочем каталоге появится папка ***DscMetaConfigs***, содержащая метаконфигурации PowerShell DSC для подключаемых компьютеров (в качестве администратора):

    ```powershell
    Set-DscLocalConfigurationManager -Path $env:UserProfile\Desktop\DscMetaConfigs
    ```

## <a name="secure-registration"></a>Безопасная регистрация

Компьютеры можно безопасно подключать к учетной записи службы автоматизации Azure с помощью протокола регистрации DSC WMF 5. Этот протокол позволяет узлу DSC проходить проверку подлинности на сервере отчетов или опрашивающем сервере PowerShell DSC (включая "Настройка состояния службы автоматизации Azure"). Узел регистрируется на сервере с использованием **URL-адреса регистрации**, проходя аутентификацию с помощью **Регистрационного ключа**. Во время регистрации узел DSC и сервер отчетов или опрашивающий сервер DSC создают для этого узла уникальный сертификат, который будет использоваться для проверки подлинности после регистрации на сервере. Этот процесс исключает возможность подмены подключенных узлов друг другом, например, если один из узлов взломан и является вредоносным. После регистрации регистрационный ключ повторно не используется для проверки подлинности и удаляется из узла.

Данные, необходимые для протокола регистрации State Configuration, можно найти в подразделе **Ключи** раздела **Параметры учетной записи** на портале Azure. Откройте эту колонку, щелкнув значок ключа на панели **Основные компоненты** в учетной записи службы автоматизации.

![URL-адрес и ключи службы автоматизации Azure](./media/automation-dsc-onboarding/DSC_Onboarding_4.png)

- «Регистрационный URL-адрес» — это поле «URL-адрес» в колонке «Управление ключами».
- Регистрационный ключ — это поле «Первичный ключ доступа» или «Вторичный ключ доступа» в колонке «Управление ключами». Можно использовать любой из этих ключей.

Для повышения безопасности первичный и вторичный ключи доступа учетной записи службы автоматизации можно создавать повторно в любое время (на странице **Управление ключами**). Это исключает возможность регистрации узла с помощью ранее использованных ключей.

## <a name="troubleshooting-azure-virtual-machine-onboarding"></a>Устранение неполадок при подключении виртуальной машины Azure

Служба "Настройка состояния службы автоматизации Azure" позволяет легко подключать виртуальные машины Microsoft Azure для управления конфигурациями. Расширение DSC для виртуальных машин Azure используется для регистрации виртуальных машин в службе "Настройка состояния службы автоматизации Azure". Так как с виртуальными машинами Azure расширение DSC работает асинхронно, важно отслеживать ход выполнения и устранять неполадки.

> [!NOTE]
> Независимо от способа подключения виртуальной машины Microsoft Azure к службе "Настройка состояния службы автоматизации Azure" с расширением DSC для виртуальных машин Azure, узел будет отображаться как зарегистрированный в службе автоматизации Azure приблизительно через час. Это связано с тем, что расширение DSC для виртуальных машин Azure устанавливает на виртуальную машину платформу Windows Management Framework 5.0, которая требуется для размещения этой виртуальной машины в DSC службы "Настройка состояния службы автоматизации Azure".

Чтобы устранить неполадки или просмотреть состояние расширения DSC для виртуальных машин Azure, на портале Azure перейдите к подключаемой виртуальной машине и выберите **Расширения** в разделе **Параметры**. Затем щелкните **DSC** или **DSCForLinux** в зависимости от операционной системы. Для получения дополнительных сведений щелкните **Просмотреть подробные сведения о состоянии**.

## <a name="certificate-expiration-and-reregistration"></a>Истечение срока действия сертификата и повторная регистрация

Возможно, после регистрации компьютера в качестве узла DSC в службе "Настройка состояния службы автоматизации Azure" этот узел нужно будет зарегистрировать повторно. Такая необходимость может возникнуть по ряду причин.

- После регистрации каждый узел автоматически согласовывает уникальный сертификат для проверки подлинности, срок действия которого составляет один год. В настоящее время протокол регистрации PowerShell DSC не может автоматически обновлять сертификаты при приближении даты окончания срока их действия, поэтому по истечении года необходимо повторно регистрировать узлы. Перед повторной регистрацией убедитесь, что на каждом узле работает Windows Management Framework 5.0 RTM. Если истек срок действия сертификата аутентификации узла и узел не зарегистрирован повторно, он не может взаимодействовать со службой автоматизации Azure и получает пометку "Не отвечает". Повторная регистрация, выполненная не позднее чем через 90 дней с момента истечения срока действия сертификата или в любой момент после истечения срока действия сертификата, приведет к созданию и использованию нового сертификата.
- Чтобы изменить какие-либо [значения локального диспетчера конфигураций PowerShell DSC](/powershell/dsc/metaconfig4) , заданные при первоначальной регистрации узла, например ConfigurationMode. Сейчас эти значения агента DSC можно изменить только в ходе повторной регистрации. Единственным исключением является конфигурация узла, назначенная узлу, — ее можно изменить на платформе Azure Automation DSC напрямую.

Повторная регистрация узла выполняется аналогично первоначальной, то есть с помощью любого из средств, описанных в этом документе. Перед повторной регистрацией отменять регистрацию узла в "Настройка состояния службы автоматизации Azure" не нужно.

## <a name="next-steps"></a>Дальнейшие действия

- Чтобы приступить к работе со службой "Настройка состояния службы автоматизации Azure", см. сведения в [этой статье](automation-dsc-getting-started.md).
- Сведения о компилировании конфигураций DSC, которые затем можно назначить целевым узлам, см. в статье [Компилирование конфигураций в Azure Automation DSC](automation-dsc-compile.md).
- Справочник по командлетам PowerShell для службы "Настройка состояния службы автоматизации Azure" см. в [этой статье](/powershell/module/azurerm.automation/#automation).
- Сведения о ценах см. на странице [с ценами на службу "Настройка состояния службы автоматизации Azure"](https://azure.microsoft.com/pricing/details/automation/).
- Пример использования службы "Настройка состояния службы автоматизации Azure" и Chocolatey в конвейере непрерывного развертывания см. в [этой статье](automation-dsc-cd-chocolatey.md).
