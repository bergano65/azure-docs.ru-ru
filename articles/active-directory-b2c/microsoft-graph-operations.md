---
title: Поддерживаемые операции Microsoft Graph
titleSuffix: Azure AD B2C
description: Индекс операций Microsoft Graph, поддерживаемый для управления ресурсами Azure AD B2C, включая пользователей, потоки пользователей, поставщиков идентификационных данных, пользовательские политики, ключи политики и многое другое.
services: B2C
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/20/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 27fe1a41365d96a4179f8c659b63dc22c7b9fc93
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78184254"
---
# <a name="microsoft-graph-operations-available-for-azure-ad-b2c"></a>Операции Microsoft Graph доступны для Azure AD B2C

Следующие операции Microsoft Graph API поддерживаются для управления ресурсами Azure AD B2C, включая пользователей, поставщиков идентификационных данных, потоки пользователей, пользовательские политики и ключи политики.

Каждая ссылка в следующих разделах нацелена на соответствующую страницу в справочнике Microsoft Graph API для этой операции.

## <a name="user-management"></a>Управление пользователями

- [Список пользователей](https://docs.microsoft.com/graph/api/user-list)
- [Создание потребителя](https://docs.microsoft.com/graph/api/user-post-users)
- [Получить пользователя](https://docs.microsoft.com/graph/api/user-get)
- [Обновление пользователя](https://docs.microsoft.com/graph/api/user-update)
- [Удаление пользователя](https://docs.microsoft.com/graph/api/user-delete)

Для получения дополнительной информации об управлении учетными записями пользователей Azure AD B2C с помощью API Microsoft Graph можно [ознакомиться на учетных записях пользователей Azure AD B2C с помощью Microsoft Graph.](manage-user-accounts-graph-api.md)

## <a name="identity-providers-user-flow"></a>Поставщики идентификационных данных (поток пользователей)

Управление поставщиками идентификационных данных, доступными для пользователей в вашем арендаторе Azure AD B2C.

- [Список поставщиков идентификационных данных, зарегистрированных в арендаторе Azure AD B2C](https://docs.microsoft.com/graph/api/identityprovider-list)
- [Создание поставщика идентификационных данных](https://docs.microsoft.com/graph/api/identityprovider-post-identityproviders)
- [Получить поставщика идентификационных данных](https://docs.microsoft.com/graph/api/identityprovider-get)
- [Поставщик идентификационных данных обновления](https://docs.microsoft.com/graph/api/identityprovider-update)
- [Удаление поставщика идентификационных данных](https://docs.microsoft.com/graph/api/identityprovider-delete)

## <a name="user-flow"></a>Поток пользователя

Настройте предустановленные политики для регистрации, регистрации, комбинированной регистрации и регистрации, сбросить пароль и обновления профиля.

- [Список потоков пользователей](https://docs.microsoft.com/graph/api/identityuserflow-list)
- [Создание потока пользователей](https://docs.microsoft.com/graph/api/identityuserflow-post-userflows)
- [Получить поток пользователей](https://docs.microsoft.com/graph/api/identityuserflow-get)
- [Удаление потока пользователей](https://docs.microsoft.com/graph/api/identityuserflow-delete)

## <a name="custom-policies"></a>Пользовательские политики

Следующие операции позволяют управлять политиками Azure AD B2C Trust Framework, известными как [пользовательские политики.](custom-policy-overview.md)

- [Перечислите все политики рамок доверия, настроенные в арендаторе](https://docs.microsoft.com/graph/api/trustframework-list-trustframeworkpolicies)
- [Создание рамочной политики доверия](https://docs.microsoft.com/graph/api/trustframework-post-trustframeworkpolicy)
- [Чтение свойств существующей политики целевой базы](https://docs.microsoft.com/graph/api/trustframeworkpolicy-get)
- [Обновление или создание политики рамочной базы доверия.](https://docs.microsoft.com/graph/api/trustframework-put-trustframeworkpolicy)
- [Удаление существующей политики целевой базы](https://docs.microsoft.com/graph/api/trustframeworkpolicy-delete)

## <a name="policy-keys"></a>Ключи политики

В рамках Identity Experience Framework хранятся секреты, упомянутые в пользовательской политике для установления доверия между компонентами. Эти секреты могут быть симметричными или асимметричными ключами/ценностями. На портале Azure эти сущности отображаются как **ключи политики.**

Ресурсом верхнего уровня для ключей политики в API Microsoft Graph — [—Доверенный набор рамочных сетей.](https://docs.microsoft.com/graph/api/resources/trustframeworkkeyset) Каждый **ключ** содержит по крайней мере один **ключ.** Чтобы создать ключ, сначала создайте пустой ключ, а затем создайте ключ в клавиатуре. Вы можете создать ручной секрет, загрузить сертификат или ключ PKCS12. Ключ может быть сгенерированным секретом, строкой, которая вы определяете (например, секрет приложения Facebook) или сертификатом, который вы загружаете. Если набор ключей имеет несколько ключей, активен только один из ключей.

### <a name="trust-framework-policy-keyset"></a>Ключ политики Trust Framework

- [Перечислите ключевые моменты целевой базы](https://docs.microsoft.com/graph/api/trustframework-list-keysets)
- [Создание ключей инфраструктуры доверия](https://docs.microsoft.com/graph/api/trustframework-post-keysets)
- [Получить набор ключей](https://docs.microsoft.com/graph/api/trustframeworkkeyset-get)
- [Обновление ключей целевой базы](https://docs.microsoft.com/graph/api/trustframeworkkeyset-update)
- [Удаление ключей платформы доверия](https://docs.microsoft.com/graph/api/trustframeworkkeyset-delete)

### <a name="trust-framework-policy-key"></a>Ключ политики Рамочной целевой структуры

- [Получить в настоящее время активный ключ в клавиатуре](https://docs.microsoft.com/graph/api/trustframeworkkeyset-getactivekey)
- [Создание ключа в клавиатуре](https://docs.microsoft.com/graph/api/trustframeworkkeyset-generatekey)
- [Загрузить секрет строки, основанной на строке](https://docs.microsoft.com/graph/api/trustframeworkkeyset-uploadsecret)
- [Загрузить сертификат X.509](https://docs.microsoft.com/graph/api/trustframeworkkeyset-uploadcertificate)
- [Загрузить сертификат формата PKCS12](https://docs.microsoft.com/graph/api/trustframeworkkeyset-uploadpkcs12)

## <a name="applications"></a>Приложения

- [Список приложений](https://docs.microsoft.com/graph/api/application-list)
- [Создание приложения](https://docs.microsoft.com/graph/api/resources/application)
- [Обновление приложения](https://docs.microsoft.com/graph/api/application-update)
- [Создание сервисаПринцип](https://docs.microsoft.com/graph/api/resources/serviceprincipal)
- [Создание гранта oauth2Permission](https://docs.microsoft.com/graph/api/resources/oauth2permissiongrant)
- [Удаление приложения](https://docs.microsoft.com/graph/api/application-delete)

## <a name="application-extension-properties"></a>Свойства расширения приложения

- [Свойства расширения списка](https://docs.microsoft.com/graph/api/application-list-extensionproperty)

Azure AD B2C предоставляет каталог, который может содержать 100 настраиваемых атрибутов для каждого пользователя. Для потоков пользователей эти свойства расширения [управляются с помощью портала Azure.](custom-policy-custom-attributes.md) Для пользовательских политик Azure AD B2C создает свойство для вас при первой записи значения для свойства расширения.

## <a name="audit-logs"></a>Журналы аудита

- [Список журналов аудита](https://docs.microsoft.com/graph/api/directoryaudit-list)

Для получения дополнительной информации о доступе к журналам аудита Azure AD B2C с помощью API Microsoft Graph можно [получить доступ к журналам аудита Azure AD B2C.](view-audit-logs.md)
