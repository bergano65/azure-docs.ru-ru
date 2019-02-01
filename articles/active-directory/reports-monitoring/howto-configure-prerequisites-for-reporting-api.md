---
title: Предварительные требования для доступа к API отчетов Azure Active Directory | Документация Майкрософт
description: Узнайте о предварительных требованиях для доступа к API отчетов Azure AD
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: ''
ms.assetid: ada19f69-665c-452a-8452-701029bf4252
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: ae2ed2ed2f82b3f01643db7603961c83f1723ed1
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55154972"
---
# <a name="prerequisites-to-access-the-azure-active-directory-reporting-api"></a>Предварительные требования для доступа к API отчетов Azure Active Directory

[API-интерфейсы отчетов Azure Active Directory (Azure AD)](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-reports-and-events-preview) предоставляют программный доступ к данным с помощью набора API-интерфейсов на базе REST. Эти интерфейсы API можно вызвать, используя различные языки и инструменты программирования.

API отчетов использует [OAuth](https://msdn.microsoft.com/library/azure/dn645545.aspx) для авторизации доступа к веб-API.

Чтобы подготовить доступ к API отчетов, сделайте следующее:

1. [Назначение ролей](#assign-roles)
2. [Регистрация приложения](#register-an-application)
3. [Предоставление разрешений](#grant-permissions)
4. [Сбор параметров конфигурации](#gather-configuration-settings)

## <a name="assign-roles"></a>Назначение ролей

Чтобы получить доступ к данным отчетов через API, необходима одна из следующих ролей:

- Чтение данных безопасности

- Администратор безопасности

- глобального администратора;


## <a name="register-an-application"></a>Регистрация приложения

Приложение необходимо зарегистрировать, даже если вы обращаетесь к API отчетов с помощью сценария. Это позволит получить **идентификатор приложения**, необходимый для вызова авторизации, и ваш код сможет получать маркеры.

Чтобы настроить для каталога доступ к API отчетов AAD, необходимо войти на [портал Azure](https://portal.azure.com) с учетной записью администратора Azure, которой также назначена роль участника каталога **глобального администратора** в клиенте AAD.

> [!IMPORTANT]
> В приложениях, запущенных от имени учетных данных с привилегиями администратора, доступно много возможностей. Поэтому храните в надежном месте идентификатор приложения и секретные учетные данные.
> 

**Чтобы зарегистрировать приложение AAD, сделайте следующее:**

1. На [портале Azure](https://portal.azure.com) в области навигации слева щелкните **Azure Active Directory**.
   
    ![Регистрация приложения](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. На странице **Azure Active Directory** щелкните **Регистрация приложений**.

    ![Регистрация приложения](./media/howto-configure-prerequisites-for-reporting-api/02.png) 

3. На странице **Регистрация приложений** щелкните **Регистрация нового приложения**.

    ![Регистрация приложения](./media/howto-configure-prerequisites-for-reporting-api/03.png)

4. На странице **Создание** выполните следующие действия.

    ![Регистрация приложения](./media/howto-configure-prerequisites-for-reporting-api/04.png)

    a. В текстовом поле **Имя** введите `Reporting API application`.

    b. В качестве **типа приложения** выберите **Веб-приложение или API**.

    c. В текстовом поле **URL-адрес входа** введите `https://localhost`.

    d. Нажмите кнопку **Создать**. 


## <a name="grant-permissions"></a>Предоставление разрешений 

В зависимости от API, к которому вы хотите получить доступ, приложению необходимо предоставить следующие разрешения:  

| API | Разрешение |
| --- | --- |
| Microsoft Azure Active Directory | Прочитать данные каталога |
| Microsoft Graph | Прочитать все данные журнала аудита |


![Регистрация приложения](./media/howto-configure-prerequisites-for-reporting-api/36.png)

В следующем разделе перечислены шаги для получения доступа к обоим API. Если вам не нужен доступ к данным API, вы можете пропустить эти шаги.

**Вот как можно предоставить приложению разрешения на использование API.**

1. Выберите приложение на странице **Регистрация приложений**, а затем щелкните **Параметры**. 

    ![Регистрация приложения](./media/howto-configure-prerequisites-for-reporting-api/05.png)

2. На странице **Параметры** щелкните **Необходимые разрешения**. 

    ![Регистрация приложения](./media/howto-configure-prerequisites-for-reporting-api/06.png)

3. На странице **Необходимые разрешения** в списке **API** щелкните **Windows Azure Active Directory**. 

    ![Регистрация приложения](./media/howto-configure-prerequisites-for-reporting-api/07.png)

4. На странице **Разрешение доступа** выберите параметр **Чтение данных каталога** и отмените выбор параметра **Sign in and read user profile** (Вход и чтение профиля пользователя). 

    ![Регистрация приложения](./media/howto-configure-prerequisites-for-reporting-api/08.png)

5. На панели инструментов вверху щелкните **Сохранить**.

    ![Регистрация приложения](./media/howto-configure-prerequisites-for-reporting-api/15.png)

6. На странице **Необходимые разрешения** на панели инструментов вверху нажмите кнопку **Добавить**.

    ![Регистрация приложения](./media/howto-configure-prerequisites-for-reporting-api/32.png)

7. На странице **Добавить доступ через API** щелкните **Выбор API**.

    ![Регистрация приложения](./media/howto-configure-prerequisites-for-reporting-api/31.png)

8. На странице **Select an API** (Выбор API) щелкните **Microsoft Graph**, а затем нажмите кнопку **Выбрать**.

    ![Регистрация приложения](./media/howto-configure-prerequisites-for-reporting-api/33.png)

9. На странице **Разрешение доступа** выберите **Read all audit log data** (Прочитать все данные журнала аудита), а затем нажмите кнопку **Выбрать**.  

    ![Регистрация приложения](./media/howto-configure-prerequisites-for-reporting-api/34.png)

10. На странице **Добавить доступ через API** щелкните **Готово**.  

11. На странице **Необходимые разрешения** на панели инструментов вверху щелкните **Предоставить разрешения** и выберите **Да**.

    ![Регистрация приложения](./media/howto-configure-prerequisites-for-reporting-api/17.png)


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
Чтобы получить секретный ключ клиента приложения, необходимо создать ключ и сохранить его значение при сохранении нового ключа, так как это значение невозможно получить позже.

**Вот как можно получить секрет клиента приложения.**

1. На [портале Azure](https://portal.azure.com) в области навигации слева щелкните **Azure Active Directory**.
   
    ![Регистрация приложения](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2.  Выберите свое приложение на странице **Регистрация приложений**.

3. На странице приложения на панели инструментов в верхней области щелкните **Параметры**. 

    ![Регистрация приложения](./media/howto-configure-prerequisites-for-reporting-api/05.png)

4. На странице **Параметры** в разделе **Доступ через API** щелкните **Ключи**. 

    ![Регистрация приложения](./media/howto-configure-prerequisites-for-reporting-api/12.png)

5. На странице **Ключи** выполните следующие действия.

    ![Регистрация приложения](./media/howto-configure-prerequisites-for-reporting-api/14.png)

    a. В текстовом поле **Описание** введите `Reporting API`.

    b. Для параметра **Срок действия истекает** выберите значение **Через 2 года**.

    c. Выберите команду **Сохранить**.

    d. Скопируйте значение ключа.

## <a name="troubleshoot-errors-in-the-reporting-api"></a>Устранение ошибок в API отчетов

В этом разделе перечислены распространенные сообщения об ошибках, с которыми вы можете столкнуться при доступе к отчетам о действиях с помощью API MS Graph, а также меры по их устранению.

### <a name="500-http-internal-server-error-while-accessing-microsoft-graph-v2-endpoint"></a>500 HTTP internal server error while accessing Microsoft Graph V2 endpoint (Внутренняя ошибка сервера 500 HTTP при обращении к конечной точке Microsoft Graph версии 2)

Конечная точка Microsoft Graph версии 2 сейчас не поддерживается, для доступа к журналам действий используйте конечную точку Microsoft Graph версии 1.

### <a name="error-failed-to-get-user-roles-from-ad-graph"></a>Ошибка: Failed to get user roles from AD Graph (Не удалось получить роли пользователей из AD Graph)

Это сообщение об ошибке может появиться при попытке обращения ко входам с помощью песочницы Graph. Войдите в учетную запись, используя обе кнопки входа в пользовательском интерфейсе песочницы Graph, как показано на рисунке ниже. 

![Песочница Graph](./media/troubleshoot-graph-api/graph-explorer.png)

### <a name="error-failed-to-do-premium-license-check-from-ad-graph"></a>Ошибка: Failed to do premium license check from AD Graph (Не удалось проверить лицензию уровня "Премиум" от AD Graph) 

Если при попытке обращения ко входам с помощью песочницы Graph появляется это сообщение об ошибке, выберите **Изменить разрешения** под своей учетной записью на левой панели навигации, а затем выберите **Tasks.ReadWrite** и **Directory.Read.All**. 

![Пользовательский интерфейс для изменения разрешений](./media/troubleshoot-graph-api/modify-permissions.png)


### <a name="error-neither-tenant-is-b2c-or-tenant-doesnt-have-premium-license"></a>Ошибка: Neither tenant is B2C or tenant doesn't have premium license (Клиент не относится к типу B2C или у него нет лицензии уровня "Премиум")

Для доступа к отчетам о входе требуется лицензия Azure Active Directory Premium 1 (P1). Если вы видите это сообщение об ошибке при обращении ко входам, убедитесь, что клиент лицензируется по лицензии Azure AD P1.

### <a name="error-user-is-not-in-the-allowed-roles"></a>Ошибка: User is not in the allowed roles (Пользователя нет в списке разрешенных ролей) 

Если вы видите это сообщение об ошибке при обращении к журналам аудита или входам с помощью API, убедитесь, что ваша учетная запись является частью роли **Читатель безопасности** или **Читатель отчета** в клиенте Azure Active Directory. 

### <a name="error-application-missing-aad-read-directory-data-permission"></a>Ошибка: Application missing AAD "Read directory data" permission (У приложения нет разрешения "Чтение данных каталога" AAD) 

Выполните шаги, описанные в разделе [Предварительные требования для доступа к API отчетов Azure Active Directory](howto-configure-prerequisites-for-reporting-api.md), чтобы убедиться в наличии подходящего набора разрешений для приложения. 

### <a name="error-application-missing-msgraph-api-read-all-audit-log-data-permission"></a>Ошибка: Application missing MSGraph API "Read all audit log data" permission (У приложения нет разрешения "Чтение всех данных журнала аудита" API MS Graph)

Выполните шаги, описанные в разделе [Предварительные требования для доступа к API отчетов Azure Active Directory](howto-configure-prerequisites-for-reporting-api.md), чтобы убедиться в наличии подходящего набора разрешений для приложения. 

## <a name="next-steps"></a>Дополнительная информация

* [Получение данных с помощью API отчетов Azure Active Directory с сертификатами](tutorial-access-api-with-certificates.md)
* [Справочник по API аудита](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) 
* [Справочник по API отчетов о действиях при входе](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)
