---
title: Аутентификация в Azure Maps | Документация Майкрософт
description: Аутентификация для использования служб Azure Maps.
author: walsehgal
ms.author: v-musehg
ms.date: 07/11/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: a4608d0631c9a590fdde583e399883a023275c30
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67838041"
---
# <a name="authentication-with-azure-maps"></a>Аутентификация в Azure Maps

Azure Maps поддерживает два способа аутентификации запросов: с помощью общего ключа и Azure Active Directory (Azure AD). В этой статье объясняются эти способы проверки подлинности, которые помогут вам с реализацией.

## <a name="shared-key-authentication"></a>Аутентификация на основе общего ключа

Аутентификация на основе общего ключа передает ключи, создаваемые учетной записью Azure Maps, при каждом запросе в службу Azure Maps.  При создании вашей учетной записи Azure Maps генерируются два ключа. При каждом запросе к службам Azure Maps ключ подписки нужно добавить к URL-адресу в качестве параметра.

> [!Tip]
> Мы рекомендуем регулярно генерировать новые ключи. Вам предоставляется два ключа. Поэтому вы можете устанавливать подключения с помощью одного из них во время обновления другого. Если вы генерируете новые ключи, вам необходимо обновить данные о ключах для всех приложений, которые используют эту учетную запись.

Сведения о просмотре ключей см. в разделе [Просмотр сведений об аутентификации](https://aka.ms/amauthdetails).

## <a name="authentication-with-azure-active-directory-preview"></a>Аутентификация с помощью Azure Active Directory (предварительная версия)

Azure Maps теперь предлагает интеграцию с [Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) для аутентификации запросов к службам Azure Maps. Azure AD обеспечивает проверку подлинности на основе удостоверений, включая [управления доступом на основе ролей (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview), для предоставления доступа уровня пользователя, группы уровня и уровня приложения к ресурсам службы карт Azure. Последующие разделы помогут вам понять принципы и компоненты интеграции Azure Maps с Azure AD.

## <a name="authentication-with-oauth-access-tokens"></a>Аутентификация с помощью токенов доступа OAuth

Azure Maps принимает токены доступа **OAuth 2.0** для клиентов Azure AD с подпиской Azure, которая содержит учетную запись Azure Maps. Azure Maps принимает токены для таких субъектов:

* пользователи Azure AD; 
* партнерские приложения, использующие разрешения, делегируемые пользователями;
* Управляемые удостоверения для ресурсов Azure.

Azure Maps создает *уникальный идентификатор (идентификатор клиента)* для каждой учетной записи Azure Maps. Если вы объединяете этот идентификатор клиента с дополнительными параметрами, вы можете запрашивать токены из Azure AD, указав приведенное ниже значение.

```
https://login.microsoftonline.com
```
Дополнительные сведения о настройке Azure AD и отправке запросов на получение токенов для Azure Maps см. в статье об [управлении аутентификацией](https://review.docs.microsoft.com/azure/azure-maps/how-to-manage-authentication).

Общие сведения об отправке запросов на получение токенов из Azure AD см. в статье [Что такое проверка подлинности?](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios)

## <a name="request-azure-map-resources-with-oauth-tokens"></a>Запрос ресурсов Azure Maps с помощью токенов OAuth

После получения токена из Azure AD можно отправить запрос в Azure Maps с набором из таких двух обязательных заголовков запросов.

| Заголовок запроса    |    Значение    |
|:------------------|:------------|
| x-ms-client-id    | 30d7cc….9f55|
| Authorization     | Bearer eyJ0e….HNIVN |

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

Azure AD позволяет управлять доступом к защищенным ресурсам с помощью RBAC. После создания учетной записи службы карт Azure и зарегистрировать приложение Azure Maps Azure AD в клиенте Azure AD, RBAC можно настроить для пользователя, группы, приложения или ресурса Azure на странице портала учетной записи службы карт Azure.

Карты Azure поддерживает управление доступом на чтение для отдельных пользователей Azure AD, группы, приложений и служб Azure с помощью управляемых удостоверений для ресурсов Azure.

![Читатель данных Azure Maps (предварительная версия)](./media/azure-maps-authentication/concept.png)

Сведения о просмотре параметров RBAC см. в статье [Управление аутентификацией в Azure Maps](https://aka.ms/amrbac).

## <a name="managed-identities-for-azure-resources-and-azure-maps"></a>Управляемые удостоверения для ресурсов Azure и Azure Maps

[Управляемые удостоверения для ресурсов Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) предоставляют службам Azure (Службе приложений Azure, Функциям Azure, Виртуальным машинам Azure и т. д.) автоматически управляемое удостоверение, которое можно авторизовать для доступа к службам Azure Maps.  

## <a name="next-steps"></a>Следующие шаги

* Дополнительные сведения об аутентификации приложения в Azure AD и Azure Maps см. в статье об [управлении аутентификацией](https://review.docs.microsoft.com/azure/azure-maps/how-to-manage-authentication).

* Дополнительные сведения об аутентификации Map Control в Azure Maps и Azure AD см. в статье [Использование библиотеки Map Control в службе Azure Maps](https://aka.ms/amaadmc).