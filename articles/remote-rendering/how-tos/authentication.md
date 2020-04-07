---
title: Аутентификация
description: Объясняет, как работает аутентификация
author: florianborn71
ms.author: flborn
ms.date: 02/12/2019
ms.topic: how-to
ms.openlocfilehash: fbc27f2fa3b7517151c4bcdbec5b146e83dd868e
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681640"
---
# <a name="configure-authentication"></a>Настройка аутентификации

Azure Remote Rendering использует тот же механизм аутентификации, что и [пространственные якоря Azure (ASA).](https://docs.microsoft.com/azure/spatial-anchors/concepts/authentication?tabs=csharp) Для успешного вызова AIS REST клиентам необходимо установить *AccountKey,* *AuthenticationToken*или *AccessToken.* *AccountKey* можно получить во вкладке "Ключи" для учетной записи удаленного рендеринга на портале Azure. *AuthenticationToken* — это маркер Azure AD, который можно получить с помощью [библиотеки ADAL.](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries) *AccessToken* — это токен MR, который можно получить в службе доступа к теме Azure Mixed Reality Sken Service (STS).

## <a name="authentication-for-deployed-applications"></a>Аутентификация для развернутых приложений

 Использование ключей учетной записи рекомендуется для быстрой посадки на борт, но только при разработке/прототипировании. Настоятельно рекомендуется не отстранять приложение в производство с помощью встроенного ключа учетной записи, а вместо этого использовать подходы проверки подлинности Azure AD на основе пользовательских или сервисных служб.

 Аутентификация Azure AD описана в разделе [аутентификации пользователей Azure AD](https://docs.microsoft.com/azure/spatial-anchors/concepts/authentication?tabs=csharp#azure-ad-user-authentication) [службы Пространственных якорей Azure Spatial Anchors (ASA).](https://docs.microsoft.com/azure/spatial-anchors/)

## <a name="role-based-access-control"></a>Управление доступом на основе ролей

Чтобы контролировать уровень доступа к приложениям, службам или пользователям Azure AD для ваших служб, были созданы следующие роли, которые вы могли назначить по мере необходимости учетным записям удаленной визуализации Azure:

* **Удаленный администратор рендеринга**: предоставляет пользователю возможности преобразования, управления сеансами, визуализации и диагностики для удаленного рендеринга Azure.
* **Удаленный клиент рендеринга**: предоставляет пользователю возможности управления сеансом, визуализации и диагностики для удаленного рендеринга Azure.

## <a name="next-steps"></a>Дальнейшие действия

* [Создание учетной записи](create-an-account.md)
* [Использование AIS Azure Frontend для проверки подлинности](frontend-apis.md)
* [Пример скриптов PowerShell](../samples/powershell-example-scripts.md)
