---
title: Методы проверки подлинности | Карты Microsoft Azure
description: В этой статье вы узнаете о Azure Active Directory (Azure AD) и проверке подлинности с помощью общего ключа. Оба используются для служб Microsoft Azure Maps. Узнайте, как получить ключ подписки Azure Maps.
author: walsehgal
ms.author: v-musehg
ms.date: 02/11/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 73c0d9f76ad92d0ef7ed0f518de5ab1f8b174c9d
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/13/2020
ms.locfileid: "77189792"
---
# <a name="authentication-with-azure-maps"></a>Аутентификация в Azure Maps

Azure Maps поддерживает два способа проверки подлинности запросов: Аутентификация с помощью общего ключа и проверка подлинности Azure Active Directory. В этой статье объясняются методы проверки подлинности, которые помогут вам реализовать Azure Maps служб.

## <a name="shared-key-authentication"></a>Аутентификация на основе общего ключа

 Первичные и вторичные ключи создаются после создания учетной записи Azure Maps. Рекомендуется использовать первичный ключ в качестве ключа подписки при вызове Azure Maps с помощью проверки подлинности с использованием общего ключа. Проверка подлинности с помощью общего ключа передает ключ, созданный учетной записью Azure Maps, в службу Azure Maps. Для каждого запроса к Azure Maps службам добавьте *ключ подписки* в качестве параметра в URL-адрес. Вторичный ключ можно использовать в таких сценариях, как изменение ключевых изменений.  

Сведения о просмотре ключей в портал Azure см. в разделе [Управление проверкой подлинности](https://aka.ms/amauthdetails).

> [!Tip]
> Мы рекомендуем регулярно генерировать новые ключи. Вы предоставляете два ключа, которые позволяют поддерживать соединения с одним ключом при повторном создании другого. При повторном создании ключей необходимо обновить все приложения, которые обращаются к вашей учетной записи, с новыми ключами.

## <a name="authentication-with-azure-active-directory-preview"></a>Аутентификация с помощью Azure Active Directory (предварительная версия)

Azure Maps теперь предлагает запросы проверки подлинности для Azure Maps служб с помощью [Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis). Azure AD обеспечивает проверку подлинности на основе удостоверений, включая [Управление доступом на основе ролей (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview). RBAC используется для предоставления доступа к Azure Maps ресурсам уровня пользователя, уровня группы или приложения. В следующих разделах обсуждаются основные понятия и компоненты интеграции Azure Maps с Azure AD.

## <a name="authentication-with-oauth-access-tokens"></a>Аутентификация с помощью токенов доступа OAuth

Azure Maps принимает токены доступа **OAuth 2.0** для клиентов Azure AD с подпиской Azure, которая содержит учетную запись Azure Maps. Azure Maps также принимает маркеры для:

* Пользователи Azure AD
* Партнерские приложения, использующие разрешения, делегированные пользователями
* Управляемые удостоверения для ресурсов Azure

Azure Maps создает *уникальный идентификатор (идентификатор клиента)* для каждой учетной записи Azure Maps. Вы можете запросить токены из Azure AD, если вы объедините этот идентификатор клиента с дополнительными параметрами. Чтобы запросить маркер, укажите значения в следующей таблице в зависимости от среды Azure.

| Среда Azure   | Конечная точка маркера Azure AD |
| --------------------|-------------------------|
| Azure Public        | https://login.microsoftonline.com |
| Azure для государственных организаций    | https://login.microsoftonline.us |


Дополнительные сведения о настройке Azure AD и отправке запросов на получение токенов для Azure Maps см. в статье об [управлении аутентификацией](https://docs.microsoft.com/azure/azure-maps/how-to-manage-authentication).

Общие сведения об отправке запросов на получение токенов из Azure AD см. в статье [Что такое проверка подлинности?](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios)

## <a name="request-azure-map-resources-with-oauth-tokens"></a>Запрос ресурсов Azure Maps с помощью токенов OAuth

После того как Azure AD получит маркер, Azure Maps отправляет запрос со следующим набором обязательных заголовков запроса:

| Заголовок запроса    |    Значение    |
|:------------------|:------------|
| x-ms-client-id    | 30d7cc….9f55|
| Авторизация     | Bearer eyJ0e….HNIVN |

> [!Note]
> `x-ms-client-id` — это GUID Azure Maps на основе учетных записей, который отображается на странице проверки подлинности службы Azure Maps.

Ниже приведен пример запроса маршрута Azure Maps, в котором используется токен OAuth.

```
GET /route/directions/json?api-version=1.0&query=52.50931,13.42936:52.50274,13.43872 
Host: atlas.microsoft.com 
x-ms-client-id: 30d7cc….9f55 
Authorization: Bearer eyJ0e….HNIVN 
```

Сведения о просмотре идентификатора клиента см. в разделе [Просмотр сведений об аутентификации](https://aka.ms/amauthdetails).

## <a name="control-access-with-rbac"></a>Управление доступом с помощью RBAC

В Azure AD используйте RBAC для управления доступом к защищенным ресурсам. Настройте учетную запись Azure Maps и зарегистрируйте Azure Maps клиент Azure AD. Azure Maps поддерживает управление доступом на чтение для отдельных пользователей Azure AD, групп, приложений, ресурсов Azure и служб Azure с помощью управляемых удостоверений для ресурсов Azure. На странице портала Azure Maps можно настроить RBAC для выбранных ролей.

![Читатель данных Azure Maps (предварительная версия)](./media/azure-maps-authentication/concept.png)

Сведения о просмотре параметров RBAC см. в статье [Управление аутентификацией в Azure Maps](https://aka.ms/amrbac).

## <a name="managed-identities-for-azure-resources-and-azure-maps"></a>Управляемые удостоверения для ресурсов Azure и Azure Maps

[Управляемые удостоверения для ресурсов Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) предоставляют службы Azure с автоматически управляемым удостоверением, которое может быть разрешено для доступа к службам Azure Maps. Примеры управляемых удостоверений: служба приложений Azure, функции Azure и виртуальные машины Azure.

## <a name="next-steps"></a>Следующие шаги

* Дополнительные сведения об аутентификации приложения в Azure AD и Azure Maps см. в статье об [управлении аутентификацией](https://docs.microsoft.com/azure/azure-maps/how-to-manage-authentication).

* Дополнительные сведения об аутентификации Map Control в Azure Maps и Azure AD см. в статье [Использование библиотеки Map Control в службе Azure Maps](https://aka.ms/amaadmc).
