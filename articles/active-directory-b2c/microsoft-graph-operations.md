---
title: Поддерживаемые операции Microsoft Graph
titleSuffix: Azure AD B2C
description: Индекс операций Microsoft Graph, поддерживаемых для управления Azure AD B2C ресурсами, включая пользователей, потоки пользователей, поставщики удостоверений, пользовательские политики, ключи политики и многое другое.
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
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/29/2020
ms.locfileid: "78184254"
---
# <a name="microsoft-graph-operations-available-for-azure-ad-b2c"></a>Microsoft Graph операции, доступные для Azure AD B2C

Следующие операции API Microsoft Graph поддерживаются для управления Azure AD B2C ресурсами, включая пользователей, поставщиков удостоверений, потоки пользователей, пользовательские политики и ключи политики.

Каждая ссылка в следующих разделах предназначена для соответствующей страницы в справочнике по Microsoft Graph API для этой операции.

## <a name="user-management"></a>Управление пользователями

- [Список пользователей](https://docs.microsoft.com/graph/api/user-list)
- [Создание пользователя-получателя](https://docs.microsoft.com/graph/api/user-post-users)
- [Получение пользователя](https://docs.microsoft.com/graph/api/user-get)
- [Обновление пользователя](https://docs.microsoft.com/graph/api/user-update)
- [Удаление пользователя](https://docs.microsoft.com/graph/api/user-delete)

Дополнительные сведения об управлении учетными записями пользователей Azure AD B2C с помощью Microsoft Graph API см. в статье [Управление учетными записями пользователей Azure AD B2C с помощью Microsoft Graph](manage-user-accounts-graph-api.md).

## <a name="identity-providers-user-flow"></a>Поставщики удостоверений (поток пользователя)

Управление поставщиками удостоверений, доступными для пользователя, в клиенте Azure AD B2C.

- [Список поставщиков удостоверений, зарегистрированных в клиенте Azure AD B2C](https://docs.microsoft.com/graph/api/identityprovider-list)
- [Создание поставщика удостоверений](https://docs.microsoft.com/graph/api/identityprovider-post-identityproviders)
- [Получение поставщика удостоверений](https://docs.microsoft.com/graph/api/identityprovider-get)
- [Обновление поставщика удостоверений](https://docs.microsoft.com/graph/api/identityprovider-update)
- [Удаление поставщика удостоверений](https://docs.microsoft.com/graph/api/identityprovider-delete)

## <a name="user-flow"></a>Поток пользователя

Настройте предварительно созданные политики для регистрации, входа, объединения и входа в систему, сброса пароля и обновления профиля.

- [Перечисление потоков пользователей](https://docs.microsoft.com/graph/api/identityuserflow-list)
- [Создание потока пользователя](https://docs.microsoft.com/graph/api/identityuserflow-post-userflows)
- [Получение потока пользователя](https://docs.microsoft.com/graph/api/identityuserflow-get)
- [Удаление потока пользователя](https://docs.microsoft.com/graph/api/identityuserflow-delete)

## <a name="custom-policies"></a>Пользовательские политики

Следующие операции позволяют управлять политиками инфраструктуры доверия Azure AD B2C, называемыми [пользовательскими политиками](custom-policy-overview.md).

- [Вывод списка всех политик инфраструктуры доверия, настроенных в клиенте](https://docs.microsoft.com/graph/api/trustframework-list-trustframeworkpolicies)
- [Создание политики инфраструктуры доверия](https://docs.microsoft.com/graph/api/trustframework-post-trustframeworkpolicy)
- [Чтение свойств существующей политики инфраструктуры доверия](https://docs.microsoft.com/graph/api/trustframeworkpolicy-get)
- [Обновите или создайте политику инфраструктуры доверия.](https://docs.microsoft.com/graph/api/trustframework-put-trustframeworkpolicy)
- [Удаление существующей политики инфраструктуры доверия](https://docs.microsoft.com/graph/api/trustframeworkpolicy-delete)

## <a name="policy-keys"></a>Ключи политики

Инфраструктура процедур идентификации хранит секреты, на которые ссылается пользовательская политика, для установления отношений доверия между компонентами. Эти секреты могут быть симметричными или асимметричными ключами или значениями. В портал Azure эти сущности отображаются в виде **ключей политики**.

Ресурс верхнего уровня для ключей политики в Microsoft Graph API — это [набор ключей доверенной платформы](https://docs.microsoft.com/graph/api/resources/trustframeworkkeyset). Каждый **набор ключей** содержит по крайней мере один **ключ**. Чтобы создать ключ, сначала создайте пустой набор ключей, а затем создайте ключ в наборе ключей. Можно создать секрет вручную, отправить сертификат или PKCS12 ключ. Ключ может быть созданным секретом, определяемой вами строкой (например, секретом приложения Facebook) или отправленным сертификатом. Если набор ключей содержит несколько ключей, то активен только один из них.

### <a name="trust-framework-policy-keyset"></a>Набор ключей политики доверенной инфраструктуры

- [Перечисление инфраструктуры доверия формируются](https://docs.microsoft.com/graph/api/trustframework-list-keysets)
- [Создание инфраструктуры доверия формируются](https://docs.microsoft.com/graph/api/trustframework-post-keysets)
- [Получение набора ключей](https://docs.microsoft.com/graph/api/trustframeworkkeyset-get)
- [Обновление инфраструктуры доверия формируются](https://docs.microsoft.com/graph/api/trustframeworkkeyset-update)
- [Удаление инфраструктуры доверия формируются](https://docs.microsoft.com/graph/api/trustframeworkkeyset-delete)

### <a name="trust-framework-policy-key"></a>Ключ политики инфраструктуры доверия

- [Получить активный в данный момент ключ в наборе ключей](https://docs.microsoft.com/graph/api/trustframeworkkeyset-getactivekey)
- [Создание ключа в наборе ключей](https://docs.microsoft.com/graph/api/trustframeworkkeyset-generatekey)
- [Отправка секрета на основе строки](https://docs.microsoft.com/graph/api/trustframeworkkeyset-uploadsecret)
- [Отправка сертификата X. 509](https://docs.microsoft.com/graph/api/trustframeworkkeyset-uploadcertificate)
- [Отправка сертификата формата PKCS12](https://docs.microsoft.com/graph/api/trustframeworkkeyset-uploadpkcs12)

## <a name="applications"></a>Приложения

- [Список приложений](https://docs.microsoft.com/graph/api/application-list)
- [Создание приложения](https://docs.microsoft.com/graph/api/resources/application)
- [Обновить приложение](https://docs.microsoft.com/graph/api/application-update)
- [Создание servicePrincipal](https://docs.microsoft.com/graph/api/resources/serviceprincipal)
- [Создание oauth2Permission Grant](https://docs.microsoft.com/graph/api/resources/oauth2permissiongrant)
- [Удаление приложения](https://docs.microsoft.com/graph/api/application-delete)

## <a name="application-extension-properties"></a>Свойства расширения приложения

- [Список свойств расширения](https://docs.microsoft.com/graph/api/application-list-extensionproperty)

Azure AD B2C предоставляет каталог, который может содержать 100 настраиваемых атрибутов для каждого пользователя. Для потоков пользователей эти свойства расширения [управляются с помощью портал Azure](custom-policy-custom-attributes.md). Для настраиваемых политик Azure AD B2C создает свойство при первой записи значения в свойство расширения.

## <a name="audit-logs"></a>Журналы аудита

- [Список журналов аудита](https://docs.microsoft.com/graph/api/directoryaudit-list)

Дополнительные сведения о доступе к журналам аудита Azure AD B2C с помощью Microsoft Graph API см. в разделе [доступ к журналам аудита Azure AD B2C](view-audit-logs.md).
