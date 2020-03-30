---
title: Зарегистрируйте заявку SaaS Лазурный рынок
description: Объясняет, как зарегистрировать приложение SaaS с помощью портала Azure.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: dsindona
ms.openlocfilehash: 9c20fe34e108de95a34aabea56390e8a6f0d858f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80275719"
---
# <a name="register-a-saas-application"></a>Регистрация приложения SaaS

В этой статье объясняется, как зарегистрировать приложение SaaS с помощью портала Microsoft [Azure.](https://portal.azure.com/)  После успешной регистрации вы получите маркер безопасности Azure Active Directory (Azure AD), который можно использовать для доступа к AAP SaaS Fulfillment.  Для получения дополнительной информации об Azure AD [см.](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios)


## <a name="service-to-service-authentication-flow"></a>Поток аутентификации от обслуживания

На следующей диаграмме показан поток действий при подписке нового клиента, а также этапы, на которых используются эти API:

![Процесс использования API для предложения SaaS](./media/saas-offer-publish-api-flow-v1.png)

Azure не налагает никаких ограничений на аутентификацию, которую служба SaaS предоставляет своим пользователям. Однако аутентификация с помощью AAS Fulfillment AIS выполняется с помощью маркера безопасности Azure AD, обычно получаемого путем регистрации приложения SaaS через портал Azure. 


## <a name="register-an-azure-ad-secured-app"></a>Регистрация приложения с защитой AD Azure

Все приложения, которые будут использовать возможности Azure AD, сначала следует зарегистрировать в клиенте Azure AD. Этот процесс регистрации включает в себя предоставление Azure AD сведений о приложении (например, URL-адрес, по которому расположено приложение, URL-адрес для отправки ответов после проверки подлинности пользователя, идентифицирующий приложение код URI и др.).  Чтобы зарегистрировать новое приложения с помощью портала Azure, сделайте следующее:

1.  Войдите на [портал Azure](https://portal.azure.com/).
2.  Если ваша учетная запись дает вам доступ к более чем одному, щелкните учетную запись в правом верхнем углу и установите сеанс портала желаемому арендатору Azure AD.
3.  В области навигации слева щелкните службу **Azure Active Directory**, а затем выберите **Регистрация приложений** и щелкните **Регистрация нового приложения**.

    ![Регистрации приложений AD для SaaS](./media/saas-offer-app-registration-v1.png)

4.  На странице создания введите сведения о регистрации приложения.
    -   **Имя**. Введите понятное имя для приложения.
    -   **Тип приложения**: 
        - Выберите **Native** для [клиентских приложений,](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#client-application) которые устанавливаются локально на устройстве. Этот параметр используется для общедоступных [собственных клиентов](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#native-client) OAuth.
        - Выберите **веб-приложение / API** для [клиентских приложений](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#client-application) и [приложений ресурса/API,](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#resource-server) которые установлены на защищенном сервере. Эта настройка используется для конфиденциальных [веб-клиентов](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#web-client) OAuth и публичных [клиентов,базирующихся с пользовательскими агентами.](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#user-agent-based-client)
        То же приложение может также предоставлять клиент и ресурс или API.
    -   **URL-адрес входа**. Для веб-приложений и API укажите основной URL-адрес приложения. Например, **http://localhost:31544** может быть URL-адрес веб-приложения, работая на локальной машине. Пользователи будут использовать этот URL-адрес для входа в приложение веб-клиента.
    -   **URI перенаправления**. Для приложений "Машинный код" укажите универсальный код ресурса (URI), который Azure AD будет использовать для возвращения токенов ответа. Например, **http://MyFirstAADApp**введите значение, конкретное для приложения.

        ![Регистрации приложений AD для SaaS](./media/saas-offer-app-registration-v1-2.png)

        Для конкретных примеров для веб-приложений или нативных приложений ознакомьтесь с настройками быстрого запуска, которые доступны в разделе *Get Started* [в руководстве разработчиков Azure AD.](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide)

5.  По завершении нажмите кнопку **Создать**. Azure AD присваивает приложению уникальный *идентификатор приложения,* и вы\'повторно забираетесь на главную страницу регистрации приложения.\' В зависимости от того, является ли ваше приложение веб-приложением или собственным приложением, будут предоставлены различные параметры для добавления дополнительных возможностей в приложение.

>[!Note]
>По умолчанию в только что зарегистрированное приложение могут входить только пользователи того же клиента.


## <a name="using-the-azure-ad-security-token"></a>Использование маркера безопасности Azure AD

После регистрации приложения можно запрограммировать маркер безопасности Azure AD.  Ожидается, что издатель использует этот маркер и сделает запрос на его решение.  При использовании различных AIS выполнения параметр маркерного запроса находится в URL, когда пользователь перенаправляется на веб-сайт SaaS из Azure.  Этот маркер действителен только в течение одного часа.  Кроме того, перед его использованием URL должен декодировать значение маркера из браузера.

Для получения дополнительной информации об [Azure Active Directory access tokens](https://docs.microsoft.com/azure/active-directory/develop/access-tokens)этих токенах см.


### <a name="get-a-token-based-on-the-azure-ad-app"></a>Получение токена для приложения Azure AD

Метод HTTP

`POST`

*Request URL (URL-адрес запроса)*

**https://login.microsoftonline.com/*(tenantId)*/oauth2/token**

*Параметр универсального кода ресурса*

|  **Название параметра**  | **Обязательно**  | **Описание**                               |
|  ------------------  | ------------- | --------------------------------------------- |
| tenantId             | True          | Идентификатор клиента для зарегистрированного приложения AAD   |
|  |  |  |


*Заголовок запроса*

|  **Имя заголовка**  | **Обязательно** |  **Описание**                                   |
|  --------------   | ------------ |  ------------------------------------------------- |
|  Content-Type     | True         | Связанный с этим запросом тип содержимого. Значение по умолчанию — `application/x-www-form-urlencoded`.  |
|  |  |  |


*Запрос тела*

| **Имя свойства**   | **Обязательно** |  **Описание**                                                          |
| -----------------   | -----------  | ------------------------------------------------------------------------- |
|  Grant_type         | True         | Тип предоставления разрешения. Значение по умолчанию — `client_credentials`.                    |
|  Client_id          | True         |  Идентификатор клиента или приложения, связанный с приложением Azure AD.                  |
|  client_secret      | True         |  Пароль, связанный с приложением Azure AD.                               |
|  Ресурс           | True         |  Целевой ресурс, для которого запрашивается токен. Значение по умолчанию — `62d94f6c-d599-489b-a797-3e10e42fbe22`. |
|  |  |  |


*Ответ*

|  **Название**  | **Тип**       |  **Описание**    |
| ---------- | -------------  | ------------------- |
| 200 ОК    | TokenResponse  | Запрос успешно выполнен   |
|  |  |  |

*TokenResponse*

Пример токена ответа.

``` json
  {
      "token_type": "Bearer",
      "expires_in": "3600",
      "ext_expires_in": "0",
      "expires_on": "15251…",
      "not_before": "15251…",
      "resource": "62d94f6c-d599-489b-a797-3e10e42fbe22",
      "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImlCakwxUmNxemhpeTRmcHhJeGRacW9oTTJZayIsImtpZCI6ImlCakwxUmNxemhpeTRmcHhJeGRacW9oTTJZayJ9…"
  }               
```


## <a name="next-steps"></a>Дальнейшие действия

Ваше приложение с защитой AD, защищенное AD, теперь может использовать [API-версию SaaS Fulfillment API.](./pc-saas-fulfillment-api-v2.md)
