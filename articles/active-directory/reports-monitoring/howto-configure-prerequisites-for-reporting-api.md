---
title: Предварительные требования для доступа к API отчетов Azure Active Directory | Документация Майкрософт
description: Узнайте о предварительных требованиях для доступа к API отчетов Azure AD
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: ada19f69-665c-452a-8452-701029bf4252
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: f72d15707d9f56b9e9b5a5d527d1204007c40afa
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/14/2018
ms.locfileid: "51621978"
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


## <a name="next-steps"></a>Дополнительная информация

* [Получение данных с помощью API отчетов Azure Active Directory с сертификатами](tutorial-access-api-with-certificates.md)
* [Справочник по API аудита](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) 
* [Справочник по API отчетов о действиях при входе](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)
