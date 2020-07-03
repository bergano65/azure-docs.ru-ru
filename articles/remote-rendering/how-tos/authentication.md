---
title: Аутентификация
description: Описание принципа работы проверки подлинности
author: florianborn71
ms.author: flborn
ms.date: 02/12/2019
ms.topic: how-to
ms.custom: has-adal-ref
ms.openlocfilehash: 34874e01b526a4d88ff4402edb0c3771c75a3726
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/12/2020
ms.locfileid: "83195308"
---
# <a name="configure-authentication"></a>Настройка аутентификации

Удаленная визуализация Azure использует тот же механизм проверки подлинности, что и [Пространственные привязки Azure (ASA)](https://docs.microsoft.com/azure/spatial-anchors/concepts/authentication?tabs=csharp). Клиенты должны задать *AccountKey*, *AuthenticationToken*или *AccessToken* для успешного вызова интерфейсов API-интерфейса. *AccountKey* можно получить на вкладке "ключи" для учетной записи удаленной подготовки к просмотру на портал Azure. *AuthenticationToken* — это маркер Azure AD, который можно получить с помощью [библиотеки ADAL](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries). *AccessToken* — это токен MR, который можно получить из службы маркеров безопасности (STS) в смешанной реальности Azure.

## <a name="authentication-for-deployed-applications"></a>Проверка подлинности для развернутых приложений

 Использование ключей учетной записи рекомендуется для быстрой адаптации, но только во время разработки или создания прототипов. Настоятельно рекомендуется не поставлять приложение в рабочую среду, используя внедренный ключ учетной записи, а вместо этого использовать подходы проверки подлинности Azure AD на основе пользователей или служб.

 Проверка подлинности Azure AD описана в разделе [Проверка подлинности пользователя Azure AD](https://docs.microsoft.com/azure/spatial-anchors/concepts/authentication?tabs=csharp#azure-ad-user-authentication) в службе [пространственных привязок Azure (ASA)](https://docs.microsoft.com/azure/spatial-anchors/) .

## <a name="role-based-access-control"></a>Управление доступом на основе ролей

Для управления уровнем доступа, предоставляемого приложениям, службам или пользователям Azure AD вашей службы, были созданы следующие роли для назначения в учетных записях удаленной подготовки Azure.

* **Администратор удаленной подготовки отчетов**. предоставляет пользователю возможность преобразования, управления сеансами, подготовки к просмотру и диагностики для удаленной подготовки к просмотру Azure.
* **Клиент удаленной отрисовки**: предоставляет пользователю возможность управления сеансами, отрисовкой и диагностикой для удаленной подготовки к просмотру Azure.

## <a name="next-steps"></a>Дальнейшие действия

* [Создание учетной записи](create-an-account.md)
* [Использование интерфейсов API интерфейса Azure для проверки подлинности](frontend-apis.md)
* [Примеры скриптов PowerShell](../samples/powershell-example-scripts.md)
