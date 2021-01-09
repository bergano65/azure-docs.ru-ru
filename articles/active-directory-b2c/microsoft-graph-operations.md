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
ms.date: 10/15/2020
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: fed1e31380381b864530b3fa0b9e8c0886737d04
ms.sourcegitcommit: c4c554db636f829d7abe70e2c433d27281b35183
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/08/2021
ms.locfileid: "98033614"
---
# <a name="microsoft-graph-operations-available-for-azure-ad-b2c"></a>Microsoft Graph операции, доступные для Azure AD B2C

Следующие операции API Microsoft Graph поддерживаются для управления Azure AD B2C ресурсами, включая пользователей, поставщиков удостоверений, потоки пользователей, пользовательские политики и ключи политики.

Каждая ссылка в следующих разделах предназначена для соответствующей страницы в справочнике по Microsoft Graph API для этой операции.

## <a name="user-management"></a>управление пользователями;

- [Список пользователей](/graph/api/user-list)
- [Создание пользователя-получателя](/graph/api/user-post-users)
- [Получение пользователя](/graph/api/user-get)
- [Обновление пользователя](/graph/api/user-update)
- [Удаление пользователя](/graph/api/user-delete)

Дополнительные сведения об управлении учетными записями пользователей Azure AD B2C с помощью Microsoft Graph API см. в статье [Управление учетными записями пользователей Azure AD B2C с помощью Microsoft Graph](manage-user-accounts-graph-api.md).

## <a name="user-phone-number-management"></a>Управление номером телефона пользователя

- [Добавление](/graph/api/authentication-post-phonemethods)
- [Получить](/graph/api/b2cauthenticationmethodspolicy-get)
- [Обновление](/graph/api/b2cauthenticationmethodspolicy-update)
- [Удалить](/graph/api/phoneauthenticationmethod-delete)

Дополнительные сведения об управлении номером телефона для входа пользователя с помощью Microsoft Graph API см. в разделе [методы проверки подлинности B2C](/graph/api/resources/b2cauthenticationmethodspolicy).

## <a name="identity-providers-user-flow"></a>Поставщики удостоверений (поток пользователя)

Управление поставщиками удостоверений, доступными для пользователя, в клиенте Azure AD B2C.

- [Список поставщиков удостоверений, зарегистрированных в клиенте Azure AD B2C](/graph/api/identityprovider-list)
- [Создание поставщика удостоверений](/graph/api/identityprovider-post-identityproviders)
- [Получение поставщика удостоверений](/graph/api/identityprovider-get)
- [Обновление поставщика удостоверений](/graph/api/identityprovider-update)
- [Удаление поставщика удостоверений](/graph/api/identityprovider-delete)

## <a name="user-flow"></a>Поток пользователя

Настройте предварительно созданные политики для регистрации, входа, объединения и входа в систему, сброса пароля и обновления профиля.

- [Перечисление потоков пользователей](/graph/api/identitycontainer-list-b2cuserflows)
- [Создание потока пользователей](/graph/api/identitycontainer-post-b2cuserflows)
- [Получение потока пользователя](/graph/api/b2cidentityuserflow-get)
- [Удаление потока пользователя](/graph/api/b2cidentityuserflow-delete)

## <a name="custom-policies"></a>Пользовательские политики

Следующие операции позволяют управлять политиками инфраструктуры доверия Azure AD B2C, называемыми [пользовательскими политиками](custom-policy-overview.md).

- [Вывод списка всех политик инфраструктуры доверия, настроенных в клиенте](/graph/api/trustframework-list-trustframeworkpolicies)
- [Создание политики инфраструктуры доверия](/graph/api/trustframework-post-trustframeworkpolicy)
- [Чтение свойств существующей политики инфраструктуры доверия](/graph/api/trustframeworkpolicy-get)
- [Обновите или создайте политику инфраструктуры доверия.](/graph/api/trustframework-put-trustframeworkpolicy)
- [Удаление существующей политики инфраструктуры доверия](/graph/api/trustframeworkpolicy-delete)

## <a name="policy-keys"></a>Ключи политики

Инфраструктура процедур идентификации хранит секреты, на которые ссылается пользовательская политика, для установления отношений доверия между компонентами. Эти секреты могут быть симметричными или асимметричными ключами или значениями. В портал Azure эти сущности отображаются в виде **ключей политики**.

Ресурс верхнего уровня для ключей политики в Microsoft Graph API — это [набор ключей доверенной платформы](/graph/api/resources/trustframeworkkeyset). Каждый **набор ключей** содержит по крайней мере один **ключ**. Чтобы создать ключ, сначала создайте пустой набор ключей, а затем создайте ключ в наборе ключей. Можно создать секрет вручную, отправить сертификат или PKCS12 ключ. Ключ может быть созданным секретом, определяемой вами строкой (например, секретом приложения Facebook) или отправленным сертификатом. Если набор ключей содержит несколько ключей, то активен только один из них.

### <a name="trust-framework-policy-keyset"></a>Набор ключей политики доверенной инфраструктуры

- [Перечисление инфраструктуры доверия формируются](/graph/api/trustframework-list-keysets)
- [Создание инфраструктуры доверия формируются](/graph/api/trustframework-post-keysets)
- [Получение набора ключей](/graph/api/trustframeworkkeyset-get)
- [Обновление инфраструктуры доверия формируются](/graph/api/trustframeworkkeyset-update)
- [Удаление инфраструктуры доверия формируются](/graph/api/trustframeworkkeyset-delete)

### <a name="trust-framework-policy-key"></a>Ключ политики инфраструктуры доверия

- [Получить активный в данный момент ключ в наборе ключей](/graph/api/trustframeworkkeyset-getactivekey)
- [Создание ключа в наборе ключей](/graph/api/trustframeworkkeyset-generatekey)
- [Отправка секрета на основе строки](/graph/api/trustframeworkkeyset-uploadsecret)
- [Отправка сертификата X. 509](/graph/api/trustframeworkkeyset-uploadcertificate)
- [Отправка сертификата формата PKCS12](/graph/api/trustframeworkkeyset-uploadpkcs12)

## <a name="applications"></a>Приложения

- [Список приложений](/graph/api/application-list)
- [Создание приложения](/graph/api/resources/application)
- [Update application](/graph/api/application-update)
- [Создание servicePrincipal](/graph/api/resources/serviceprincipal)
- [Создание oauth2Permission Grant](/graph/api/resources/oauth2permissiongrant)
- [Удаление приложения](/graph/api/application-delete)

## <a name="application-extension-properties"></a>Свойства расширения приложения

- [Список свойств расширения](/graph/api/application-list-extensionproperty)

Azure AD B2C предоставляет каталог, который может содержать 100 настраиваемых атрибутов для каждого пользователя. Для потоков пользователей эти свойства расширения [управляются с помощью портал Azure](user-flow-custom-attributes.md). Для настраиваемых политик Azure AD B2C создает свойство для вас в первый раз, когда политика записывает значение в свойство расширения.

## <a name="audit-logs"></a>Журналы аудита

- [Список журналов аудита](/graph/api/directoryaudit-list)

Дополнительные сведения о доступе к журналам аудита Azure AD B2C с помощью Microsoft Graph API см. в разделе [доступ к журналам аудита Azure AD B2C](view-audit-logs.md).