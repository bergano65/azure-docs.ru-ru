---
title: Бортовые машины для управления системой Azure Automation State Configuration
description: В этой статье описывается, как настроить машины для управления с помощью state Configuration Azure Automation.
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.topic: conceptual
ms.date: 04/15/2020
manager: carmonm
ms.openlocfilehash: 4fc9f701f4f832deb1fed34d7ac9f888521d3830
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81406205"
---
# <a name="onboard-machines-for-management-by-azure-automation-state-configuration"></a>Бортовые машины для управления системой Azure Automation State Configuration

Azure Automation State Configuration — это служба управления конфигурацией для узлов «Желаемая конфигурация состояния» (DSC) в облачных или нахождениях центров обработки данных. Вы можете получить доступ к сервису на портале Azure, выбрав **конфигурацию состояния (DSC)** под **управлением конфигурации.** 

С помощью конфигурации Azure Automation State Configuration вы можете быстро и легко достичь масштабируемости на тысячах машин из центрального безопасного места. Вы можете легко на борту машин, назначить им декларативные конфигурации и просматривать отчеты, которые показывают соответствие каждой машины указанному состоянию.

Служба государственной конфигурации Azure Automation — это DSC, что такое runbooks Azure Automation для сценариев PowerShell. Другими словами, служба автоматизации Azure помогает управлять сценариями PowerShell так же, как и конфигурациями DSC. Дополнительные сведения о преимуществах использования "Настройка состояния службы автоматизации Azure" см. в статье с [обзором этой службы](automation-dsc-overview.md).

Для управления различными машинами можно использовать конфигурацию состояния автоматизации Azure:

- Виртуальные машины Azure
- Виртуальные машины Azure (классические).
- Физические/виртуальные компьютеры Windows в помещениях или в облаке, кроме Azure (включая экземпляры Amazon Web Services (AWS) Elastic Compute Cloud (EC2)
- Физические или виртуальные машины под управлением Linux, расположенные локально, в Azure или облачной службе, отличной от Azure.

Если вы не готовы управлять конфигурацией машины из облака, можно использовать конфигурацию состояния Azure Automation state Configuration в качестве конечной точки только для отчетов. Эта функция позволяет устанавливать или толкать конфигурации через DSC и просматривать сведения о отчетности в Azure Automation.

> [!NOTE]
> Управление VMs-мизантами Azure с конфигурацией Azure Automation State включено без дополнительной платы, если установленное расширение Azure VM Desired State Configuration является версией 2.70 или позже. Для получения дополнительной информации [см.](https://azure.microsoft.com/pricing/details/automation/)

В следующих разделах этой статьи описано, как можно на борту ранее перечисленных машин в Azure Automation State Configuration.

## <a name="onboard-azure-vms"></a>Подключение виртуальных машин Azure

>[!NOTE]
>Эта статья была изменена и теперь содержит сведения о новом модуле Az для Azure PowerShell. Вы по-прежнему можете использовать модуль AzureRM, исправления ошибок для которого будут продолжать выпускаться как минимум до декабря 2020 г. Дополнительные сведения о совместимости модуля Az с AzureRM см. в статье [Introducing the new Azure PowerShell Az module](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0) (Знакомство с новым модулем Az для Azure PowerShell). Для инструкций по установке модуля Az для вашего гибридного runbook Worker [см.](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0) Для учетной записи Автоматизация вы можете обновить свои модули до последней версии, используя [модули Обновления Azure PowerShell в Azure Automation.](automation-update-azure-modules.md)

Поскольку расширение Azure работает асинхронно, мы предоставляем шаги для отслеживания его хода или устранения неполадок в [разделе виртуальной машины Troubleshoot Azure в](#troubleshoot-azure-virtual-machine-onboarding) разделе этой статьи.

> [!NOTE]
> Развертывание DSC в узла Linux использует папку */tmp.* Перед установкой `nxautomation` модули временно загружаются для проверки. Чтобы убедиться, что модули установлены правильно, агент уанализа журнала Analytics для Linux нуждается в разрешении на чтение/запись в папке */tmp.*<br><br>
> Агент Log Analytics для Linux работает как пользователь *omsagent.* Чтобы дать разрешение на запись пользователю *omsagent,* запустите команду. `setfacl -m u:omsagent:rwx /tmp`

### <a name="onboard-vms-by-using-the-azure-portal"></a>На борту ВМ с помощью портала Azure

Чтобы на борту Azure VMs для Azure Автоматизация государственной конфигурации через [портал Azure:](https://portal.azure.com/)

1. Перейдите на учетную запись Azure Automation, в которой вы хотите на борту VMs. 

1. На странице **конфигурации состояния** выберите вкладку **узлов,** а затем выберите **Добавить.**

1. Выберите VM на борту.

1. Если машина не имеет установить расширение Состояния PowerShell Desired State и работает состояние питания, выберите **Connect.**

1. При **регистрации**введите [значения PowerShell DSC Local Configuration Manager (LCM), необходимые](/powershell/scripting/dsc/managing-nodes/metaConfig) для вашего случая использования. Дополнительно можно ввести конфигурацию узла для присвоения VM.

![Панель регистрации VM](./media/automation-dsc-onboarding/DSC_Onboarding_6.png)

### <a name="onboard-vms-by-using-azure-resource-manager-templates"></a>Бортовые ВМ с помощью шаблонов управления ресурсами Azure

Развертывание и развертывание VM-системы в государственной конфигурации Azure Automation можно с помощью шаблонов Azure Resource Manager. Например, шаблон, на котором бортовая часть существующей конфигурации состояния автоматизации VM для Azure, [см. Сервер, управляемый службой желаемой государственной конфигурации.](https://azure.microsoft.com/resources/templates/101-automation-configuration/) Если вы управляете набором виртуального масштаба машины, см. шаблон примера в [конфигурации набора виртуальной шкалы машин, управляемой Azure Automation.](https://azure.microsoft.com/resources/templates/201-vmss-automation-dsc/)

### <a name="onboard-vms-by-using-powershell"></a>На борту VMs с помощью PowerShell

Вы можете использовать смдлет [Register-AzAutomationDscNode](/powershell/module/az.automation/register-azautomationdscnode) в PowerShell для посадки на ВМ для конфигурации azure Automation State. 

> [!NOTE]
>Cmdlet `Register-AzAutomationDscNode` реализован в настоящее время только для машин под управлением Windows, потому что cmdlet вызывает только расширение Windows.

### <a name="register-vms-across-azure-subscriptions"></a>Регистрация всхоза через подписки Azure

Лучший способ зарегистрировать вМ-то из других подписок Azure — использовать расширение DSC в шаблоне развертывания ресурсов Azure Manager. Примеры приведены в [расширении конфигурации «Желаемое состояние» с шаблонами Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/extensions/dsc-template)Manager.

Чтобы найти ключ регистрации и URL-адрес регистрации для использования в качестве параметров в шаблоне, [смотрите бортовой безопасно, используя](#onboard-securely-by-using-registration) раздел регистрации в этой статье.

## <a name="onboard-physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azure"></a>Бортовые физические/виртуальные компьютеры Windows в помещениях или в облаке, кроме Azure

Вы можете находиться на борту серверов Windows, работающих на месте или в других облачных средах, включая экземпляры AWS EC2, в конфигурацию состояния Azure Automation. Серверы должны иметь [исходящий доступ к Azure](automation-dsc-overview.md#network-planning).

1. Убедитесь, что последняя версия [Windows Management Framework 5](https://aka.ms/wmf5latest) установлена на машинах, которые будут находиться на борту в azure Automation State Configuration. Кроме того, Windows Management Framework 5 должна быть установлена на компьютере, который вы используете для работы на борту.
1. Чтобы создать папку, содержащую необходимые метаконфигурации DSC, следуйте указаниям в разделе [метаконфигураций Generate DSC.](#generate-dsc-metaconfigurations) 
1. Чтобы применить метаконфигурации PowerShell DSC удаленно к машинам, которые вы хотите на борту, используйте следующие cmdlet: 

   ```powershell
   Set-DscLocalConfigurationManager -Path C:\Users\joe\Desktop\DscMetaConfigs -ComputerName MyServer1, MyServer2
   ```

1. Если вы не можете применить метаконфигурации PowerShell DSC удаленно, скопируйте папку **метаконфигураций** на машины, которые вы находитесь на борту. Затем добавьте код для вызова [Set-DscLocalConfigurationManager](https://docs.microsoft.com/powershell/module/psdesiredstateconfiguration/set-dsclocalconfigurationmanager?view=powershell-5.1) локально на машинах.

1. На портале Azure или с помощью cmdlets убедитесь, что машины, которые будут на борту, отображаться в качестве узлов конфигурации состояния, зарегистрированных в учетной записи Azure Automation.

## <a name="onboard-physicalvirtual-linux-machines-on-premises-or-in-a-cloud-other-than-azure"></a>Бортовые физические/виртуальные машины Linux на месте или в облаке, кроме Azure

Вы можете находиться на борту серверов Linux, работающих на месте или в других облачных средах, в Azure Automation State Configuration. Серверы должны иметь [исходящий доступ к Azure](automation-dsc-overview.md#network-planning).

1. Убедитесь, что последняя версия [PowerShell Desired State Configuration для Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux) установлена на машинах, которые будут установлены на борту для конфигурации состояния azure Automation State.
1. Если [по умолчанию PowerShell DSC LCM](/powershell/scripting/dsc/managing-nodes/metaConfig4) совпадает с вашим примером использования, и вы хотите, чтобы бортовые машины, чтобы они как тянуть из и сообщать в Azure Автоматизация государственной конфигурации, сделать следующее:

   а. На каждой машине Linux, на борту `Register.py` которой можно зайти в состояние Azure Automation, используйте их на борту с помощью по умолчанию по умолчанию.

     `/opt/microsoft/dsc/Scripts/Register.py <Automation account registration key> <Automation account registration URL>`

   b. Чтобы найти ключ регистрации и URL-адрес регистрации для учетной записи Automation, [см. Onboard безопасно, используя](#onboard-securely-by-using-registration) раздел регистрации этой статьи.  
   c. Перейти к шагу 4.
   
1. Если по умолчанию PowerShell DSC LCM не совпадают с вашим примером использования, или если вы хотите, чтобы бортовые машины, которые сообщают только в состояние конфигурации Azure Automation, выполните последующие шаги a-d. В противном случае, перейти непосредственно к шагу d.

    а. Чтобы создать папку, содержащую необходимые метаконфигурации DSC, следуйте указаниям в разделе [метаконфигураций Generate DSC.](#generate-dsc-metaconfigurations)

    b. Убедитесь, что последняя версия [Windows Management Framework 5](https://aka.ms/wmf5latest) установлена на машине, которая используется для посадки.

    c. Чтобы удаленно применять метаконфигурации PowerShell DSC к машинам, которые необходимо на борту, добавьте следующий код:

    ```powershell
    $SecurePass = ConvertTo-SecureString -String '<root password>' -AsPlainText -Force
    $Cred = New-Object System.Management.Automation.PSCredential 'root', $SecurePass
    $Opt = New-CimSessionOption -UseSsl -SkipCACheck -SkipCNCheck -SkipRevocationCheck

    # need a CimSession for each Linux machine to onboard
    $Session = New-CimSession -Credential $Cred -ComputerName <your Linux machine> -Port 5986 -Authentication basic -SessionOption $Opt

    Set-DscLocalConfigurationManager -CimSession $Session -Path C:\Users\joe\Desktop\DscMetaConfigs
    ```

    d. Если вы не можете применить метаконфигурации PowerShell DSC удаленно, скопируйте метаконфигурации, соответствующие удаленным машинам, из папки, описанной в шаге 3a, к машинам Linux.

1. Добавьте код для вызова *Set-DscLocalConfigurationManager.py* локально на каждой машине Linux, которая будет находиться на борту в Azure Automation State Configuration.

   `/opt/microsoft/dsc/Scripts/SetDscLocalConfigurationManager.py -configurationmof <path to metaconfiguration file>`

1. Используя портал Azure или cmdlets, убедитесь, что машины, которые будут на борту, теперь отображаться в качестве узлов DSC, зарегистрированных в вашей учетной записи Azure Automation.

## <a name="generate-dsc-metaconfigurations"></a>Создание метаконфигураций DSC

Для посадки на любой машине в конфигурацию состояния автоматизации Azure можно создать [метаконфигурацию DSC.](/powershell/scripting/dsc/managing-nodes/metaConfig) Эта конфигурация говорит агенту DSC выйти из конфигурации состояния автоматизации Azure или сообщить о нем. Метаконфигурация DSC для конфигурации состояния автоматизации Azure можно создать с помощью конфигурации PowerShell DSC или cmdlets Azure Automation PowerShell.

> [!NOTE]
> Метаконфигурации DSC содержат секреты, необходимые для посадки машины на счет автоматизации для управления. Будьте уверены, чтобы должным образом защитить любые DSC метаконфигурации вы создаете, или удалить их после использования.

Прокси-поддержка метаконфигураций контролируется LCM, который является движком Windows PowerShell DSC. LCM работает на всех целевых узлах и отвечает за вызов ресурсов конфигурации, включенных в сценарий метаконфигурации DSC. 

Вы можете включить поддержку прокси в метаконфигурации, включив определения прокси `ConfigurationRepositoryWeb`URL `ResourceRepositoryWeb`и `ReportServerWeb` прокси-учетных данных по мере необходимости в , и блоков. Смотрите [Настройка локального менеджера конфигурации](https://docs.microsoft.com/powershell/scripting/dsc/managing-nodes/metaconfig?view=powershell-7).

### <a name="generate-dsc-metaconfigurations-by-using-a-dsc-configuration"></a>Создание метаконфигураций DSC с помощью конфигурации DSC

1. Открыть visual Studio Code (или ваш любимый редактор) в качестве администратора на машине в локальной среде. Машина должна иметь последнюю версию [Windows Management Framework 5](https://aka.ms/wmf5latest) установлен.
1. Локально выполните следующий скрипт. Этот сценарий содержит конфигурацию PowerShell DSC для создания метаконфигураций и команду, запускающую этот процесс.

    > [!NOTE]
    > Имена конфигураций конфигурации состояния чувствительны на портале Azure. Если дело несовместимо, узла не будет отображаться под вкладкой **узлов.**

   ```powershell
   # The DSC configuration that will generate metaconfigurations.
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

    # Create the metaconfigurations.
    # NOTE: DSC Node Configuration names are case sensitive in the portal.
    # TODO: Edit the following as needed for your use case.
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

   # Use PowerShell splatting to pass parameters to the DSC configuration being invoked.
   # For more info about splatting, run: Get-Help -Name about_Splatting.
   DscMetaConfigs @Params
   ```

1. Введите регистрационный ключ и URL-адрес для учетной записи автоматизации, а также имена виртуальных машин, которые необходимо внедрить. Все остальные параметры являются необязательными. Чтобы найти ключ регистрации и URL-адрес регистрации для учетной записи Automation, [см. Onboard безопасно с помощью](#onboard-securely-by-using-registration) раздела регистрации.

1. Если вы хотите, чтобы машины сообщали информацию о состоянии DSC в Azure Automation `ReportOnly` State Configuration, но не тянули настройки или модули PowerShell, установите параметр в *истину.*

1. Если `ReportOnly` машина не установлена, машины сообщают информацию о состоянии DSC в модули состояния автоматизации Azure и конфигурацию управления powerShell. Установить параметры соответственно `ConfigurationRepositoryWeb`в `ResourceRepositoryWeb`, `ReportServerWeb` и блоки.

1. Выполните скрипт. Теперь у вас должна быть рабочая папка каталога под названием *DscMetaConfigs,* которая содержит метаконфигурации PowerShell DSC для машин на борту (в качестве администратора).

    ```powershell
    Set-DscLocalConfigurationManager -Path ./DscMetaConfigs
    ```

### <a name="generate-dsc-metaconfigurations-by-using-azure-automation-cmdlets"></a>Создание метаконфигураций DSC с помощью cmdlets Azure Automation

Если по умолчанию PowerShell DSC LCM совпадает с вашим примером использования и вы хотите, чтобы бортовые машины как вытащить из и сообщить в Azure Automation State Configuration, вы можете создать необходимые метаконфигурации DSC более просто с помощью cmdlets Azure Automation.

1. Откройте консоль PowerShell или Visual Studio Code в качестве администратора на машине в локальной среде.
1. Подключитесь к менеджеру ресурсов Azure с помощью [Connect-AzAccount](https://docs.microsoft.com/powershell/module/Az.Accounts/Connect-AzAccount?view=azps-3.7.0).
1. С учетной записи Automation, в которой вы настраиваете узлы, загрузите метаконфигурации PowerShell DSC для машин, которые вы хотите на борту.

   ```powershell
   # Define the parameters for Get-AzAutomationDscOnboardingMetaconfig using PowerShell Splatting.
   $Params = @{
       ResourceGroupName = 'ContosoResources'; # The name of the Resource Group that contains your Azure Automation account
       AutomationAccountName = 'ContosoAutomation'; # The name of the Azure Automation account where you want a node on-boarded to
       ComputerName = @('web01', 'web02', 'sql01'); # The names of the computers that the metaconfiguration will be generated for
       OutputFolder = "$env:UserProfile\Desktop\";
   }
   # Use PowerShell splatting to pass parameters to the Azure Automation cmdlet being invoked.
   # For more info about splatting, run: Get-Help -Name about_Splatting.
   Get-AzAutomationDscOnboardingMetaconfig @Params
   ```

1. Теперь у вас должна быть папка *DscMetaConfigs,* содержащая метаконфигурации PowerShell DSC для машин, которые вы хотите находиться на борту (в качестве администратора).

    ```powershell
    Set-DscLocalConfigurationManager -Path $env:UserProfile\Desktop\DscMetaConfigs
    ```

## <a name="onboard-securely-by-using-registration"></a>На борту надежно с помощью регистрации

Машины могут быть надежно загружены на борт учетной записи Azure Automation через протокол регистрации DSC DSC Для управления windows. Этот протокол позволяет узлу DSC аутентифицироваться на сервере PowerShell DSC, включая конфигурацию состояния автоматизации Azure. Узла регистрируется с сервером по URL-адресу регистрации и проверяетподлинность с помощью ключа регистрации. 

Во время регистрации узла DSC и сервера DSC pull/report ведут переговоры об уникальном сертификате, который узла использовать для проверки подлинности сервера после регистрации. Этот процесс предотвращает, чтобы на борту узлов выдавались за себя друг друга (например, если узла скомпрометировано и ведет себя злонамеренно). 

После регистрации ключ регистрации не используется повторно для проверки подлинности и удаляется из узла.

Чтобы получить информацию, необходимую для протокола регистрации государственной конфигурации, перейдите на портал Azure и, в **настройках учетной записи,** выберите **Ключи.** 

![Ключи автоматизации Azure и URL-адрес в панели ключей](./media/automation-dsc-onboarding/DSC_Onboarding_4.png)

- **URL-адрес регистрации**: Значение в поле **URL.**
- **Ключ регистрации**: Значение в **поле ключа доступа Primary** или в поле ключа **вторичного доступа.** Вы можете использовать любой ключ.

Для дополнительной безопасности можно регенерировать основные и вторичные ключи доступа учетной записи Automation в любое время в панели **ключей.** Регенерация ключей предотвращает использование предыдущих ключей в будущем.

## <a name="re-register-a-node"></a>Перерегистрация узла

После регистрации машины в качестве узла DSC в конфигурации состояния автоматизации Azure может потребоваться перерегистрация этого узла в будущем по любой из следующих причин:

- **Продление сертификата**: Для версий Windows Server раньше, чем Windows Server 2019, каждый узла автоматически обсуждает уникальный сертификат для проверки подлинности, срок действия которого истекает через год. Если срок действия сертификата истекает без продления, узел не может связаться с Azure Automation и помечен *как не отвечающий.* 

  В настоящее время протокол регистрации PowerShell DSC не может автоматически продлевать сертификаты, когда они приближаются к истечению, и вы должны перерегистрировать узлы через год. Перед перерегистрацией убедитесь, что каждый узла работает с платформой управления Windows 5 RTM. 

    Перерегистрация осуществляется через 90 или менее дней со времени истечения срока действия сертификата или в любой момент после истечения срока действия сертификата, что приводит к порождению и использованию нового сертификата. Решение этой проблемы включено в Windows Server 2019 и позже.

- **Изменения в значениях DSC LCM**: Возможно, потребуется изменить [значения PowerShell DSC LCM,](/powershell/scripting/dsc/managing-nodes/metaConfig4) которые `ConfigurationMode`были установлены во время первоначальной регистрации узла (например, ). В настоящее время изменить эти значения агента DSC можно только путем перерегистрации. Единственным исключением является значение конфигурации узлов, назначенное узлам. Это значение можно изменить непосредственно в DSC Azure Automation DSC.

Вы можете перерегистрировать узла так же, как вы зарегистрировали его на начальном этапе, используя любой из методов посадки, которые описаны в этом документе. Вам не нужно отменять узел из конфигурации состояния автоматизации Azure, прежде чем перерегистрировать его.

## <a name="troubleshoot-azure-virtual-machine-onboarding"></a>Проблема съемки Azure виртуальная машина на борту

Служба "Настройка состояния службы автоматизации Azure" позволяет легко подключать виртуальные машины Microsoft Azure для управления конфигурациями. Расширение DSC для виртуальных машин Azure используется для регистрации виртуальных машин в службе "Настройка состояния службы автоматизации Azure". Поскольку расширение Azure VM Desired State Configuration работает асинхронно, отслеживание его хода и устранение неполадок может быть важным.

> [!NOTE]
> Любой метод посадки Azure Windows VM в государственную конфигурацию Azure Automation, использующем расширение azure VM Desired State Configuration, может занять до часа, чтобы Azure Automation показал ее как зарегистрированную. Эта задержка является результатом установки платформы управления Windows 5 на VM расширением Azure VM Desired State Configuration, которое необходимо для посадки VM в государственную конфигурацию Автоматизации Azure.

Для устранения неполадок или просмотра статуса расширения Azure VM Desired State Configuration:

1. На портале Azure перейдите на VM, который на борту.
2. В разделе **Параметры** выберите **Расширения**. 
3. Выберите **DSC** или **DSCForLinux**, в зависимости от вашей операционной системы. 
4. Для получения дополнительной информации о расширении выберите **Просмотр подробного статуса.**

Для получения дополнительной информации об устранении неполадок см. [Проблемы устранения неполадок с Azure Automation Desired State Configuration (DSC).](./troubleshoot/desired-state-configuration.md)

## <a name="next-steps"></a>Дальнейшие действия

- Для начала смотрите [начало с конфигурацией состояния автоматизации Azure.](automation-dsc-getting-started.md)
- Чтобы узнать о компиляции конфигураций DSC, чтобы можно было назначить их целевым узлам, см. [конфигурации компиляции в конфигурации состояния azure Automation State Configuration.](automation-dsc-compile.md)
- Для справки PowerShell cmdlet [см.](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation)
- Для получения информации о ценах на цены [см.](https://azure.microsoft.com/pricing/details/automation/)
- Например, использование конфигурации состояния azure Automation в [Continuous deployment to virtual machines using Azure Automation State Configuration and Chocolatey](automation-dsc-cd-chocolatey.md)непрерывном конвейере развертывания см.
