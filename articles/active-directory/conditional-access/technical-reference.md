---
title: Справочник по параметрам условного доступа в Azure Active Directory | Документация Майкрософт
description: Эта статья содержит обзор поддерживаемых параметров в политике условного доступа Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 03/22/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: spunukol
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0e87a4c7ebafd8ddcfa54c87b189316b0ce98b0f
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59358995"
---
# <a name="azure-active-directory-conditional-access-settings-reference"></a>Справочник по параметрам условного доступа в Azure Active Directory

Благодаря [условному доступу Azure Active Directory (Azure AD)](../active-directory-conditional-access-azure-portal.md) можно контролировать доступ авторизованных пользователей к своим ресурсам.

Эта статья содержит информацию о поддержке следующих параметров конфигурации в политике условного доступа:

- назначения облачных приложений;
- условие платформы устройства;
- условие клиентских приложений;
- требование утвержденных клиентских приложений.

Если это не та информация, которую вы ищете, оставьте комментарий в конце этой статьи.

## <a name="cloud-apps-assignments"></a>Назначения облачных приложений

С помощью политик условного доступа можно управлять доступом пользователей к [облачным приложениям](conditions.md#cloud-apps). При настройке политики условного доступа необходимо выбрать как минимум одно облачное приложение, к которому она применяется. 

![Выбор облачных приложений для политики](./media/technical-reference/09.png)

### <a name="microsoft-cloud-applications"></a>Облачные приложения Майкрософт

Политики условного доступа можно назначить для следующих облачных приложений от корпорации Майкрософт:

- Службы Azure Analysis Services
- Azure DevOps
- База данных SQL Azure и хранилище данных — [Подробнее](https://docs.microsoft.com/azure/sql-database/sql-database-conditional-access)
- Dynamics CRM Online
- Microsoft Application Insights Analytics
- Microsoft Azure Information Protection — [Подробнее](https://docs.microsoft.com/azure/information-protection/faqs#i-see-azure-information-protection-is-listed-as-an-available-cloud-app-for-conditional-accesshow-does-this-work)
- Управление Microsoft Azure — [Подробнее](https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management)
- Microsoft Azure RemoteApp
- Управление подписками Microsoft Azure
- Microsoft Cloud App Security
- Microsoft Commerce средств портал контроля доступа
- Служба проверки подлинности средства Microsoft Commerce
- Microsoft Flow
- Microsoft Forms
- Microsoft Intune
- Регистрация в Microsoft Intune
- Планировщик (Майкрософт);
- Microsoft Power BI
- Microsoft PowerApps
- Microsoft Search в Bing
- Microsoft StaffHub
- Microsoft Stream;
- Microsoft Teams
- Office 365 Exchange Online
- Office 365 SharePoint Online
- Office 365 Yammer
- Углубимся Office
- Office Sway
- Outlook Groups
- Project Online
- Skype для бизнеса Online
- Виртуальная частная сеть (VPN)
- Центр приложений Visual Studio
- ATP в Защитнике Windows

### <a name="other-applications"></a>Другие приложения

Помимо облачных приложений Майкрософт, политику условного доступа можно назначить для следующих типов облачных приложений:

- приложения, подключенные к Azure AD;
- предварительно интегрированное федеративное приложение SaaS;
- приложения, использующие единый вход с помощью пароля;
- бизнес-приложения;
- приложения, использующие прокси приложения Azure AD.

## <a name="device-platform-condition"></a>условие платформы устройства;

В политике условного доступа можно настроить условие платформы устройства для привязки политики к клиентской операционной системе. Условный доступ Azure AD поддерживает следующие платформы устройств:

- Android
- iOS
- Windows Phone
-  Windows
- macOS

![Привязка политики доступа к клиентской ОС](./media/technical-reference/41.png)

## <a name="client-apps-condition"></a>Условие клиентских приложений

В политике условного доступа можно настроить условие [Клиентские приложения](conditions.md#client-apps), чтобы привязать ее к клиентскому приложению, которое инициировало попытку доступа. Условие клиентских приложений можно задать, чтобы предоставить или заблокировать доступ, если была предпринята попытка доступа из приведенных ниже типов клиентских приложений:

- "Обзор"
- мобильные и классические приложения.

![Управление доступом к клиентским приложениям](./media/technical-reference/03.png)

### <a name="supported-browsers"></a>Поддерживаемые браузеры

В политике условного доступа можно выбрать параметр **Браузеры**, чтобы в качестве клиентского приложения указать браузеры.

![Управление доступом поддерживаемых браузеров](./media/technical-reference/05.png)

Этот параметр работает со всеми браузерами. Но чтобы выполнить условия политики устройств, например требование соответствия, поддерживаются следующие операционные системы и браузеры:

| ОС                     | Браузеры                                      |
| :--                    | :--                                           |
| Windows 10             | Internet Explorer, Microsoft Edge, Chrome     |
| Windows 8, Windows 8.1        | Internet Explorer, Chrome                     |
| Windows 7              | Internet Explorer, Chrome                     |
| iOS                    | Управляемый браузер Intune Safari, Microsoft Edge |
| Android                | Управляемый браузер Intune Chrome, Microsoft Edge |
| Windows Phone          | Internet Explorer, Microsoft Edge, Chrome             |
| Windows Server 2016    | Internet Explorer, Microsoft Edge, Chrome             |
| Windows Server 2016    | Chrome                                        |
| Windows Server 2012 R2 | Internet Explorer, Chrome                     |
| Windows Server 2008 R2 | Internet Explorer, Chrome                     |
| macOS                  | Chrome, Safari                                |

#### <a name="why-do-i-see-a-certificate-prompt-in-the-browser"></a>Почему в браузере запрос сертификата

В Windows 7, iOS, Android и macOS Azure AD определяет устройства, с помощью сертификата клиента, который был подготовлен, когда устройство регистрируется с помощью Azure AD.  При первом входе пользователя через браузер пользователя будет предложено выбрать сертификат. Пользователь должен выбрать этот сертификат, прежде чем использовать браузер.

#### <a name="chrome-support"></a>Поддержка Chrome

Для поддержки Chrome в **Windows 10 Creators Update (версия 1703)** или более поздней версии установите [это расширение](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji).

Чтобы автоматически развернуть это расширение в браузере Chrome, создайте следующий раздел реестра:

|    |    |
| --- | --- |
| Путь | HKEY_LOCAL_MACHINE\Software\Policies\Google\Chrome\ExtensionInstallForcelist |
| ИМЯ | 1 |
| type | REG_SZ (String) |
| Данные | ppnbnpeolgkicgegkbkbjmhlideopiji; https://clients2.google.com/service/update2/crx |

Для поддержки Chrome в **Windows 8.1 и Windows 7** создайте следующий раздел реестра:

|    |    |
| --- | --- |
| Путь | HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Google\Chrome\AutoSelectCertificateForUrls |
| ИМЯ | 1 |
| type | REG_SZ (String) |
| Данные | {"pattern":"https://device.login.microsoftonline.com","filter":{"ISSUER":{"CN":"MS-Organization-Access"}}} |

Эти браузеры поддерживают аутентификацию устройств, позволяя идентифицировать устройство и проверить, соответствует ли оно политике. Если браузер работает в частном режиме, проверка устройства завершается ошибкой.

### <a name="supported-mobile-applications-and-desktop-clients"></a>Поддерживаемые мобильные приложения и классические клиенты

В политике условного доступа можно выбрать параметр **Мобильные приложения и настольные клиенты**, чтобы в качестве клиентского приложения указать мобильные приложения и классические клиенты.

![Управление доступом поддерживаемых мобильных приложений или классических клиентов](./media/technical-reference/06.png)

Этот параметр влияет на попытки доступа, предпринимаемые из следующих мобильных приложений и классических клиентов.

| Клиентские приложения | Целевая служба | платформа |
| --- | --- | --- |
| Azure RemoteApp | Удаленная служба приложений Azure | Windows 10, Windows 8.1, Windows 7, iOS, Android и macOS |
| Приложение Dynamics CRM | Dynamics CRM | Windows 10, Windows 8.1, iOS и Android |
| Приложения Почта, Календарь и Люди, Outlook 2016, Outlook 2013 (с современной аутентификацией)| Office 365 Exchange Online | Windows 10 |
| MFA и политика расположения для приложений Политики на основе устройств не поддерживаются.| Все службы приложения "Мои приложения"| Android и iOS |
| Microsoft Teams Services — контролируют все службы, которые поддерживают Microsoft Teams, и все их клиентские приложения: для Windows Desktop, iOS, Android, WP, а также веб-клиент. | Microsoft Teams | Windows 10, Windows 8.1, Windows 7, iOS, Android и macOS |
| Приложения Office 2016, Office 2013 (с современной проверкой подлинности), клиент синхронизации OneDrive (см. [заметки](https://support.office.com/en-US/article/Azure-Active-Directory-conditional-access-with-the-OneDrive-sync-client-on-Windows-028d73d7-4b86-4ee0-8fb7-9a209434b04e)) | Office 365 SharePoint Online | Windows 8.1, Windows 7 |
| Приложения Office 2016, универсальные приложения Office, Office 2013 (с современной проверкой подлинности), клиент синхронизации OneDrive (см. [заметки](https://support.office.com/en-US/article/Azure-Active-Directory-conditional-access-with-the-OneDrive-sync-client-on-Windows-028d73d7-4b86-4ee0-8fb7-9a209434b04e)); поддержка групп Office и SharePoint ожидается в будущем | Office 365 SharePoint Online | Windows 10 |
| Office 2016 (только Word, Excel, PowerPoint, OneNote). Поддержку OneDrive для бизнеса планируется реализовать в будущем.| Office 365 SharePoint Online| macOS|
| Office 2019| Office 365 SharePoint Online | Windows 10, macOS |
| Мобильные приложения Office | Office 365 SharePoint Online | Android, iOS |
| Приложение Office Yammer | Office 365 Yammer | Windows 10, iOS, Android |
| Outlook 2019 | Office 365 SharePoint Online | Windows 10, macOS |
| Outlook 2016 (Office для macOS) | Office 365 Exchange Online | macOS |
| Outlook 2016, Outlook 2013 (с современной проверкой подлинности), Skype для бизнеса (с современной проверкой подлинности) | Office 365 Exchange Online | Windows 8.1, Windows 7 |
| Приложение Outlook Mobile | Office 365 Exchange Online | Android, iOS |
| Приложение Power BI | Служба Power BI | Windows 10, Windows 8.1, Windows 7, Android и iOS |
| Skype для бизнеса | Office 365 Exchange Online| Android, iOS |
| Приложение Visual Studio Team Services | Visual Studio Team Services | Windows 10, Windows 8.1, Windows 7, iOS и Android |

## <a name="support-for-legacy-authentication"></a>Поддержка устаревших методов проверки подлинности

Выберите вариант **Другие клиенты**, чтобы указать условие, которое влияет на приложения, использующие обычную проверку подлинности с протоколами электронной почты IMAP, MAPI, POP, SMTP, и приложения Office более ранних версий, которые не используют современные методы проверки подлинности.  

![Другие клиенты](./media/technical-reference/11.png)

Дополнительные сведения см. в разделе [Клиентские приложения](conditions.md#client-apps).

## <a name="approved-client-app-requirement"></a>Требование утвержденного клиентского приложения

В политике условного доступа можно указать, что попытка доступа к выбранным облачным приложениям должна предприниматься из утвержденного клиентского приложения. 

![Управление доступом утвержденных клиентских приложений](./media/technical-reference/21.png)

Этот параметр применяется к следующим клиентским приложениям.

- Microsoft Azure Information Protection.
- Microsoft Edge
- Microsoft Excel
- Microsoft Flow
- Microsoft Intune Managed Browser
- Microsoft Invoicing.
- Microsoft Kaizala
- Microsoft Launcher;
- Microsoft OneDrive
- Microsoft OneNote;
- Microsoft Outlook
- Планировщик (Майкрософт);
- Microsoft PowerApps
- Microsoft Power BI
- Microsoft PowerPoint
- Microsoft SharePoint
- Microsoft Skype для бизнеса;
- Microsoft StaffHub
- Microsoft Stream;
- Microsoft Teams
- Microsoft To-Do
- Microsoft Visio;
- Microsoft Word
- Microsoft Yammer

**Примечания**

- Утвержденные клиентских приложения поддерживают функцию управления мобильными приложениями Intune.
- Функция **Требовать утвержденное клиентское приложение**:
   - поддерживает только iOS и Android для [условия платформы устройства](#device-platform-condition);

## <a name="app-protection-policy-requirement"></a>Требование политики защиты приложений 

В политике условного доступа вы можете потребовать, чтобы политики защиты приложений в клиентском приложении перед предоставляется доступ к выбранным облачным приложениям. 

![Управление доступом с помощью политики защиты приложений](./media/technical-reference/22.png)

Этот параметр применяется к следующим клиентским приложениям.

- Microsoft OneDrive
- Microsoft Outlook

**Примечания**

- Приложения для политики защиты приложения поддерживают функцию управления мобильными приложениями Intune с политикой защиты.
- **Требуется политика защиты приложений** требования:
    - поддерживает только iOS и Android для [условия платформы устройства](#device-platform-condition);

## <a name="next-steps"></a>Дальнейшие действия

- Общие сведения об условном доступе см. в статье [Условный доступ в Azure Active Directory](../active-directory-conditional-access-azure-portal.md)
- Если вы готовы к настройке политик условного доступа для своей среды, прочитайте статью [Рекомендации по работе с условным доступом в Azure Active Directory](best-practices.md).

<!--Image references-->
[1]: ./media/technical-reference/01.png
