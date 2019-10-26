---
title: Справочник по параметрам условного доступа Azure Active Directory | Документация Майкрософт
description: Получите общие сведения о поддерживаемых параметрах в Azure Active Directory политики условного доступа.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: reference
ms.date: 07/10/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: spunukol
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5a3518dfcad3678dc298ba8529e731d48ec1d195
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/24/2019
ms.locfileid: "72893462"
---
# <a name="azure-active-directory-conditional-access-settings-reference"></a>Справочник по параметрам условного доступа Azure Active Directory

Вы можете использовать [Условный доступ Azure Active Directory (Azure AD)](../active-directory-conditional-access-azure-portal.md) , чтобы контролировать, как разрешенные пользователи могут получать доступ к ресурсам.

В этой статье содержатся сведения о поддержке следующих параметров конфигурации в политике условного доступа:

- назначения облачных приложений;
- условие платформы устройства;
- условие клиентских приложений;
- требование утвержденных клиентских приложений.

Если это не та информация, которую вы ищете, оставьте комментарий в конце этой статьи.

## <a name="cloud-apps-assignments"></a>Назначения облачных приложений

С помощью политик условного доступа вы управляете доступом пользователей к [облачным приложениям](conditions.md#cloud-apps-and-actions). При настройке политики условного доступа необходимо выбрать хотя бы одно облачное приложение. 

![Выбор облачных приложений для политики](./media/technical-reference/09.png)

### <a name="microsoft-cloud-applications"></a>Облачные приложения Майкрософт

Политику условного доступа можно назначить следующим облачным приложениям от Майкрософт:

- Службы Azure Analysis Services
- Azure DevOps
- База данных SQL Azure и хранилище данных — [Дополнительные сведения](https://docs.microsoft.com/azure/sql-database/sql-database-conditional-access)
- Dynamics CRM Online
- Аналитика Microsoft Application Insights
- Microsoft Azure Information Protection — дополнительные [сведения](https://docs.microsoft.com/azure/information-protection/faqs#i-see-azure-information-protection-is-listed-as-an-available-cloud-app-for-conditional-accesshow-does-this-work)
- Управление Microsoft Azureми — дополнительные [сведения](https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management)
- Управление подпиской Microsoft Azure
- Microsoft Cloud App Security
- Портал управления доступом к средствам Microsoft Commerce
- Служба проверки подлинности инструментов Microsoft Commerce
- Microsoft Flow
- Microsoft Forms
- Microsoft Intune
- Регистрация в Microsoft Intune
- Планировщик (Майкрософт);
- Microsoft PowerApps
- Microsoft Search в Bing
- Microsoft StaffHub
- Microsoft Stream
- Microsoft Teams
- Office 365 Exchange Online
- Office 365 SharePoint Online
- Office 365 Yammer
- Office delve
- Office Sway
- Outlook Groups
- Служба Power BI
- Project Online
- Skype для бизнеса Online
- Виртуальная частная сеть (VPN)
- ATP в Защитнике Windows

### <a name="other-applications"></a>Другие приложения

Помимо облачных приложений Майкрософт, политику условного доступа можно назначить для следующих типов облачных приложений:

- приложения, подключенные к Azure AD;
- предварительно интегрированное федеративное приложение SaaS;
- приложения, использующие единый вход с помощью пароля;
- бизнес-приложения;
- приложения, использующие прокси приложения Azure AD.

## <a name="device-platform-condition"></a>условие платформы устройства;

В политике условного доступа можно настроить условие платформы устройства, чтобы привязать политику к операционной системе на клиенте. Условный доступ Azure AD поддерживает следующие платформы устройств:

- Android
- iOS
- Windows Phone
- Windows
- MacOS

![Привязка политики доступа к клиентской ОС](./media/technical-reference/41.png)

Если вы блокируете устаревшую проверку подлинности с помощью условия **другие клиенты** , можно также задать условие платформы устройства.

## <a name="client-apps-condition"></a>Условие клиентских приложений

В политике условного доступа можно настроить условие [клиентских приложений](conditions.md#client-apps) , чтобы привязать политику к клиентскому приложению, которое инициировало попытку доступа. Условие клиентских приложений можно задать, чтобы предоставить или заблокировать доступ, если была предпринята попытка доступа из приведенных ниже типов клиентских приложений:

- Браузер
- мобильные и классические приложения.

![Управление доступом к клиентским приложениям](./media/technical-reference/03.png)

### <a name="supported-browsers"></a>Поддерживаемые браузеры

В политике условного доступа можно выбрать **браузеры** как клиентское приложение.

![Управление доступом поддерживаемых браузеров](./media/technical-reference/05.png)

Этот параметр работает со всеми браузерами. Но чтобы выполнить условия политики устройств, например требование соответствия, поддерживаются следующие операционные системы и браузеры:

| ОС                     | Браузеры                                        |
| :--                    | :--                                             |
| Windows 10             | Microsoft ребро, Internet Explorer, Chrome       |
| Windows 8, Windows 8.1        | Internet Explorer, Chrome                       |
| Windows 7              | Internet Explorer, Chrome                       |
| iOS                    | Microsoft ребро, Intune Managed Browser, Safari  |
| Android                | Microsoft ребро, Intune Managed Browser, Chrome  |
| Windows Phone          | Microsoft ребро, Internet Explorer               |
| Windows Server 2019    | Microsoft ребро, Internet Explorer, Chrome       |
| Windows Server 2016    | Internet Explorer |
| Windows Server 2012 R2 | Internet Explorer                       |
| Windows Server 2008 R2 | Internet Explorer                       |
| MacOS                  | Chrome, Safari                                  |


#### <a name="why-do-i-see-a-certificate-prompt-in-the-browser"></a>Почему в браузере отображается запрос на сертификат

В Windows 7, iOS, Android и macOS Azure AD определяет устройство с помощью сертификата клиента, подготовленного при регистрации устройства в Azure AD.  При первом входе пользователя в систему через браузер пользователю предлагается выбрать сертификат. Пользователь должен выбрать этот сертификат перед использованием браузера.

#### <a name="chrome-support"></a>Поддержка Chrome

Для поддержки Chrome в **Windows 10 Creators Update (версия 1703)** или более поздней установите [Расширение учетных записей Windows 10](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji). Это расширение требуется, если для политики условного доступа требуются определенные сведения об устройстве.

Чтобы автоматически развернуть это расширение в браузере Chrome, создайте следующий раздел реестра:

|    |    |
| --- | --- |
| Путь | HKEY_LOCAL_MACHINE\Software\Policies\Google\Chrome\ExtensionInstallForcelist |
| Name | 1 |
| Тип | REG_SZ (String) |
| Данные | ппнбнпеолгкикжегкбкбжмхлидеопижи, HTTPS\://clients2.google.com/service/update2/crx |

Для поддержки Chrome в **Windows 8.1 и Windows 7** создайте следующий раздел реестра:

|    |    |
| --- | --- |
| Путь | HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Google\Chrome\AutoSelectCertificateForUrls |
| Name | 1 |
| Тип | REG_SZ (String) |
| Данные | {"pattern":"https://device.login.microsoftonline.com","filter":{"ISSUER":{"CN":"MS-Organization-Access"}}} |

Эти браузеры поддерживают аутентификацию устройств, позволяя идентифицировать устройство и проверить, соответствует ли оно политике. Если браузер работает в частном режиме, проверка устройства завершается ошибкой.

### <a name="supported-mobile-applications-and-desktop-clients"></a>Поддерживаемые мобильные приложения и классические клиенты

В политике условного доступа можно выбрать **мобильные приложения и настольные клиенты** в качестве клиентского приложения.

![Управление доступом поддерживаемых мобильных приложений или классических клиентов](./media/technical-reference/06.png)

Этот параметр влияет на попытки доступа, предпринимаемые из следующих мобильных приложений и классических клиентов.

| Клиентские приложения | Целевая служба | платформа |
| --- | --- | --- |
| Приложение Dynamics CRM | Dynamics CRM | Windows 10, Windows 8.1, iOS и Android |
| Приложения Почта, Календарь и Люди, Outlook 2016, Outlook 2013 (с современной аутентификацией)| Office 365 Exchange Online | Windows 10 |
| MFA и политика расположения для приложений Политики на основе устройств не поддерживаются.| Все службы приложения "Мои приложения"| Android и iOS |
| Microsoft Teams Services — контролируют все службы, которые поддерживают Microsoft Teams, и все их клиентские приложения: для Windows Desktop, iOS, Android, WP, а также веб-клиент. | Microsoft Teams | Windows 10, Windows 8.1, Windows 7, iOS, Android и macOS |
| Приложения Office 2016, Office 2013 (с современной проверкой подлинности), клиент синхронизации OneDrive (см. [заметки](https://support.office.com/en-US/article/Azure-Active-Directory-conditional-access-with-the-OneDrive-sync-client-on-Windows-028d73d7-4b86-4ee0-8fb7-9a209434b04e)) | Office 365 SharePoint Online | Windows 8.1, Windows 7 |
| Приложения Office 2016, универсальные приложения Office, Office 2013 (с современной проверкой подлинности), клиент синхронизации OneDrive (см. [заметки](https://support.office.com/en-US/article/Azure-Active-Directory-conditional-access-with-the-OneDrive-sync-client-on-Windows-028d73d7-4b86-4ee0-8fb7-9a209434b04e)); поддержка групп Office и SharePoint ожидается в будущем | Office 365 SharePoint Online | Windows 10 |
| Office 2016 (только Word, Excel, PowerPoint, OneNote). Поддержку OneDrive для бизнеса планируется реализовать в будущем.| Office 365 SharePoint Online| MacOS|
| Office 2019| Office 365 SharePoint Online | Windows 10, macOS |
| Мобильные приложения Office | Office 365 SharePoint Online | Android, iOS |
| Приложение Office Yammer | Office 365 Yammer | Windows 10, iOS, Android |
| Outlook 2019 | Office 365 SharePoint Online | Windows 10, macOS |
| Outlook 2016 (Office для macOS) | Office 365 Exchange Online | MacOS |
| Outlook 2016, Outlook 2013 (с современной проверкой подлинности), Skype для бизнеса (с современной проверкой подлинности) | Office 365 Exchange Online | Windows 8.1, Windows 7 |
| Приложение Outlook Mobile | Office 365 Exchange Online | Android, iOS |
| Приложение Power BI | служба Power BI | Windows 10, Windows 8.1, Windows 7, Android и iOS |
| Skype для бизнеса | Office 365 Exchange Online| Android, iOS |
| Приложение Visual Studio Team Services | Visual Studio Team Services | Windows 10, Windows 8.1, Windows 7, iOS и Android |

## <a name="support-for-legacy-authentication"></a>Поддержка устаревших методов проверки подлинности

Выберите вариант **Другие клиенты**, чтобы указать условие, которое влияет на приложения, использующие обычную проверку подлинности с протоколами электронной почты IMAP, MAPI, POP, SMTP, и приложения Office более ранних версий, которые не используют современные методы проверки подлинности.  

![Другие клиенты](./media/technical-reference/11.png)

Дополнительные сведения см. в разделе [Клиентские приложения](conditions.md#client-apps).

## <a name="approved-client-app-requirement"></a>Требование утвержденного клиентского приложения

В политике условного доступа можно потребовать, чтобы попытка доступа к выбранным облачным приложениям выполнялась из утвержденного клиентского приложения. 

![Управление доступом утвержденных клиентских приложений](./media/technical-reference/21.png)

Этот параметр применяется к следующим клиентским приложениям.

- Microsoft Azure Information Protection
- Microsoft Bookings
- Microsoft Кортана
- Microsoft Dynamics 365
- Microsoft Edge
- Microsoft Excel
- Microsoft Flow
- Microsoft Intune Managed Browser
- Microsoft Invoicing
- Microsoft Kaizala
- Microsoft Launcher
- Microsoft OneDrive
- Microsoft OneNote
- Microsoft Outlook
- Планировщик (Майкрософт);
- Microsoft PowerApps
- Microsoft Power BI
- Microsoft PowerPoint
- Microsoft SharePoint
- Microsoft Skype для бизнеса
- Microsoft StaffHub
- Microsoft Stream
- Microsoft Teams
- Microsoft To-Do
- Microsoft Visio
- Microsoft Word
- Microsoft Yammer

**Примечания**

- Утвержденные клиентских приложения поддерживают функцию управления мобильными приложениями Intune.
- Функция **Требовать утвержденное клиентское приложение**:
   - поддерживает только iOS и Android для [условия платформы устройства](#device-platform-condition);

## <a name="app-protection-policy-requirement"></a>Требование политики защиты приложений 

Чтобы получить доступ к выбранным облачным приложениям, в политике условного доступа можно потребовать, чтобы политика защиты приложений присутствовала в клиентском приложении. 

![Управление доступом с помощью политики защиты приложений](./media/technical-reference/22.png)

Этот параметр применяется к следующим клиентским приложениям.

- Microsoft Кортана
- Microsoft OneDrive
- Microsoft Outlook
- Планировщик (Майкрософт);

**Примечания**

- Приложения для политики защиты приложений поддерживают функцию управления мобильными приложениями Intune с защитой политики.
- Требования к **политике защиты приложений** :
    - поддерживает только iOS и Android для [условия платформы устройства](#device-platform-condition);

## <a name="next-steps"></a>Дальнейшие действия

- Общие сведения о условном доступе см [. в разделе что такое условный доступ в Azure Active Directory?](../active-directory-conditional-access-azure-portal.md)
- Если вы готовы к настройке политик условного доступа в среде, ознакомьтесь с рекомендациями [по условному доступу в Azure Active Directory](best-practices.md).

<!--Image references-->
[1]: ./media/technical-reference/01.png
