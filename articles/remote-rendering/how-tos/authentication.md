---
title: Аутентификация
description: Описание принципа работы проверки подлинности
author: florianborn71
ms.author: flborn
ms.date: 06/15/2020
ms.topic: how-to
ms.openlocfilehash: 8f3b144a7790c3122d59d27183b3037998ddadd1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "85565849"
---
# <a name="configure-authentication"></a>Настройка аутентификации

Удаленная визуализация Azure использует тот же механизм проверки подлинности, что и [Пространственные привязки Azure (ASA)](https://docs.microsoft.com/azure/spatial-anchors/concepts/authentication?tabs=csharp). Чтобы успешно вызывать API-интерфейсы, клиентам необходимо установить *одно* из следующих действий.

* **AccountKey**: можно получить на вкладке "ключи" для учетной записи удаленной подготовки к просмотру на портал Azure. Ключи учетной записи рекомендуются только для разработки и создания прототипов.
    ![Идентификатор учетной записи](./media/azure-account-primary-key.png)

* **AuthenticationToken**— это маркер Azure AD, который можно получить с помощью [библиотеки MSAL](https://docs.microsoft.com/azure/active-directory/develop/msal-overview). Существует несколько различных потоков, которые принимают учетные данные пользователя и используют эти учетные данные для получения маркера доступа.

* **Мракцесстокен**: — это токен MR, который может быть получен из службы маркеров безопасности (STS) в смешанной реальности Azure. Извлечение из `https://sts.mixedreality.azure.com` конечной точки с помощью вызова RESTful, аналогичного приведенному ниже вызову:

    ```rest
    GET https://sts.mixedreality.azure.com/Accounts/35d830cb-f062-4062-9792-d6316039df56/token HTTP/1.1
    Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1Ni<truncated>FL8Hq5aaOqZQnJr1koaQ
    Host: sts.mixedreality.azure.com
    Connection: Keep-Alive

    HTTP/1.1 200 OK
    Date: Tue, 24 Mar 2020 09:09:00 GMT
    Content-Type: application/json; charset=utf-8
    Content-Length: 1153
    Accept: application/json
    MS-CV: 05JLqWeKFkWpbdY944yl7A.0
    {"AccessToken":"eyJhbGciOiJSUzI1<truncated>uLkO2FvA"}
    ```

    В котором заголовок авторизации имеет следующий формат: `Bearer <Azure_AD_token>` или `Bearer <accoundId>:<accountKey>` . Первый из них является предпочтительным для безопасности. Токен, возвращенный этим вызовом RESTFUL, является маркером доступа MR.

## <a name="authentication-for-deployed-applications"></a>Проверка подлинности для развернутых приложений

Ключи учетной записи рекомендуются для быстрого создания прототипов во время разработки. Не рекомендуется поставлять приложение в рабочую среду, используя внедренный ключ учетной записи. Рекомендуемый подход заключается в использовании метода проверки подлинности Azure AD на основе пользователей или служб.

 Проверка подлинности Azure AD описана в разделе [Проверка подлинности пользователя Azure AD](https://docs.microsoft.com/azure/spatial-anchors/concepts/authentication?tabs=csharp#azure-ad-user-authentication) в службе [пространственных привязок Azure (ASA)](https://docs.microsoft.com/azure/spatial-anchors/) .

 Дополнительные сведения см [. в руководстве защита удаленной подготовки Azure и хранилища моделей — Azure Active Directory аутентификация.](../tutorials/unity/security/security.md#azure-active-directory-azure-ad-authentication)

## <a name="role-based-access-control"></a>Управление доступом на основе ролей

Для управления уровнем доступа, которому предоставлена ваша служба, используйте следующие роли при предоставлении доступа на основе ролей.

* **Администратор удаленной подготовки отчетов**. предоставляет пользователю возможность преобразования, управления сеансами, подготовки к просмотру и диагностики для удаленной подготовки к просмотру Azure.
* **Клиент удаленной отрисовки**: предоставляет пользователю возможность управления сеансами, отрисовкой и диагностикой для удаленной подготовки к просмотру Azure.

## <a name="next-steps"></a>Дальнейшие действия

* [Создание учетной записи](create-an-account.md)
* [Использование внешних API Azure для аутентификации](frontend-apis.md)
* [Примеры сценариев PowerShell](../samples/powershell-example-scripts.md)
