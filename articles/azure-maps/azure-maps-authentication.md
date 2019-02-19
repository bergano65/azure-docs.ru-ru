---
title: Аутентификация в Azure Maps | Документация Майкрософт
description: Аутентификация для использования служб Azure Maps.
author: walsehgal
ms.author: v-musehg
ms.date: 02/12/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: b97c303433eb8fadcda51257d37447f052ce4a3b
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/12/2019
ms.locfileid: "56118847"
---
# <a name="authentication-with-azure-maps"></a>Аутентификация в Azure Maps

Службы Azure Maps поддерживают два способа аутентификации запросов. Общий ключ и Azure Active Directory (Azure AD) — это два отдельных метода для авторизации каждого отправленного в Azure Maps запроса. Подробное описание обоих методов в этой статье поможет вам реализовать собственный подход к аутентификации.

## <a name="shared-key-authentication"></a>Аутентификация на основе общего ключа

Принцип аутентификации на основе общего ключа заключается в передаче с каждым запросом в Azure Maps ключей, созданных в учетной записи Azure Maps.  При создании вашей учетной записи Azure Maps генерируются два ключа.  При каждом запросе к службам Azure Maps нужно добавить к URL-адресу ключ подписки в качестве параметра.

> [!Tip]
> Мы рекомендуем регулярно генерировать новые ключи. Вам предоставляется два ключа. Поэтому вы можете устанавливать подключения с помощью одного из них во время обновления другого. Если вы генерируете новые ключи, вам необходимо обновить данные о ключах для всех приложений, которые используют эту учетную запись.

Чтобы узнать, как просмотреть ключи, см. раздел [View authentication details](https://aka.ms/amauthdetails) (Просмотр сведений об аутентификации).

## <a name="authentication-with-azure-active-directory-preview"></a>Аутентификация с помощью Azure Active Directory (предварительная версия)

Azure Maps теперь предлагает интеграцию с [Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) для аутентификации запросов к службам Azure Maps.  Azure AD обеспечивает аутентификацию на основе удостоверений (в том числе [управление доступом на основе ролей (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview)) для предоставления пользователю или уровню приложений доступа к ресурсам Azure Maps. В этой статье разъясняются концепции и элементы интеграции Azure Maps с Azure AD.

## <a name="authentication-with-oauth-access-tokens"></a>Аутентификация с помощью токенов доступа OAuth

Azure Maps принимает токены доступа **OAuth 2.0** для клиентов Azure AD с подпиской Azure, которая содержит учетную запись Azure Maps.  Azure Maps принимает токены для таких субъектов:

* пользователи Azure AD; 
* сторонние приложения, использующие делегированные пользователями разрешения;
* Управляемые удостоверения для ресурсов Azure

Azure Maps создает `unique identifier (client ID)` для каждой учетной записи Azure Maps.  При добавлении к ИД клиента дополнительных параметров маркеры можно запросить из Azure AD, указав следующее значение:

```
https://login.microsoftonline.com
```
Дополнительные сведения о настройке Azure AD и отправке запросов на получение маркеров для Azure Maps см. в статье об [управлении аутентификацией](https://review.docs.microsoft.com/azure/azure-maps/how-to-manage-authentication).

Общие сведения об отправке запросов на маркеры из Azure AD см. в статье [Что такое проверка подлинности?](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios).

## <a name="requesting-azure-map-resources-with-oauth-tokens"></a>Запрос ресурсов Azure Maps с помощью токенов OAuth

После получения токена из Azure AD можно отправить запрос в Azure Maps с набором таких двух обязательных заголовков запросов:

| Заголовок запроса    |    Значение    |
|:------------------|:------------|
| x-ms-client-id    | 30d7cc….9f55|
| Авторизация     | Bearer eyJ0e….HNIVN |

> [!Note]
> `x-ms-client-id` — это идентификатор GUID учетной записи Azure Maps, отображаемый на странице аутентификации Azure Maps.

Ниже приведен пример запроса маршрута Azure Maps с использованием токена OAuth:

```
GET /route/directions/json?api-version=1.0&query=52.50931,13.42936:52.50274,13.43872 
Host: atlas.microsoft.com 
x-ms-client-id: 30d7cc….9f55 
Authorization: Bearer eyJ0e….HNIVN 
```

Чтобы узнать, как просмотреть ИД клиента, см. раздел [View authentication details](https://aka.ms/amauthdetails) (Просмотр сведений об аутентификации).

## <a name="control-access-with-role-based-access-control-rbac"></a>Управление доступом на основе ролей (RBAC)

Ключевой компонент Azure AD — это управление доступом к защищенным ресурсам с помощью RBAC. Когда вы создадите свою учетную запись Azure Maps и зарегистрируете приложение Azure AD для Azure Maps в своем клиенте Azure AD, вы сможете настроить управление доступом на основе ролей для пользователя, приложения или ресурса Azure на странице учетной записи Azure Maps портала. 

Сейчас Azure Maps поддерживает управление доступом на чтение для отдельных пользователей, приложений Azure AD или служб Azure через управляемые удостоверения для ресурсов Azure.

![концепция](./media/azure-maps-authentication/concept.png)

Дополнительные сведения см. в разделе [View Azure Maps role-based access control (RBAC)](https://aka.ms/amrbac) (Просмотр данных управления доступом на основе ролей (RBAC) в Azure Maps).

## <a name="managed-identities-for-azure-resources-and-azure-maps"></a>Управляемые удостоверения для ресурсов Azure и Azure Maps

[Управляемые удостоверения для ресурсов Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) предоставляют службам Azure (Службе приложений Azure, Функциям Azure, Виртуальным машинам и т. д.) автоматически управляемые удостоверения, которые можно авторизовать для доступа к службам Azure Maps.  

## <a name="next-steps"></a>Дополнительная информация

* Дополнительные сведения об аутентификации приложения в Azure AD и Azure Maps см. в статье об [управлении аутентификацией](https://review.docs.microsoft.com/azure/azure-maps/how-to-manage-authentication).

* Дополнительные сведения об аутентификации Azure Maps, Map Control и Azure AD см. в статье [Использование библиотеки Map Control в службе Azure Maps](https://aka.ms/amaadmc).