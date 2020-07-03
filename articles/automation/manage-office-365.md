---
title: Управление службами Office 365 с помощью службы автоматизации Azure
description: Сведения о том, как использовать службу автоматизации Azure для управления службами подписки Office 365.
services: automation
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: 9cb505ced907b143fbd6a5f4f30c818092005bb8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80550426"
---
# <a name="manage-office-365-services-using-azure-automation"></a>Управление службами Office 365 с помощью службы автоматизации Azure

Службу автоматизации Azure можно использовать для управления службами подписки Office 365 для таких продуктов, как Microsoft Word и Microsoft Outlook. Взаимодействие с Office 365 включается [Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis). См. статью [Использование Azure AD в службе автоматизации Azure для проверки подлинности в Azure](automation-use-azure-ad.md).

>[!NOTE]
>Эта статья была изменена и теперь содержит сведения о новом модуле Az для Azure PowerShell. Вы по-прежнему можете использовать модуль AzureRM, исправления ошибок для которого будут продолжать выпускаться как минимум до декабря 2020 г. Дополнительные сведения о совместимости модуля Az с AzureRM см. в статье [Introducing the new Azure PowerShell Az module](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0) (Знакомство с новым модулем Az для Azure PowerShell). Инструкции по установке модуля Az в гибридной рабочей роли Runbook см. в статье об [установке модуля Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Чтобы обновить модули в учетной записи службы автоматизации, см. руководство по [обновлению модулей Azure PowerShell в службе автоматизации Azure](automation-update-azure-modules.md).

## <a name="prerequisites"></a>Предварительные требования

Для управления службами подписки Office 365 в службе автоматизации Azure необходимо следующее.

* Подписка Azure. См. раздел [руководств по решениям подписки](https://docs.microsoft.com/azure/cloud-adoption-framework/decision-guides/subscriptions/).
* Объект автоматизации в Azure для хранения учетных данных и модулей Runbook учетной записи пользователя. Ознакомьтесь с [введением в службу автоматизации Azure](https://docs.microsoft.com/azure/automation/automation-intro).
* Azure AD. См. статью [Использование Azure AD в службе автоматизации Azure для проверки подлинности в Azure](automation-use-azure-ad.md).
* Клиент Office 365 с учетной записью. См. раздел [Настройка клиента Office 365](https://docs.microsoft.com/sharepoint/dev/spfx/set-up-your-developer-tenant).

## <a name="installing-the-msonline-and-msonlineext-modules"></a>Установка модулей MSOnline и Мсонлиникст

Для использования Office 365 в службе автоматизации Azure требуется Microsoft Azure Active Directory для Windows PowerShell`MSOnline` (модуль). Вам также понадобится модуль [`MSOnlineExt`](https://www.powershellgallery.com/packages/MSOnlineExt/1.0.35), который упрощает управление Azure AD в средах с одним и несколькими клиентами. Установите модули, как описано в статье [Использование Azure AD в службе автоматизации Azure для проверки подлинности в Azure](automation-use-azure-ad.md).

>[!NOTE]
>Чтобы использовать MSOnline PowerShell, необходимо быть членом Azure AD. Гостевые пользователи не могут использовать этот модуль.

## <a name="creating-an-azure-automation-account"></a>Создание учетной записи службы автоматизации Azure

Для выполнения действий, описанных в этой статье, требуется учетная запись в службе автоматизации Azure. См. раздел [Создание учетной записи службы автоматизации Azure](automation-quickstart-create-account.md).
 
## <a name="adding-msonline-and-msonlineext-as-assets"></a>Добавление MSOnline и Мсонлиникст в качестве ресурсов

Теперь добавьте установленные модули MSOnline и Мсонлиникст, чтобы включить функциональные возможности Office 365. См. статью [Управление модулями в службе автоматизации Azure](shared-resources/modules.md).

1. В портал Azure выберите **учетные записи службы автоматизации**.
2. Выберите учетную запись службы автоматизации.
3. В разделе **Общие ресурсы**выберите **коллекция модулей** .
4. Выполните поиск по запросу MSOnline.
5. Выберите модуль `MSOnline` PowerShell и нажмите кнопку **Импорт** , чтобы импортировать модуль как ресурс.
6. Повторите шаги 4 и 5, чтобы указать и импортировать `MSOnlineExt` модуль. 

## <a name="creating-a-credential-asset-optional"></a>Создание ресурса учетных данных (необязательно)

Создать ресурс-контейнер учетных данных для пользователя с правами администратора Office 365, который имеет разрешения на выполнение скрипта, необязательно. Однако это может помочь предотвратить предоставление имен пользователей и паролей в сценариях PowerShell. Инструкции см. [в разделе Создание ресурса учетных данных](automation-use-azure-ad.md#creating-a-credential-asset).

## <a name="creating-an-office-365-service-account"></a>Создание учетной записи службы Office 365

Для запуска служб подписки Office 365 требуется учетная запись службы Office 365 с разрешениями на выполнение необходимых действий. Можно использовать одну учетную запись глобального администратора, одну учетную запись для каждой службы или одну функцию или сценарий для выполнения. В любом случае учетной записи службы требуется сложный и безопасный пароль. См. раздел [Настройка Office 365 для бизнеса](https://docs.microsoft.com/microsoft-365/admin/setup/setup?view=o365-worldwide). 

## <a name="connecting-to-the-azure-ad-online-service"></a>Подключение к веб-службе Azure AD

>[!NOTE]
>Чтобы использовать командлеты модуля MSOnline, необходимо запустить их из Windows PowerShell. PowerShell Core не поддерживает эти командлеты.

Вы можете использовать модуль MSOnline для подключения к Azure AD из подписки Office 365. Подключение использует имя пользователя и пароль Office 365 или использует многофакторную проверку подлинности (MFA). Подключиться можно с помощью портал Azure или командной строки Windows PowerShell (не требуется повышать уровень).

Ниже показан пример PowerShell. Командлет [Get-Credential](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-credential?view=powershell-7) запрашивает учетные данные и сохраняет их в `Msolcred` переменной. Затем командлет [Connect-MsolService](https://docs.microsoft.com/powershell/module/msonline/connect-msolservice?view=azureadps-1.0) использует учетные данные для подключения к веб-службе Azure Directory. Если вы хотите подключиться к определенной среде Azure, используйте `AzureEnvironment` параметр.

```powershell
$Msolcred = Get-Credential
Connect-MsolService -Credential $MsolCred -AzureEnvironment "AzureCloud"
```

Если ошибки не появляются, подключение успешно установлено. Быстрая проверка заключается в запуске командлета Office 365, `Get-MsolUser`например, и просмотре результатов. При возникновении ошибок обратите внимание, что распространенная проблема — неверный пароль.

>[!NOTE]
>Вы также можете использовать модуль AzureRM или AZ для подключения к Azure AD из подписки Office 365. Главный командлет подключения — [Connect-AzureAD](https://docs.microsoft.com/powershell/module/azuread/connect-azuread?view=azureadps-2.0). Этот командлет поддерживает `AzureEnvironmentName` параметр для конкретных сред Office 365.

## <a name="creating-a-powershell-runbook-from-an-existing-script"></a>Создание модуля Runbook PowerShell из существующего скрипта

Доступ к функциям Office 365 осуществляется из сценария PowerShell. Ниже приведен пример сценария для учетных данных `Office-Credentials` с именем пользователя. `admin@TenantOne.com` Он использует `Get-AutomationPSCredential` для импорта учетных данных Office 365.

```powershell
$emailFromAddress = "admin@TenantOne.com" 
$emailToAddress = "servicedesk@TenantOne.com" 
$emailSMTPServer = "outlook.office365.com" 
$emailSubject = "Office 365 License Report" 

$credObject = Get-AutomationPSCredential -Name "Office-Credentials" 
Connect-MsolService -Credential $credObject

$O365Licenses = Get-MsolAccountSku | Out-String 
Send-MailMessage -Credential $credObject -From $emailFromAddress -To $emailToAddress -Subject $emailSubject -Body 

$O365Licenses -SmtpServer $emailSMTPServer -UseSSL
```

## <a name="running-the-script-in-a-runbook"></a>Выполнение скрипта в модуле Runbook

Вы можете использовать скрипт в модуле Runbook службы автоматизации Azure. Например, мы будем использовать тип Runbook PowerShell.

1. Создайте новый Runbook PowerShell. См. статью [Создание модуля Runbook службы автоматизации Azure](https://docs.microsoft.com/azure/automation/automation-quickstart-create-runbook).
2. В учетной записи службы автоматизации выберите **модули Runbook** в разделе **Автоматизация процессов**.
3. Выберите новый модуль Runbook и нажмите кнопку **изменить**.
4. Скопируйте скрипт и вставьте его в текстовый редактор для модуля Runbook.
5. Выберите **активы**, затем разверните **учетные данные** и убедитесь, что учетные данные Office 365 находятся там.
6. Нажмите кнопку **Сохранить**.
7. Выберите **область тестирования**, а затем нажмите кнопку **начать** , чтобы начать тестирование модуля Runbook. См. раздел [Управление модулями Runbook в службе автоматизации Azure](https://docs.microsoft.com/azure/automation/manage-runbooks).
8. После завершения тестирования выйдите из области тестирования.

## <a name="publishing-and-scheduling-the-runbook"></a>Публикация и планирование модуля Runbook

Сведения о публикации и планировании модуля Runbook см. [в статье Управление модулями Runbook в службе автоматизации Azure](https://docs.microsoft.com/azure/automation/manage-runbooks).

## <a name="next-steps"></a>Дальнейшие шаги

* Сведения о ресурсах учетных данных службы автоматизации можно найти в [ресурсах учетных данных в службе автоматизации Azure](shared-resources/credentials.md).
* Сведения о работе с модулями автоматизации см. в статье [Управление модулями в службе автоматизации Azure](shared-resources/modules.md) .
* Общие сведения об управлении Runbook см. [в статье Управление модулями Runbook в службе автоматизации Azure](https://docs.microsoft.com/azure/automation/manage-runbooks).
* Дополнительные сведения о методах, которые можно использовать для запуска модуля Runbook в службе автоматизации Azure, см. в статье [Запуск модуля Runbook в службе автоматизации Azure](automation-starting-a-runbook.md).
* Дополнительные сведения о PowerShell, включая Справочник по языку и обучающие модули, см. в документации по [PowerShell](https://docs.microsoft.com/powershell/scripting/overview).