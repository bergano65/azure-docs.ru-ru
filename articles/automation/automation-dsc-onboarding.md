---
title: Подключение компьютеров для управления с помощью службы "Настройка состояния службы автоматизации Azure"
description: Как настроить машины для управления с помощью государственной конфигурации Azure Automation
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.topic: conceptual
ms.date: 12/10/2019
manager: carmonm
ms.openlocfilehash: c718b9a66b378044618c8c52eec3a1a498ace83c
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81383205"
---
# <a name="onboarding-machines-for-management-by-azure-automation-state-configuration"></a>Подключение компьютеров для управления с помощью службы "Настройка состояния службы автоматизации Azure"

## <a name="why-manage-machines-with-azure-automation-state-configuration"></a>В чем преимущества управления компьютерами с помощью службы "Настройка состояния службы автоматизации Azure"?

Azure Automation State Configuration — это служба управления конфигурацией для узлов «Желаемая конфигурация состояния» (DSC) в любом облачном или натерритории. Он доступен на портале Azure, выбрав **конфигурацию состояния (DSC)** под **управлением конфигурации.** 

Эта услуга обеспечивает масштабируемость на тысячах машин быстро и легко из центрального, безопасного места. Вы можете легко на борту машин, назначить им декларативные конфигурации и просматривать отчеты, показывающие соответствие каждой машины указанному состоянию.

Служба государственной конфигурации Azure Automation — это DSC, что такое runbooks Azure Automation для сценариев PowerShell. Другими словами, служба автоматизации Azure помогает управлять сценариями PowerShell так же, как и конфигурациями DSC. Дополнительные сведения о преимуществах использования "Настройка состояния службы автоматизации Azure" см. в статье с [обзором этой службы](automation-dsc-overview.md).

Службу "Настройка состояния службы автоматизации Azure" можно использовать для управления разными компьютерами. Их типы перечислены ниже.

- Виртуальные машины Azure
- Виртуальные машины Azure (классические).
- Физические/виртуальные компьютеры Windows в помещениях или в облаке, кроме Azure (включая экземпляры AWS EC2)
- Физические или виртуальные машины под управлением Linux, расположенные локально, в Azure или облачной службе, отличной от Azure.

Если вы не готовы управлять конфигурацией машины из облака, вы можете использовать конфигурацию состояния Azure Automation State в качестве конечной точки только для отчетов. Эта функция позволяет устанавливать (толкать) конфигурации через DSC и просматривать сведения о отчетности в Azure Automation.

> [!NOTE]
> Управление VMs-мизантами Azure с конфигурацией Azure Automation State включено без дополнительной платы, если установленная версия расширения Azure VM Desired State Configuration превышает 2,70. Для получения дополнительной информации [**см.**](https://azure.microsoft.com/pricing/details/automation/)

В следующих разделах этой статьи описано, как можно на борту машин, перечисленных выше, в Azure Automation State Configuration.

>[!NOTE]
>Эта статья была изменена и теперь содержит сведения о новом модуле Az для Azure PowerShell. Вы по-прежнему можете использовать модуль AzureRM, исправления ошибок для которого будут продолжать выпускаться как минимум до декабря 2020 г. Дополнительные сведения о совместимости модуля Az с AzureRM см. в статье [Introducing the new Azure PowerShell Az module](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0) (Знакомство с новым модулем Az для Azure PowerShell). Для инструкций по установке модуля Az на гибридном Runbook Worker [см.](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0) Для учетной записи Автоматизация вы можете обновить свои модули до последней версии, используя [как обновить модули Azure PowerShell в Azure Automation.](automation-update-azure-modules.md)

## <a name="onboarding-azure-vms"></a>На борту Azure VMs

Конфигурация azure Automation State Позволяет легко перемещаться на борту Проектов-мизаносцев Azure для управления конфигурацией, используя портал Azure, шаблоны управления ресурсами Azure или PowerShell. Под капотом и без удаленного в VM администратора, который должен удаленно в VM, расширение Azure VM Desired State Configuration регистрирует VM с конфигурацией Azure Automation State. Поскольку расширение Azure работает асинхронно, шаги для отслеживания его хода или устранения неполадок предоставляются в [разделе виртуальной машины Troubleshooting Azure в](#troubleshooting-azure-virtual-machine-onboarding) разделе этой статьи.

> [!NOTE]
>Развертывание DSC в узла Linux использует папку **/tmp.** Модули, `nxautomation` такие как временно загружаются для проверки перед установкой их в соответствующих местах. Чтобы убедиться, что модули правильно установят, агенту Log Analytics для Linux нужны разрешения на чтение/запись в папке **/tmp.**<br><br>
>Агент Log Analytics для Linux `omsagent` работает в качестве пользователя. Чтобы предоставить >`omsagent` написать разрешение `setfacl -m u:omsagent:rwx /tmp`пользователю, запустите команду.

### <a name="onboard-a-vm-using-azure-portal"></a>На борту VM с помощью портала Azure

Чтобы на борту Azure VM для Azure Automation State Configuration через [портал Azure:](https://portal.azure.com/)

1. Перейдите к учетной записи Azure Automation, в которой можно перемещать сяпов. 

2. На странице конфигурации состояния выберите вкладку **Узлы,** а затем нажмите **Добавить**.

3. Выберите VM на борту.

4. Если машина не имеет установлено желаемое состояние PowerShell и работает состояние питания, нажмите **Connect.**

5. При **регистрации**введите значения локальных менеджеров [конфигурации PowerShell DSC, необходимые](/powershell/scripting/dsc/managing-nodes/metaConfig) для вашего случая использования. Дополнительно можно ввести конфигурацию узла для присвоения VM.

![Подключение](./media/automation-dsc-onboarding/DSC_Onboarding_6.png)

### <a name="onboard-a-vm-using-azure-resource-manager-templates"></a>На борту VM с использованием шаблонов управления ресурсами Azure

Развертывание и развертывание Системы автоматизации Автоматизации Azure с помощью шаблонов Azure Resource Manager и на борту vM для автоматизации Azure. Смотрите [сервер, управляемый службой «Желаемая государственная конфигурация»,](https://azure.microsoft.com/resources/templates/101-automation-configuration/) для примера шаблона, который на борту существующей конфигурации состояния автоматизации Azure для автоматизации Azure. Если вы управляете набором шкалы VM, см. шаблон примера в [конфигурации набора шкалы VM, управляемой Azure Automation.](https://azure.microsoft.com/resources/templates/201-vmss-automation-dsc/)

### <a name="onboard-machines-using-powershell"></a>Бортовые машины с использованием PowerShell

Вы можете использовать смдлет [Register-AzAutomationDscNode](/powershell/module/az.automation/register-azautomationdscnode) в PowerShell для посадки на ВМ для конфигурации azure Automation State. 

> [!NOTE]
>Cmdlet `Register-AzAutomationDscNode` реализован в настоящее время только для машин под управлением Windows, так как он вызывает только расширение Windows.

### <a name="register-vms-across-azure-subscriptions"></a>Регистрация всхоза через подписки Azure

Лучший способ зарегистрировать вМ-то из других подписок Azure — использовать расширение DSC в шаблоне развертывания ресурсов Azure Manager. Примеры приведены в [расширении конфигурации «Желаемое состояние» с шаблонами Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/extensions/dsc-template)Manager.

Чтобы найти ключ регистрации и URL-адрес регистрации для использования в качестве параметров в шаблоне, смотрите в этой статье раздел [регистрации, надежно использующий](#onboarding-securely-using-registration) раздел регистрации.

## <a name="onboarding-physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azure-including-aws-ec2-instances"></a>Посадка на физические/виртуальные windows-машины в помещениях или в облаке, кроме Azure (включая экземпляры AWS EC2)

Вы можете находиться на борту серверов Windows, работающих на месте или в других облачных средах, в Azure Automation State Configuration. Серверы должны иметь [исходящий доступ к Azure](automation-dsc-overview.md#network-planning).

1. Убедитесь, что последняя версия [WMF 5](https://aka.ms/wmf5latest) установлена на машинах для посадки в государственную конфигурацию Azure Automation State Configuration. Кроме того, WMF 5 должен быть установлен на компьютере, который вы используете для борта.
1. Следуйте указаниям в разделе [Генерация метаконфигураций DSC](#generating-dsc-metaconfigurations) для создания папки, содержащей необходимые метаконфигурации DSC. 
1. Используйте следующие cmdlet для удаленного применения метаконфигураций PowerShell DSC к машинам, которые вы хотите на борту. 

   ```powershell
   Set-DscLocalConfigurationManager -Path C:\Users\joe\Desktop\DscMetaConfigs -ComputerName MyServer1, MyServer2
   ```

1. Если вы не можете применить метаконфигурации PowerShell DSC удаленно, скопируйте папку **метаконфигураций** на машины, которые вы находитесь на борту. Затем добавьте `Set-DscLocalConfigurationManager` код для локального вызова на машинах.
1. Используя портал Azure или cmdlets, убедитесь, что машины на борту отображаются как узлы конфигурации состояния, зарегистрированные в учетной записи Azure Automation.

## <a name="onboarding-physicalvirtual-linux-machines-on-premises-or-in-a-cloud-other-than-azure"></a>Посадка на физические/виртуальные машины Linux в помещениях или в облаке, кроме Azure

Вы можете находиться на борту серверов Linux, работающих на месте или в других облачных средах, в Azure Automation State Configuration. Серверы должны иметь [исходящий доступ к Azure](automation-dsc-overview.md#network-planning).

1. Убедитесь, что последняя версия [PowerShell Desired State Configuration для Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux) установлена на машинах для посадки в состояние Azure Automation.
2. Если [по умолчанию локальный менеджер конфигурации PowerShell DSC](/powershell/scripting/dsc/managing-nodes/metaConfig4) соответствует вашему примеру использования, и необходимо использовать бортовые машины, чтобы они как вытягивались, так и отчитывались перед конфигурацией состояния Azure Automation:

   - На каждой машине Linux, на борту `Register.py` которой можно зайти в состояние Azure Automation Configuration, используйте на борту с помощью по умолчанию локального менеджера конфигурации PowerShell DSC.

     `/opt/microsoft/dsc/Scripts/Register.py <Automation account registration key> <Automation account registration URL>`

   - Чтобы найти ключ регистрации и URL-адрес регистрации для учетной записи Automation, смотрите раздел [Onboarding, надежно использующий](#onboarding-securely-using-registration) раздел регистрации этой статьи.

3. Если по умолчанию по умолчанию по умолчанию PowerShell DSC Local Configuration Manager (LCM) не совпадает с вашим примером использования или вы хотите использовать бортовые машины, которые сообщают только в конфигурацию Azure Automation State, выполните шаги 4-7. В противном случае, перейдите непосредственно к шагу 7.

4. Следуйте указаниям в разделе [метаконфигураций Генерации DSC](#generating-dsc-metaconfigurations) для создания папки, содержащей необходимые метаконфигурации DSC.

5. Убедитесь, что последняя версия [WMF 5](https://aka.ms/wmf5latest) установлена на машине, используемой для посадки.

6. Добавьте код следующим образом, чтобы применять метаконфигурации PowerShell DSC удаленно к машинам, которые вы хотите на борту.

    ```powershell
    $SecurePass = ConvertTo-SecureString -String '<root password>' -AsPlainText -Force
    $Cred = New-Object System.Management.Automation.PSCredential 'root', $SecurePass
    $Opt = New-CimSessionOption -UseSsl -SkipCACheck -SkipCNCheck -SkipRevocationCheck

    # need a CimSession for each Linux machine to onboard
    $Session = New-CimSession -Credential $Cred -ComputerName <your Linux machine> -Port 5986 -Authentication basic -SessionOption $Opt

    Set-DscLocalConfigurationManager -CimSession $Session -Path C:\Users\joe\Desktop\DscMetaConfigs
    ```

7. Если вы не можете применять метаконфигурации PowerShell DSC удаленно, скопируйте метаконфигурации, соответствующие удаленным машинам, из папки, описанной в шаге 4, к машинам Linux.

8. Добавьте код `Set-DscLocalConfigurationManager.py` для локального вызова на каждой машине Linux для борта в конфигурацию состояния автоматизации Azure.

   `/opt/microsoft/dsc/Scripts/SetDscLocalConfigurationManager.py -configurationmof <path to metaconfiguration file>`

9. Используя портал Azure или cmdlets, убедитесь, что машины, которые будут на борту, теперь отображаться в качестве узлов DSC, зарегистрированных в вашей учетной записи Azure Automation.

## <a name="generating-dsc-metaconfigurations"></a>Создание метаконфигураций DSC

Для посадки на любой машине в конфигурацию состояния автоматизации Azure можно создать [метаконфигурацию DSC.](/powershell/scripting/dsc/managing-nodes/metaConfig) Эта конфигурация говорит агенту DSC вытащить из и/или отчитаться перед конфигурацией состояния автоматизации Azure. Метаконфигурация DSC для конфигурации состояния автоматизации Azure можно создать либо с помощью конфигурации PowerShell DSC, либо смдетлетов Azure Automation PowerShell.

> [!NOTE]
> Метаконфигурации DSC содержат секретные данные, необходимые при подключении компьютера к учетной записи службы автоматизации для управления. Обеспечьте должную защиту создаваемых метаконфигураций или удаляйте их сразу после использования.

Прокси-поддержка метаконфигураций контролируется LCM, который является движком Windows PowerShell DSC. LCM работает на всех целевых узлах и отвечает за вызов ресурсов конфигурации, включенных в сценарий метаконфигурации DSC. Вы можете включить поддержку прокси в метаконфигурации, включив определения прокси `ConfigurationRepositoryWeb`URL `ResourceRepositoryWeb`и `ReportServerWeb` прокси-учетных данных по мере необходимости в , и блоков. Смотрите [Настройка локального менеджера конфигурации.](https://docs.microsoft.com/powershell/scripting/dsc/managing-nodes/metaconfig?view=powershell-7)

### <a name="generate-dsc-metaconfigurations-using-a-dsc-configuration"></a>Создание метаконфигураций DSC с помощью конфигурации DSC

1. Откройте VSCode (или ваш любимый редактор) в качестве администратора на машине в локальной среде. На этом компьютере должна быть установлена последняя версия [WMF 5](https://aka.ms/wmf5latest) .
1. Локально выполните следующий скрипт. Этот сценарий содержит конфигурацию PowerShell DSC для создания метаконфигураций и команду, запускающую этот процесс.

    > [!NOTE]
    > Имена конфигураций конфигурации состояния чувствительны на портале Azure. Если дело несовместимо, узел не будет отображаться под вкладкой **узлов.**

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

1. Введите регистрационный ключ и URL-адрес для учетной записи автоматизации, а также имена виртуальных машин, которые необходимо внедрить. Все остальные параметры являются необязательными. Чтобы найти ключ регистрации и URL-адрес регистрации для учетной записи Automation, смотрите раздел [Onboarding, надежно использующий](#onboarding-securely-using-registration) раздел регистрации.

1. Если вы хотите, чтобы машины сообщали информацию о состоянии DSC в Azure Automation `ReportOnly` State Configuration, но не тянули настройки или модули PowerShell, установите параметр в истину.

1. Если `ReportOnly` машина не установлена, машины сообщают информацию о состоянии DSC в модули состояния автоматизации Azure и конфигурацию управления powerShell. Установить параметры соответственно `ConfigurationRepositoryWeb`в `ResourceRepositoryWeb`, `ReportServerWeb` и блоки.

1. Выполните скрипт. Теперь у вас должна быть рабочая папка каталога под названием **DscMetaConfigs,** содержащая метаконфигурации PowerShell DSC для машин на борту (в качестве администратора).

    ```powershell
    Set-DscLocalConfigurationManager -Path ./DscMetaConfigs
    ```

### <a name="generate-dsc-metaconfigurations-using-azure-automation-cmdlets"></a>Создание метаконфигураций DSC с помощью cmdlets Azure Automation

Если по умолчанию PowerShell DSC LCM соответствует вашему примеру использования и вы хотите, чтобы бортовые машины как вытащить из и сообщить в Azure Automation State Configuration, вы можете создать необходимые метаконфигурации DSC более просто с помощью cmdlets Azure Automation.

1. Откройте консоль PowerShell или VSCode в качестве администратора на машине в локальной среде.
2. Подключитесь к менеджеру ресурсов Azure с помощью [Connect-AzAccount](https://docs.microsoft.com/powershell/module/Az.Accounts/Connect-AzAccount?view=azps-3.7.0).
3. Скачать метаконфигурации PowerShell DSC для машин, которые вы хотите на борту, из учетной записи Автоматизации, в которой вы настраиваете узлы.

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

1. Теперь у вас должна быть папка **DscMetaConfigs,** содержащая метаконфигурации PowerShell DSC для машин на борту (в качестве администратора).

    ```powershell
    Set-DscLocalConfigurationManager -Path $env:UserProfile\Desktop\DscMetaConfigs
    ```

## <a name="onboarding-securely-using-registration"></a>Посадка безопасно с помощью регистрации

Машины могут безопасно на борту учетной записи Azure Automation через протокол регистрации WMF 5 DSC. Этот протокол позволяет узлу DSC аутентифицироваться на сервере PowerShell DSC, включая конфигурацию состояния автоматизации Azure. Узла регистрируется с сервером по URL-адресу регистрации и проверяетподлинность с помощью ключа регистрации. Во время регистрации узла DSC и сервера DSC pull/report ведут переговоры об уникальном сертификате, который узла использовать для проверки подлинности сервера после регистрации. Этот процесс предотвращает, например,, выдавая себя за узла на борту, например, если узла скомпрометирование и злонамеренное действовав. После регистрации ключ регистрации снова не используется для проверки подлинности и удаляется из узла.

Данные, необходимые для протокола регистрации State Configuration, можно найти в подразделе **Ключи** раздела **Параметры учетной записи** на портале Azure. 

![URL-адрес и ключи службы автоматизации Azure](./media/automation-dsc-onboarding/DSC_Onboarding_4.png)

- URL-адрес регистрации — это поле URL на странице Ключей.
- Ключ регистрации — это значение **поля ключей доступа Первоначального или** **поля ключа вторичного доступа** на странице Ключей. Можно использовать любой из этих ключей.

Для дополнительной безопасности можно регенерировать основные и вторичные ключи доступа учетной записи Automation в любое время на странице Ключей. Регенерация ключей предотвращает использование предыдущих ключей в будущем.

## <a name="re-registering-a-node"></a>Перерегистрация узла

После регистрации машины в качестве узла DSC в конфигурации состояния автоматизации Azure существует несколько причин, по которым в будущем может потребоваться перерегистрация этого узла.

- **Продление сертификата.** Для версий Windows Server до Windows Server 2019 каждый узла автоматически согласовывает уникальный сертификат для проверки подлинности, срок действия которого истекает через год. Если срок действия сертификата истекает без продления, узел не `Unresponsive`может связаться с Azure Automation и помечен. В настоящее время протокол регистрации PowerShell DSC не может автоматически продлевать сертификаты, когда они приближаются к истечению, и вы должны перерегистрировать узлы через год. Перед перерегистрацией убедитесь, что каждый узла работает WMF 5 RTM. 

    Перерегистрация осуществляется через 90 дней или менее со времени истечения срока действия сертификата или в любой момент после истечения срока действия сертификата, что приводит к порождению и использованию нового сертификата. Решение этой проблемы включено в Windows Server 2019 и позже.

- **Изменения значений DSC LCM.** Возможно, потребуется изменить [значения PowerShell DSC LCM,](/powershell/scripting/dsc/managing-nodes/metaConfig4) установленные, например, `ConfigurationMode`при первоначальной регистрации узла. В настоящее время изменить эти значения агента DSC можно только путем перерегистрации. Единственным исключением является значение конфигурации узлов, назначенное узлам. Вы можете изменить это непосредственно в DSC Azure Automation DSC.

Вы можете перерегистрировать узла так же, как вы зарегистрировали узла на начальном этапе, используя любой из методов посадки, описанных в этом документе. Вам не нужно отменять узел из конфигурации состояния автоматизации Azure перед его перерегистрацией.

## <a name="troubleshooting-azure-virtual-machine-onboarding"></a>Устранение неполадок при подключении виртуальной машины Azure

Служба "Настройка состояния службы автоматизации Azure" позволяет легко подключать виртуальные машины Microsoft Azure для управления конфигурациями. Расширение DSC для виртуальных машин Azure используется для регистрации виртуальных машин в службе "Настройка состояния службы автоматизации Azure". Поскольку расширение Azure VM Desired State Configuration выполняется асинхронно, отслеживание его хода и устранение неполадок может быть важным.

> [!NOTE]
> Любой метод посадки Azure Windows VM в государственную конфигурацию Azure Automation, использующем расширение azure VM Desired State Configuration, может занять до часа, чтобы Azure Automation показал ее как зарегистрированную. Эта задержка связана с установкой WMF 5 на VM расширением Azure VM Desired State Configuration, которое необходимо для посадки VM в государственную конфигурацию Azure Automation.

Для устранения неполадок или просмотра статуса расширения Azure VM Desired State Configuration:

1. На портале Azure перейдите к VM, находящемуся на борту.
2. Нажмите **на расширения** в **настройках**. 
3. Теперь выберите **DSC** или **DSCForLinux**, в зависимости от вашей операционной системы. 
4. Для получения дополнительных сведений щелкните **Просмотреть подробные сведения о состоянии**.

Для получения дополнительной информации об устранении неполадок см. [Проблемы устранения неполадок с Azure Automation Desired State Configuration (DSC).](./troubleshoot/desired-state-configuration.md)

## <a name="next-steps"></a>Следующие шаги

- Для начала см. [Начало работы с конфигурацией состояния автоматизации Azure.](automation-dsc-getting-started.md)
- Чтобы узнать о компиляции конфигураций DSC, чтобы можно было назначить их целевым узлам, см. [Конфигурации компиляции в конфигурации состояния azure Automation.](automation-dsc-compile.md)
- Для справки PowerShell cmdlet [см.](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
)
- Для получения информации о ценах на цены [см.](https://azure.microsoft.com/pricing/details/automation/)
- Например, использование конфигурации состояния azure Automation в непрерывном конвейере развертывания см. Пример [использования: непрерывное развертывание виртуальных машин с использованием конфигурации состояния Azure Automation и Chocolatey.](automation-dsc-cd-chocolatey.md)
