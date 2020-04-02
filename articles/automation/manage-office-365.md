---
title: Управление сервисами Office 365 с помощью автоматизации Azure
description: Рассказывает, как использовать Azure Automation для управления службами подписки Office 365.
services: automation
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: 9cb505ced907b143fbd6a5f4f30c818092005bb8
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80550426"
---
# <a name="manage-office-365-services-using-azure-automation"></a>Управление сервисами Office 365 с помощью автоматизации Azure

Вы можете использовать Azure Automation для управления службами подписки Office 365 для таких продуктов, как Microsoft Word и Microsoft Outlook. Взаимодействие с Office 365 включено с помощью [active Directory Azure (Azure AD).](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) [См. Использование Azure AD в Автоматизации Azure для проверки подлинности в Azure.](automation-use-azure-ad.md)

>[!NOTE]
>Эта статья была изменена и теперь содержит сведения о новом модуле Az для Azure PowerShell. Вы по-прежнему можете использовать модуль AzureRM, исправления ошибок для которого будут продолжать выпускаться как минимум до декабря 2020 г. Дополнительные сведения о совместимости модуля Az с AzureRM см. в статье [Introducing the new Azure PowerShell Az module](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0) (Знакомство с новым модулем Az для Azure PowerShell). Для инструкций по установке модуля Az на гибридном Runbook Worker [см.](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0) Для учетной записи Автоматизация вы можете обновить свои модули до последней версии, используя [как обновить модули Azure PowerShell в Azure Automation.](automation-update-azure-modules.md)

## <a name="prerequisites"></a>Предварительные требования

Для управления службами подписки Office 365 в Azure Automation требуется следующее.

* Подписка Azure. Смотрите [руководство по принятию решений по подписке](https://docs.microsoft.com/azure/cloud-adoption-framework/decision-guides/subscriptions/).
* Объект автоматизации в Azure для хранения учетных данных пользователей и runbooks. Смотрите [введение в автоматизацию Azure](https://docs.microsoft.com/azure/automation/automation-intro).
* Azure AD. [См. Использование Azure AD в Автоматизации Azure для проверки подлинности в Azure.](automation-use-azure-ad.md)
* Офис 365 арендатора, с учетной записью. Смотрите [Настройка вашего офиса 365 арендатора](https://docs.microsoft.com/sharepoint/dev/spfx/set-up-your-developer-tenant).

## <a name="installing-the-msonline-and-msonlineext-modules"></a>Установка модулей MSOnline и MSOnlineExt

Использование Office 365 в Azure Automation требует от Microsoft Azure Active Directory для Windows PowerShell (модуль).`MSOnline` Вам также понадобится [`MSOnlineExt`](https://www.powershellgallery.com/packages/MSOnlineExt/1.0.35)модуль, который упрощает управление Azure AD в условиях с одним и несколькими арендаторами. Установите модули, описанные в [Use Azure AD в Azure Automation, для проверки подлинности в Azure.](automation-use-azure-ad.md)

>[!NOTE]
>Чтобы использовать MSOnline PowerShell, вы должны быть членом Azure AD. Гость пользователи не могут использовать модуль.

## <a name="creating-an-azure-automation-account"></a>Создание учетной записи автоматизации Azure

Для выполнения шагов в этой статье вам нужна учетная запись в Azure Automation. Смотрите [Создать учетную запись автоматизации Azure.](automation-quickstart-create-account.md)
 
## <a name="adding-msonline-and-msonlineext-as-assets"></a>Добавление MSOnline и MSOnlineExt в качестве активов

Теперь добавьте установленные модули MSOnline и MSOnlineExt, чтобы включить функциональность Office 365. Обратитесь к [модулям управления в Azure Automation](shared-resources/modules.md).

1. На портале Azure выберите **учетные записи автоматизации.**
2. Выберите учетную запись Автоматизации.
3. Выберите **Галерею модулей** под **общими ресурсами.**
4. Поиск MSOnline.
5. Выберите `MSOnline` модуль PowerShell и нажмите **«Импорт»,** чтобы импортировать модуль в качестве актива.
6. Повторите шаги 4 и 5, чтобы найти и импортировать `MSOnlineExt` модуль. 

## <a name="creating-a-credential-asset-optional"></a>Создание актива учетных данных (необязательно)

Необязательно создавать учетный учетный данный для административного пользователя Office 365, у которого есть разрешения на запуск скрипта. Это может помочь, однако, чтобы сохранить от разоблачения имен пользователей и паролей внутри скриптов PowerShell. Для инструкций [см.](automation-use-azure-ad.md#creating-a-credential-asset)

## <a name="creating-an-office-365-service-account"></a>Создание учетной записи службы Office 365

Для запуска служб подписки Office 365 для выполнения службы подписки Office 365 нужен сервисный аккаунт Office 365 с разрешениями. Можно использовать одну учетную запись глобального администратора, одну учетную запись на службу или выполнять одну функцию или скрипт. В любом случае учетная запись службы требует сложного и безопасного пароля. Смотрите [Настройка Office 365 для бизнеса](https://docs.microsoft.com/microsoft-365/admin/setup/setup?view=o365-worldwide). 

## <a name="connecting-to-the-azure-ad-online-service"></a>Подключение к онлайн-сервису Azure AD

>[!NOTE]
>Для использования cmdlets модуля MSOnline необходимо запустить их из Windows PowerShell. PowerShell Core не поддерживает эти cmdlets.

Вы можете использовать модуль MSOnline для подключения к Azure AD по подписке Office 365. Соединение использует имя пользователя Office 365 и пароль или использует многофакторную аутентификацию (MFA). Вы можете подключиться с помощью портала Azure или запроса команды Windows PowerShell (не нужно поднимать).

Ниже приведен пример PowerShell. [Get-Credential](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-credential?view=powershell-7) cmdlet подсказывает учетные данные `Msolcred` и хранит их в переменной. Затем cmdlet [Connect-MsolService](https://docs.microsoft.com/powershell/module/msonline/connect-msolservice?view=azureadps-1.0) использует учетные данные для подключения к онлайн-сервису каталога Azure. Если требуется подключиться к определенной среде Azure, используйте `AzureEnvironment` параметр.

```powershell
$Msolcred = Get-Credential
Connect-MsolService -Credential $MsolCred -AzureEnvironment "AzureCloud"
```

Если вы не получили никаких ошибок, вы успешно подключились. Быстрый тест заключается в запуске Office 365 `Get-MsolUser`cmdlet, например, и увидеть результаты. Если вы получаете ошибки, обратите внимание, что распространенной проблемой является неправильный пароль.

>[!NOTE]
>Вы также можете использовать модуль AzureRM или модуль Az для подключения к Azure AD по подписке Office 365. Основным соединением cmdlet является [Connect-AzureAD](https://docs.microsoft.com/powershell/module/azuread/connect-azuread?view=azureadps-2.0). Этот cmdlet `AzureEnvironmentName` поддерживает параметр для конкретных сред Office 365.

## <a name="creating-a-powershell-runbook-from-an-existing-script"></a>Создание runbook PowerShell из существующего скрипта

Вы получаете доступ к функционалу Office 365 из сценария PowerShell. Вот пример сценария для учетных данных с `Office-Credentials` `admin@TenantOne.com`именем пользователя . Он `Get-AutomationPSCredential` использует для импорта учетных данных Office 365.

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

## <a name="running-the-script-in-a-runbook"></a>Запуск сценария в справочнике

Скрипт можно использовать в справочнике Azure Automation. Например, мы будем использовать тип runbook PowerShell.

1. Создайте новую книгу PowerShell. Обратитесь к [примеру создать runbook автоматизации Azure.](https://docs.microsoft.com/azure/automation/automation-quickstart-create-runbook)
2. Из вашей учетной записи автоматизации выберите **Runbooks** под **автоматизацией процессов.**
3. Выберите новый runbook и нажмите **Edit**.
4. Копируйте свой сценарий и вставьте его в текстовый редактор для runbook.
5. Выберите **ASSETS**, затем расширьте **учетные данные** и убедитесь, что учетные данные Office 365 есть.
6. Нажмите **Сохранить**.
7. Выберите **тестовую панель,** затем нажмите **Кнопка Начало,** чтобы начать тестирование runbook. Смотрите [запуски в Azure Automation](https://docs.microsoft.com/azure/automation/manage-runbooks).
8. По завершении тестирования выйдите из тестового стекла.

## <a name="publishing-and-scheduling-the-runbook"></a>Публикация и планирование книги

Чтобы опубликовать, а затем запланировать свой runbook, [см. Управление runbooks в Azure Automation](https://docs.microsoft.com/azure/automation/manage-runbooks).

## <a name="next-steps"></a>Дальнейшие действия

* Вы можете найти информацию об учетных ресурсах Automation в [учетных данных в Azure Automation.](shared-resources/credentials.md)
* Ознакомиться с [модулями Управления в Azure Automation](shared-resources/modules.md) можно узнать, как работать с модулями автоматизации.
* Для получения обзора управления runbook [см.](https://docs.microsoft.com/azure/automation/manage-runbooks)
* Чтобы узнать больше о методах, которые можно использовать для запуска [Starting a runbook in Azure Automation](automation-starting-a-runbook.md)запуска сборника в Azure Automation, см.
* Для получения дополнительной информации о PowerShell, включая [PowerShell Docs](https://docs.microsoft.com/powershell/scripting/overview)языковые справочные и учебные модули, см.