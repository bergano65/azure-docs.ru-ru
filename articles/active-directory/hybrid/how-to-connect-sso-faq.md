---
title: 'Azure AD Connect выполняет следующие функции: простой единый вход — часто задаваемые вопросы | Документация Майкрософт'
description: Ответы на часто задаваемые вопросы о простом едином входе Azure Active Directory.
services: active-directory
keywords: что такое Azure AD Connect, установка Active Directory, необходимые компоненты для Azure AD, единый вход
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/14/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 96d0253e1e656f4bdb5180af0d57824aceb4f0dd
ms.sourcegitcommit: 83df2aed7cafb493b36d93b1699d24f36c1daa45
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/22/2019
ms.locfileid: "71176666"
---
# <a name="azure-active-directory-seamless-single-sign-on-frequently-asked-questions"></a>Простой единый вход Azure Active Directory: Часто задаваемые вопросы

В этой статье представлены часто задаваемые вопросы о простом едином входе Azure Active Directory. Следите за новым содержимым.

**Вопрос. Какие методы входа выполняют простой единый вход с**

Простой единый вход можно использовать вместе с методами [синхронизация хэша паролей](how-to-connect-password-hash-synchronization.md) или [сквозной проверки подлинности](how-to-connect-pta.md). Однако эту функцию нельзя использовать со службами федерации Active Directory (AD FS).

**Вопрос. Простой единый вход — бесплатная функция?**

Это бесплатный компонент, и для его использования не требуются платные выпуски Azure AD.

**Вопрос. Доступен ли простой единый вход в [Microsoft Azure — Германия облаке](https://www.microsoft.de/cloud-deutschland) и [Microsoft Azure для государственных организаций облаке](https://azure.microsoft.com/features/gov/)?**

Нет. Компонент "Простой единый вход" доступен только в международном экземпляре Azure AD.

**Вопрос. Какие приложения используют преимущества `domain_hint` или `login_hint` возможности простого единого входа?**

Ниже приведен неполный список приложений, которые могут отправить эти параметры в Azure AD и тем самым обеспечить автоматический вход пользователей с помощью простого единого входа (то есть пользователю не требуется вводить имя пользователя или пароль):

| Имя приложения | Используемый URL-адрес приложения |
| -- | -- |
| Панель доступа | HTTPS:\//MyApps.Microsoft.com/contoso.com |
| Outlook on Web | HTTPS:\//Outlook.Office365.com/contoso.com |
| Порталы Office 365 | HTTPS:\//портал.оффице.ком? domain_hint = contoso. com, HTTPS:\//ВВВ.оффице.ком? domain_hint = contoso. com |

Кроме того, пользователи получают возможность автоматического входа в систему, если приложение отправляет запросы на вход в конечные точки Azure AD, настроенные как клиенты, то есть HTTPS:\//Login.microsoftonline.com/contoso.com/<. > или HTTPS:\//Login.microsoftonline.com/<tenant_ID>/<.. > — вместо общей конечной точки Azure AD — т. е. HTTPS:\//Login.microsoftonline.com/Common/<... >. Ниже приведен неполный список приложений, которые выполняют такие типы запросов на вход.

| Имя приложения | Используемый URL-адрес приложения |
| -- | -- |
| SharePoint Online | HTTPS:\//contoso.SharePoint.com |
| портала Azure | HTTPS:\//Portal.Azure.com/contoso.com |

В приведенных выше таблицах замените contoso.com своим доменным именем, чтобы получить соответствующие URL-адреса приложений для своего клиента.

Если вы хотите использовать автоматический единый вход для других приложений, сообщите нам об этом в разделе отзывов.

**Вопрос. Поддерживает `Alternate ID` ли простой единый вход в качестве имени пользователя, `userPrincipalName`а не?**

Да. Поддерживает ли простой единый вход `Alternate ID` в качестве имени пользователя, если это настроено в Azure AD Connect, как показано [здесь](how-to-connect-install-custom.md). Не все приложения Office 365 поддерживают `Alternate ID`. Заявление о поддержке см. в документации на приложения.

**Вопрос. В чем разница между интерфейсом единого входа, обеспечиваемым [присоединением к Azure AD](../active-directory-azureadjoin-overview.md) и простым входом в систему?**

После [присоединения к Azure AD](../active-directory-azureadjoin-overview.md) пользователи могут воспользоваться возможностями единого входа, если их устройства зарегистрированы в Azure AD. Эти устройства не должны быть присоединены к домену. Возможность единого входа обеспечивается с помощью *основных токенов обновления* (*PRT*), а не протокола Kerberos. Лучше всего эта возможность работает на устройствах с Windows 10. В браузере Microsoft Edge единый вход выполняется автоматически. Эта возможность также работает в Chrome с использованием расширения браузера.

В клиенте можно настроить и технологию единого входа, предоставленную после присоединения к Azure AD, и простой единый вход. Эти две возможности дополняют друг друга. Если включены обе эти технологии, приоритет имеет единый вход, предоставленный после присоединения к Azure AD.

**Вопрос. Я хочу зарегистрировать устройства, на которых не применяется Windows 10, с помощью Azure AD, без использования AD FS. Можно ли вместо этого использовать простой единый вход?**

Да, для этого сценария требуется версия 2.1 или более поздняя версия [клиента присоединения к рабочей области](https://www.microsoft.com/download/details.aspx?id=53554).

**Вопрос. Как выполнить перечисление ключа дешифрования Kerberos для `AZUREADSSOACC` учетной записи компьютера?**

Очень важно часто менять ключ расшифровки Kerberos компьютерной учетной записи `AZUREADSSOACC` (представляющей Azure AD) в локальном лесу AD.

>[!IMPORTANT]
>Мы настоятельно рекомендуем, чтобы вы меняли ключ расшифровки Kerberos хотя бы раз в 30 дней.

Выполните следующие действия на локальном сервере, на котором выполняется Azure AD Connect:

    **Step 1. Get list of AD forests where Seamless SSO has been enabled**

    1. Сначала скачайте и установите [Azure PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview).
    2. Перейдите в папку `%programfiles%\Microsoft Azure Active Directory Connect`.
    3. Импортируйте модуль PowerShell для простого единого входа с помощью следующей команды: `Import-Module .\AzureADSSO.psd1`.
    4. Откройте PowerShell от имени администратора. В PowerShell вызовите `New-AzureADSSOAuthenticationContext`. Появится всплывающее окно для ввода учетных данных глобального администратора клиента.
    5. Вызовите `Get-AzureADSSOStatus | ConvertFrom-Json`. Эта команда выводит список лесов AD (см. список "Домены"), в которых включена эта функция.

    **Шаг 2. Обновите ключ расшифровки Kerberos в каждом лесу AD, в котором он был настроен.**

    1. Вызовите `$creds = Get-Credential`. При запросе введите свои учетные данные администратора домена для нужного леса AD.

    > [!NOTE]
    > Мы используем имя пользователя администратора домена, указанное в формате имени участника-пользователя (johndoe@contoso.com) или формате полного доменного имени учетной записи SAM (contoso\johndoe или contoso.com\johndoe), чтобы найти предполагаемый лес AD. Если вы используете полное доменное имя учетной записи SAM, то мы используем доменную часть имени пользователя, чтобы [найти контроллер домена администратора домена с помощью DNS](https://social.technet.microsoft.com/wiki/contents/articles/24457.how-domain-controllers-are-located-in-windows.aspx). Если же вы используете имя участника-пользователя, то мы [преобразуем его в полное доменное имя учетной записи SAM](https://docs.microsoft.com/windows/desktop/api/ntdsapi/nf-ntdsapi-dscracknamesa), прежде чем найти соответствующий контроллер домена.

    2. Вызовите `Update-AzureADSSOForest -OnPremCredentials $creds`. Эта команда обновляет ключ расшифровки Kerberos для компьютерной учетной записи `AZUREADSSOACC` в этом лесу AD и обновляет его в Azure AD.
    3. Повторите предыдущие шаги для каждого леса AD, где настроена эта функция.

    >[!IMPORTANT]
    >_Не_ выполняйте команду `Update-AzureADSSOForest` более одного раза. В противном случае функция перестанет действовать, пока не истечет срок действия пользовательского билета Kerberos и пока локальный экземпляр Active Directory не выдаст новый билет.

**Вопрос. Как отключить простой единый вход?**

    **Step 1. Disable the feature on your tenant**

        **Option A: Disable using Azure AD Connect**

        1. Run Azure AD Connect, choose **Change user sign-in page** and click **Next**.
        2. Uncheck the **Enable single sign on** option. Continue through the wizard.

        After completing the wizard, Seamless SSO will be disabled on your tenant. However, you will see a message on screen that reads as follows:

        "Single sign-on is now disabled, but there are additional manual steps to perform in order to complete clean-up. Learn more"

        To complete the clean-up process, follow steps 2 and 3 on the on-premises server where you are running Azure AD Connect.

        **Option B: Disable using PowerShell**

        Run the following steps on the on-premises server where you are running Azure AD Connect:

        1. First, download, and install [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview).
        2. Navigate to the `%programfiles%\Microsoft Azure Active Directory Connect` folder.
        3. Import the Seamless SSO PowerShell module using this command: `Import-Module .\AzureADSSO.psd1`.
        4. Run PowerShell as an Administrator. In PowerShell, call `New-AzureADSSOAuthenticationContext`. This command should give you a popup to enter your tenant's Global Administrator credentials.
        5. Call `Enable-AzureADSSO -Enable $false`.

        >[!IMPORTANT]
        >Disabling Seamless SSO using PowerShell will not change the state in Azure AD Connect. Seamless SSO will show as enabled in the **Change user sign-in** page.

    **Step 2. Get list of AD forests where Seamless SSO has been enabled**

    Follow tasks 1 through 4 below if you have disabled Seamless SSO using Azure AD Connect. If you have disabled Seamless SSO using PowerShell instead, jump ahead to task 5 below.

    1. Сначала скачайте и установите [Azure PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview).
    2. Перейдите в папку `%programfiles%\Microsoft Azure Active Directory Connect`.
    3. Импортируйте модуль PowerShell для простого единого входа с помощью следующей команды: `Import-Module .\AzureADSSO.psd1`.
    4. Откройте PowerShell от имени администратора. В PowerShell вызовите `New-AzureADSSOAuthenticationContext`. Появится всплывающее окно для ввода учетных данных глобального администратора клиента.
    5. Вызовите `Get-AzureADSSOStatus | ConvertFrom-Json`. Эта команда выводит список лесов AD (см. список "Домены"), в которых включена эта функция.

    **Шаг 3. Вручную удалите `AZUREADSSOACCT` учетную запись компьютера из каждого леса AD, который отображается в списке.**

## <a name="next-steps"></a>Следующие шаги

- [**Краткое руководство**](how-to-connect-sso-quick-start.md). Настройка и подготовка к работе простого единого входа Azure AD.
- [**Техническое руководство по сквозной проверке подлинности Azure Active Directory**](how-to-connect-sso-how-it-works.md). Сведения о том, как работает эта функция.
- [**Устранение неполадок**](tshoot-connect-sso.md). Узнайте, как устранить самые распространенные проблемы с этой функцией.
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect). Отправка запросов новых функций.
