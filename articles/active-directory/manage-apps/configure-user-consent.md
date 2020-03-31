---
title: Научитесь, как конечные пользователи соглашаются на приложения с помощью Azure AD
description: Узнайте, как управлять тем, как и когда пользователи могут дать согласие на приложения, которые будут иметь доступ к данным организации.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 10/22/2018
ms.author: mimart
ms.reviewer: arvindh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5bd305d2943d1b12756171748f28d32300081d71
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75443393"
---
# <a name="configure-how-end-users-consent-to-applications"></a>Настройка того, как конечные пользователи соглашаются на приложения

Приложения могут интегрироваться с платформой Microsoft Identity, чтобы позволить пользователям войти в систему с помощью своей рабочей или школьной учетной записи в Active Directory Azure (Azure AD) и получить доступ к данным организации для предоставления богатого взаимодействия с данными. Различные разрешения позволяют приложению получить различный уровень доступа к данным пользователей и организации.

По умолчанию пользователи могут дать согласие на доступ к данным организации, хотя и только для некоторых разрешений. Например, по умолчанию пользователь может дать согласие на доступ к своему почтовому ящику или беседам команд для команды, которой владеет пользователь, но не может дать согласие на то, чтобы приложение без присмотра было доступно для чтения и записи на все сайты SharePoint в вашей организации. Хотя предоставление пользователям согласия самостоятельно позволяет пользователям легко приобретать полезные приложения, которые интегрируются с Microsoft 365, Azure и другими службами, это может представлять риск, если они не используются и не контролируются тщательно.

Корпорация Майкрософт рекомендует отключить будущие операции согласия пользователей, чтобы уменьшить площадь поверхности и уменьшить этот риск. Если согласие пользователя отключено, предыдущие гранты согласия по-прежнему будут выполнены, но все будущие операции согласия должны выполняться администратором. Согласие рената на общеклиента может быть запрошено пользователями через интегрированный [рабочий процесс запроса запроса запроса запроса на согласие админа](configure-admin-consent-workflow.md) или через собственные процессы поддержки. Для более подробной информации о [знакомит с пятью шагами по обеспечению безопасности инфраструктуры идентификации.](../../security/fundamentals/steps-secure-identity.md)

## <a name="configure-user-consent-to-applications"></a>Настройка согласия пользователя на приложения
### <a name="disable-or-enable-user-consent-from-the-azure-portal"></a>Отключите или включите согласие пользователя с портала Azure

Вы можете использовать портал Azure, чтобы отключить или включить возможность пользователей дать согласие на доступ к данным организации:

1. Вопиюсь на [порталAzв](https://portal.azure.com) в качестве [глобального администратора.](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator)
2. Выберите **Активный каталог Azure,** затем **корпоративные приложения,** затем **настройки пользователя.**
3. Включить или отключить согласие пользователя с управлением помечены пользователи могут дать согласие на **приложения, имеющие доступ к данным компании от их имени.**
4. (Необязательно) Нанастройка [рабочего процесса запроса запроса запроса на согласие админа,](configure-admin-consent-workflow.md) чтобы пользователи, которым не разрешено дать согласие на приложение, могли запросить одобрение.

> [!TIP]
> Чтобы позволить пользователям запросить отзыв администратора приложения, на которое пользователю не разрешается соглашаться (например, из-за того, что согласие пользователя было отключено, или потому, что приложение запрашивает разрешения, которые пользователь не может предоставить), рассмотрите [возможность настройки рабочего процесса согласия администратора.](configure-admin-consent-workflow.md)

### <a name="disable-or-enable-user-consent-using-powershell"></a>Отключить или включить согласие пользователя с помощью PowerShell

Вы можете использовать модуль Azure AD PowerShell v1[(MSOnline),](https://docs.microsoft.com/powershell/module/msonline/?view=azureadps-1.0)чтобы включить или отключить возможность пользователей дать согласие на доступ к приложениям, доступным для данных организации.

1. Вопийте в организации, запустив этот cmdlet:

    ```powershell
    Connect-MsolService
    ```

2. Проверьте, включено ли согласие пользователя при запуске этого cmdlet:

    ```powershell
    Get-MsolCompanyInformation | Format-List UsersPermissionToUserConsentToAppEnabled
    ```

3. Включить или отключить согласие пользователя. Например, чтобы отключить согласие пользователя, запустите этот cmdlet:

    ```powershell
    Set-MsolCompanySettings -UsersPermissionToUserConsentToAppEnabled $false
    ```

## <a name="configure-group-owner-consent-to-apps-accessing-group-data"></a>Настройка согласия владельца группы на приложения, доступные для групповых данных

> [!IMPORTANT]
> Следующая информация предназначена для предстоящей функции, которая позволит владельцам групп предоставлять приложениям доступ к данным своих групп. Когда эта возможность будет выпущена, она будет включена по умолчанию. Хотя эта функция еще не выпущена широко, вы можете использовать эти инструкции, чтобы отключить возможность до ее выпуска.

Владельцы групп могут авторизовать приложения (например, приложения, опубликованные сторонними поставщиками) для доступа к данным организации, связанным с группой. Например, владелец группы (который является владельцем группы Office 365 Group) может разрешить приложению читать все сообщения Команд в группе или перечислять базовый профиль членов группы.

> [!NOTE]
> Независимо от этой настройки, владелец группы всегда может добавлять других пользователей или приложения непосредственно в качестве владельцев группы.

### <a name="configure-group-owner-consent-using-powershell"></a>Настройка согласия владельца группы с помощью PowerShell

Вы можете использовать модуль Preview Azure AD PowerShell Preview[(AzureADPreview),](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview)чтобы включить или отключить возможность владельцев групп дать согласие на доступ к приложениям, доступным для данных организации, для групп, которые у них есть.

1. Убедитесь, что вы используете модуль [AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview) (этот шаг важен, если вы установили модуль [AzureAD](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0) и модуль [AzureADPreview).](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview)

    ```powershell
    Remove-Module AzureAD
    Import-Module AzureADPreview
    ```

2. Подключайтесь к Azure AD PowerShell.

   ```powershell
   Connect-AzureAD
   ```

3. Извлеките текущее значение для настроек *параметров параметров параметров параметров параметров параметров параметров параметра параметров политики согласия* в вашем арендаторе. Это требует проверки, были ли созданы настройки каталога для этой функции, а если нет, то с использованием значений из соответствующего шаблона настроек каталога.

    ```powershell
    $consentSettingsTemplateId = "dffd5d46-495d-40a9-8e21-954ff55e198a" # Consent Policy Settings
    $settings = Get-AzureADDirectorySetting -All $true | Where-Object { $_.TemplateId -eq $consentSettingsTemplateId }

    if (-not $settings) {
        $template = Get-AzureADDirectorySettingTemplate -Id $consentSettingsTemplateId
        $settings = $template.CreateDirectorySetting()
    }

    $enabledValue = $settings.Values | ? { $_.Name -eq "EnableGroupSpecificConsent" }
    $limitedToValue = $settings.Values | ? { $_.Name -eq "ConstrainGroupSpecificConsentToMembersOfGroupId" }
    ```

4. Понимание значений настройки. Есть два значения параметров, которые определяют, какие пользователи смогут разрешить приложению доступ к данным своей группы:

    | Параметр       | Тип         | Описание  |
    | ------------- | ------------ | ------------ |
    | _EnableGroupSpecificConsent_   | Логическое |  Пометить, если владельцам групп разрешено выдавать разрешения, связанные с группой. |
    | _ОграничениегруппыСпецифическиеСогласияЧленыГруппы_ | Guid | Если _EnableGroupSpecificConsent_ настроен на "True" и это значение устанавливается в идентификатор объекта группы, члены идентифицированной группы будут уполномочены выдавать групповые разрешения группам, которые они владеют. |

5. Значения параметров обновления для нужной конфигурации:

    ```powershell
    # Disable group-specific consent entirely
    $enabledValue.Value = "False"
    $limitedToValue.Value = ""
    ```

    ```powershell
    # Enable group-specific consent for all users
    $enabledValue.Value = "True"
    $limitedToValue.Value = ""
    ```

    ```powershell
    # Enable group-specific consent for users in a given group
    $enabledValue.Value = "True"
    $limitedToValue.Value = "{group-object-id}"
    ```

6. Сохранение настроек.

    ```powershell
    if ($settings.Id) {
        # Update an existing directory settings
        Set-AzureADDirectorySetting -Id $settings.Id -DirectorySetting $settings
    } else {
        # Create a new directory settings to override the default setting 
        New-AzureADDirectorySetting -DirectorySetting $settings
    }
    ```

## <a name="next-steps"></a>Дальнейшие действия

[Настройка рабочего процесса согласия админа](configure-admin-consent-workflow.md)

[Грант ан-нанимателю согласия на заявку](grant-admin-consent.md)

[Разрешения и согласие в платформе идентификации Майкрософт](../develop/active-directory-v2-scopes.md)

[Azure AD на StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory)
