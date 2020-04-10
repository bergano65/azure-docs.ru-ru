---
title: Предпосылки для API представления активных каталогов Azure (API) Документы Майкрософт
description: Узнайте о предварительных требованиях для доступа к API отчетов Azure AD
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: ada19f69-665c-452a-8452-701029bf4252
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 03/04/2020
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7fd3580ca03fa49d428904c6da78fdf9cda202c7
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/09/2020
ms.locfileid: "80991268"
---
# <a name="prerequisites-to-access-the-azure-active-directory-reporting-api"></a>Предварительные требования для доступа к API отчетов Azure Active Directory

[API-интерфейсы отчетов Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-reporting-api) предоставляют программный доступ к данным с помощью набора API-интерфейсов на базе REST. Вы можете вызвать эти AA из языков программирования и инструментов.

API отчетов использует [OAuth](https://docs.microsoft.com/azure/api-management/api-management-howto-protect-backend-with-aad) для авторизации доступа к веб-API.

Чтобы подготовить доступ к API отчетов, сделайте следующее:

1. [Назначение ролей](#assign-roles)
2. [Лицензионные требования](#license-requirements)
3. [Регистрация приложения](#register-an-application)
4. [Разрешения на гранты](#grant-permissions)
5. [Сбор параметров конфигурации](#gather-configuration-settings)

## <a name="assign-roles"></a>Назначение ролей

Чтобы получить доступ к данным отчетов через API, необходима одна из следующих ролей:

- Читатель сведений о безопасности

- Администратор безопасности

- глобального администратора;

## <a name="license-requirements"></a>Лицензионные требования

Для доступа к отчетам о входе для арендатора у арендатора должна быть связанная лицензия Azure AD Premium. Лицензия Azure AD Premium P1 (или выше) необходима для доступа к отчетам о входной регистрации для любого арендатора Azure AD. Кроме того, если тип каталога — Это Azure AD B2C, отчеты о входе доступны через API без каких-либо дополнительных лицензионных требований. 


## <a name="register-an-application"></a>Регистрация приложения

Регистрация необходима, даже если вы получаете доступ к API отчетности с помощью скрипта. Регистрация дает вам **идентификатор приложения,** который необходим для вызова авторизации и позволяет вашему коду получать токены.

Чтобы настроить для каталога доступ к API отчетов AAD, необходимо войти на [портал Azure](https://portal.azure.com) с учетной записью администратора Azure, которой также назначена роль участника каталога **глобального администратора** в клиенте AAD.

> [!IMPORTANT]
> В приложениях, запущенных от имени учетных данных с привилегиями администратора, доступно много возможностей. Поэтому храните в надежном месте идентификатор приложения и секретные учетные данные.
> 

**Чтобы зарегистрировать приложение AAD, сделайте следующее:**

1. На [портале Azure](https://portal.azure.com) в области навигации слева щелкните **Azure Active Directory**.
   
    ![Регистрация приложения](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. На странице **Azure Active Directory** щелкните **Регистрация приложений**.

    ![Регистрация приложения](./media/howto-configure-prerequisites-for-reporting-api/02.png) 

3. На странице **регистрации приложений** выберите **новую регистрацию.**

    ![Регистрация приложения](./media/howto-configure-prerequisites-for-reporting-api/03.png)

4. Страница **Регистрации приложения:**

    ![Регистрация приложения](./media/howto-configure-prerequisites-for-reporting-api/04.png)

    а. В текстовом поле **Имя** введите `Reporting API application`.

    b. Для **типа поддерживаемых учетных записей**выберите **учетные записи только в этой организации.**

    c. В **url-адресе перенаправить** **веб-сообщение** введите тип `https://localhost`:

    d. Выберите **Зарегистрировать**. 


## <a name="grant-permissions"></a>Предоставить разрешения 

В зависимости от API, к которому вы хотите получить доступ, приложению необходимо предоставить следующие разрешения:  

| API | Разрешение |
| --- | --- |
| Microsoft Azure Active Directory | Прочитать данные каталога |
| Microsoft Graph | Прочитать все данные журнала аудита |


![Регистрация приложения](./media/howto-configure-prerequisites-for-reporting-api/36.png)

В следующем разделе перечислены шаги для получения доступа к обоим API. Если вам не нужен доступ к данным API, вы можете пропустить эти шаги.

**Вот как можно предоставить приложению разрешения на использование API.**


1. Выберите **разрешения API,** а затем **добавьте разрешение.** 

    ![Регистрация приложения](./media/howto-configure-prerequisites-for-reporting-api/05.png)

2. На **странице разрешений API запроса**найдите **график унаследованных API API** **Azure Active Directory Graph.** 

    ![Регистрация приложения](./media/howto-configure-prerequisites-for-reporting-api/06.png)

3. На странице **Требуемых разрешений** выберите **Разрешения приложений,** расширьте чекбокс **каталога** **Directory.ReadAll**.  Выберите **Добавить разрешения**.

    ![Регистрация приложения](./media/howto-configure-prerequisites-for-reporting-api/07.png)

4. На странице **приложения API - API Permissions** выберите **согласие админа Grant.** 

    ![Регистрация приложения](./media/howto-configure-prerequisites-for-reporting-api/08.png)

5. Примечание: **Microsoft Graph** добавляется по умолчанию во время регистрации API.

    ![Регистрация приложения](./media/howto-configure-prerequisites-for-reporting-api/15.png)

## <a name="gather-configuration-settings"></a>Сбор параметров конфигурации 

В этом разделе показано, как получить из каталога следующие параметры:

- Доменное имя
- Идентификатор клиента
- Секрет клиента

Эти значения необходимы при настройке вызовов API отчетов. 

### <a name="get-your-domain-name"></a>Получение имени домена

**Вот как можно получить доменное имя.**

1. На [портале Azure](https://portal.azure.com) в области навигации слева выберите **Azure Active Directory**.
   
    ![Регистрация приложения](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. На странице **Azure Active Directory** выберите **Имена пользовательских доменов**.

    ![Регистрация приложения](./media/howto-configure-prerequisites-for-reporting-api/09.png) 

3. Скопируйте нужное доменное имя из списка доменов.


### <a name="get-your-applications-client-id"></a>Получение идентификатора клиента приложения

**Вот как можно получить идентификатор клиента приложения.**

1. На [портале Azure](https://portal.azure.com) в области навигации слева щелкните **Azure Active Directory**.
   
    ![Регистрация приложения](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. Выберите свое приложение на странице **Регистрация приложений**.

3. Со страницы приложения перейдите к **идентификатору приложения** и выберите **Щелкните, чтобы скопировать**.

    ![Регистрация приложения](./media/howto-configure-prerequisites-for-reporting-api/11.png) 


### <a name="get-your-applications-client-secret"></a>Получение секрета клиента приложения
 Избегайте ошибок при доступе к журналам аудита или входа в систему с помощью API.

**Вот как можно получить секрет клиента приложения.**

1. На [портале Azure](https://portal.azure.com) в области навигации слева щелкните **Azure Active Directory**.
   
    ![Регистрация приложения](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2.  Выберите свое приложение на странице **Регистрация приложений**.

3.  Выберите **Сертификаты и Секреты** на странице **приложения API,** в разделе **Секреты клиента,** нажмите **кнопку «Новый секрет клиента**». 

    ![Регистрация приложения](./media/howto-configure-prerequisites-for-reporting-api/12.png)

5. На **странице добавить секрет клиента,** добавить:

    а. В текстовом поле **Описание** введите `Reporting API`.

    b. Для параметра **Срок действия истекает** выберите значение **Через 2 года**.

    c. Выберите команду **Сохранить**.

    d. Скопируйте значение ключа.

## <a name="troubleshoot-errors-in-the-reporting-api"></a>Устранение ошибок в API отчетов

В этом разделе перечислены распространенные сообщения об ошибках, с которыми можно столкнуться при доступе к отчетам о действиях с использованием API Microsoft Graph, и шаги для их разрешения.

### <a name="error-failed-to-get-user-roles-from-microsoft-graph"></a>Ошибка: Не удалось получить роли пользователей от Microsoft Graph

 Вопийте в свой аккаунт, используя обе кнопки ввески в uI Graph Explorer, чтобы избежать ошибки при попытке войти в систему С помощью Graph Explorer. 

![Песочница Graph](./media/troubleshoot-graph-api/graph-explorer.png)

### <a name="error-failed-to-do-premium-license-check-from-microsoft-graph"></a>Ошибка: Не удалось сделать премиум проверки лицензии от Microsoft Graph 

Если при попытке обращения ко входам с помощью песочницы Graph появляется это сообщение об ошибке, выберите **Изменить разрешения** под своей учетной записью на левой панели навигации, а затем выберите **Tasks.ReadWrite** и **Directory.Read.All**. 

![Пользовательский интерфейс для изменения разрешений](./media/troubleshoot-graph-api/modify-permissions.png)

### <a name="error-tenant-is-not-b2c-or-tenant-doesnt-have-premium-license"></a>Ошибка: Арендатор не B2C или арендатор не имеет премиум лицензии

Для доступа к отчетам о входе требуется лицензия Azure Active Directory Premium 1 (P1). Если вы видите это сообщение об ошибке при обращении ко входам, убедитесь, что клиент лицензируется по лицензии Azure AD P1.

### <a name="error-the-allowed-roles-does-not-include-user"></a>Ошибка: Разрешенные роли не включают пользователя. 

 Избегайте ошибок при доступе к журналам аудита или входа в систему с помощью API. Убедитесь, что ваша учетная запись является частью роли **читателя безопасности** или **чтения отчетов** в вашем арендаторе Active Directory Azure.

### <a name="error-application-missing-aad-read-directory-data-permission"></a>Error: Application missing AAD 'Read directory data' permission (Ошибка: у приложения нет разрешения "Чтение данных каталога" AAD) 

### <a name="error-application-missing-microsoft-graph-api-read-all-audit-log-data-permission"></a>Ошибка: Приложение отсутствует Microsoft Graph API 'Читать все данные журнала аудита' разрешение

Выполните последующие действия в [Предпосылках для доступа к API-приложению от](howto-configure-prerequisites-for-reporting-api.md) отчетов Active Directory, чтобы убедиться, что ваше приложение работает с правильным набором разрешений. 

## <a name="next-steps"></a>Следующие шаги

* [Получение данных с помощью API отчетов Azure Active Directory с сертификатами](tutorial-access-api-with-certificates.md)
* [Справочник по API аудита](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) 
* [Справочник по API отчетов о действиях при входе](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)
