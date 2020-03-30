---
title: Методы аутентификации Карты Microsoft Azure
description: В этой статье вы узнаете об активном каталоге Azure (Azure AD) и общей аутентификации ключей. Оба используются для служб Microsoft Azure Maps. Узнайте, как получить ключ подписки Azure Maps.
author: philmea
ms.author: philmea
ms.date: 01/28/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 21d29cba85adfc147ec9deb6ab362a5da943bf10
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335702"
---
# <a name="authentication-with-azure-maps"></a>Аутентификация в Azure Maps

Azure Maps поддерживает два способа проверки подлинности запросов: общая аутентификация ключей и активная проверка каталога Azure. В этой статье объясняются эти методы проверки подлинности, которые помогут вам в реализации служб Azure Maps.

> [!NOTE]
> Чтобы улучшить безопасную связь с Azure Maps, мы поддерживаем безопасность уровня транспорта (TLS) 1.2, и мы упускаем поддержку TLS 1.0 и 1.1. Чтобы избежать перерывов в обслуживании, **обновите серверы и приложения для использования TLS 1.2 до 2 апреля 2020 года.**  Если в настоящее время вы используете TLS 1.x, оцените готовность TLS 1.2 и разработайте план миграции с тестированием, описанным в [решении проблемы TLS 1.0.](https://docs.microsoft.com/security/solving-tls1-problem)

## <a name="shared-key-authentication"></a>Аутентификация на основе общего ключа

 Первичные и вторичные ключи генерируются после создания учетной записи Azure Maps. При вызове Azure Maps с помощью общей аутентификации ключей рекомендуется использовать основной ключ в качестве ключа подписки. Проверка подлинности общих ключей передает ключ, генерируемый учетной записью Azure Maps, в службу Azure Maps. Для каждого запроса в службы Azure Maps добавьте *ключ подписки* в качестве параметра к URL-адресу. Вторичный ключ может быть использован в таких сценариях, как изменение ключа.  

Информацию о просмотре ключей можно получить на портале Azure, смотрите [в управлении аутентификацией.](https://aka.ms/amauthdetails)

> [!Tip]
> Мы рекомендуем регулярно генерировать новые ключи. Вы обеспечены двумя ключами, так что вы можете поддерживать связь с одним ключом при регенерации другого. При регенерации ключей необходимо обновлять любые приложения, которые получают доступ к вашей учетной записи, с помощью новых ключей.

## <a name="authentication-with-azure-active-directory-preview"></a>Аутентификация с помощью Azure Active Directory (предварительная версия)

Azure Maps теперь предлагает проверку подлинности запросов для служб Azure Maps с помощью [Active Directory Azure (Azure AD).](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) Azure AD обеспечивает проверку подлинности на основе идентификации, включая [элементы управления доступом на основе ролей (RBAC).](https://docs.microsoft.com/azure/role-based-access-control/overview) RBAC используется для предоставления ресурсам Azure Maps на уровне пользователя, группового или уровня приложений. В следующих разделах рассматриваются концепции и компоненты интеграции Azure Maps с Azure AD.

## <a name="authentication-with-oauth-access-tokens"></a>Аутентификация с помощью токенов доступа OAuth

Azure Maps принимает токены доступа **OAuth 2.0** для клиентов Azure AD с подпиской Azure, которая содержит учетную запись Azure Maps. Azure Maps также принимает токены для:

* Пользователи Azure AD
* Партнерские приложения, которые используют разрешения, делегированные пользователями
* Управляемые удостоверения для ресурсов Azure

Azure Maps создает *уникальный идентификатор (идентификатор клиента)* для каждой учетной записи Azure Maps. Вы можете запросить токены из Azure AD, когда объедините этот идентификатор клиента с дополнительными параметрами. Чтобы запросить маркер, укажите значения в следующей таблице на основе среды Azure.

| Среда Azure   | Конечная точка маркера Azure AD |
| --------------------|-------------------------|
| Azure Public        | https://login.microsoftonline.com |
| Azure для государственных организаций    | https://login.microsoftonline.us |


Дополнительные сведения о настройке Azure AD и отправке запросов на получение токенов для Azure Maps см. в статье об [управлении аутентификацией](https://docs.microsoft.com/azure/azure-maps/how-to-manage-authentication).

Общие сведения об отправке запросов на получение токенов из Azure AD см. в статье [Что такое проверка подлинности?](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios)

## <a name="request-azure-map-resources-with-oauth-tokens"></a>Запрос ресурсов Azure Maps с помощью токенов OAuth

После получения маркера Azure AD Azure Maps отправляет запрос со следующим набором требуемых заголовков запросов:

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

В Azure AD используйте RBAC для управления доступом к защищенным ресурсам. Напишите учетную запись Azure Maps и зарегистрируйте aD-арендатор Azure Maps Azure AD. Azure Maps поддерживает контроль доступа к прочитанным для отдельных пользователей, групп, приложений, ресурсов Azure и служб Azure с помощью управляемых идентификаторов ресурсов Azure. На странице портала Azure Maps можно настроить RBAC для выбранных ролей.

![Читатель данных Azure Maps (предварительная версия)](./media/azure-maps-authentication/concept.png)

Сведения о просмотре параметров RBAC см. в статье [Управление аутентификацией в Azure Maps](https://aka.ms/amrbac).

## <a name="managed-identities-for-azure-resources-and-azure-maps"></a>Управляемые удостоверения для ресурсов Azure и Azure Maps

[Управляемые идентификаторы ресурсов Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) предоставляют службам Azure автоматически управляемый идентификатор, который может быть разрешен для доступа к службам Azure Maps. Некоторые примеры управляемых идентификаторов включают: службу приложений Azure, функции Azure и виртуальные машины Azure.

## <a name="next-steps"></a>Дальнейшие действия

* Дополнительные сведения об аутентификации приложения в Azure AD и Azure Maps см. в статье об [управлении аутентификацией](https://docs.microsoft.com/azure/azure-maps/how-to-manage-authentication).

* Дополнительные сведения об аутентификации Map Control в Azure Maps и Azure AD см. в статье [Использование библиотеки Map Control в службе Azure Maps](https://aka.ms/amaadmc).
