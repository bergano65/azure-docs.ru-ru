---
title: Microsoft identity platform developer glossary | Azure
description: A list of terms for commonly used Microsoft identity platform developer concepts and features.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 551512df-46fb-4219-a14b-9c9fc23998ba
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/28/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: jmprieur, saeeda, jesakowi, nacanuma
ms.collection: M365-identity-device-management
ms.openlocfilehash: 221a491abad6c11ee12c75b1d69f1263f4abddc4
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74322595"
---
# <a name="microsoft-identity-platform-developer-glossary"></a>Microsoft identity platform developer glossary

This article contains definitions for some of the core developer concepts and terminology, which are helpful when learning about application development using Microsoft identity platform.

## <a name="access-token"></a>Twitter,

Тип [маркера безопасности](#security-token), выдаваемого [сервером авторизации](#authorization-server), который используется в [клиентском приложении](#client-application) для доступа к [серверу защищенных ресурсов](#resource-server). Typically in the form of a [JSON Web Token (JWT)][JWT], the token embodies the authorization granted to the client by the [resource owner](#resource-owner), for a requested level of access. Он содержит все применимые [утверждения](#claim) о субъекте, благодаря чему клиентское приложение может использовать его в качестве учетных данных при доступе к определенному ресурсу. Кроме того, владельцу ресурса не нужно предоставлять свои учетные данные клиенту.

Маркеры доступа иногда называют "Пользователь + приложение" или "Только приложение" в зависимости от представляемых учетных данных. Ниже описано, при каком типе предоставления авторизации в клиентском приложении используется тот или иной маркер.

* [Предоставление авторизации "Код авторизации"](#authorization-grant)— сначала пользователь проходит аутентификацию как владелец ресурса, предоставляя клиенту права авторизации для доступа к ресурсу. Затем после получения маркера доступа проверку подлинности проходит клиент. Иногда этот маркер называют маркером "Пользователь + приложение", так как он представляет и пользователя, который авторизовал клиентское приложение, и само приложение.
* [Предоставление авторизации "Учетные данные клиента"](#authorization-grant)— проверку подлинности проходит только клиент, а проверка подлинности и авторизация владельца ресурса не выполняются, поэтому маркер иногда называют маркером "Только приложение".

See [Microsoft identity platform Token Reference][AAD-Tokens-Claims] for more details.

## <a name="application-id-client-id"></a>application ID (client ID)

Уникальный идентификатор, который служба Azure AD присваивает зарегистрированному приложению. Идентификатор определяет конкретное приложение и связанные с ним конфигурации. This application ID ([client ID](https://tools.ietf.org/html/rfc6749#page-15)) is used when performing authentication requests and is provided to the authentication libraries in development time. The application ID (client ID) is not a secret.

## <a name="application-manifest"></a>Манифест приложения

A feature provided by the [Azure portal][AZURE-portal], which produces a JSON representation of the application's identity configuration, used as a mechanism for updating its associated [Application][AAD-Graph-App-Entity] and [ServicePrincipal][AAD-Graph-Sp-Entity] entities. See [Understanding the Azure Active Directory application manifest][AAD-App-Manifest] for more details.

## <a name="application-object"></a>Объект приложения

When you register/update an application in the [Azure portal][AZURE-portal], the portal creates/updates both an application object and a corresponding [service principal object](#service-principal-object) for that tenant. Объект приложения глобально *определяет* конфигурацию удостоверения для приложения (для всех клиентов, где у него есть доступ) и предоставляет шаблон, *на основе* которого создаются соответствующие объекты субъектов-служб для локального использования во время выполнения (в конкретном клиенте).

For more information, see [Application and Service Principal Objects][AAD-App-SP-Objects].

## <a name="application-registration"></a>Регистрация приложения

Чтобы разрешить приложению выполнять интеграцию с функциями управления удостоверениями и доступом и предоставлять права на их использование в Azure AD, необходимо зарегистрировать его в [клиенте](#tenant)Azure AD. При регистрации приложения в Azure AD вы предоставляете соответствующую конфигурацию удостоверения, что позволяет интегрировать приложение с Azure AD и использовать следующие возможности:

* Robust management of Single Sign-On using Azure AD Identity Management and [OpenID Connect][OpenIDConnect] protocol implementation
* Brokered access to [protected resources](#resource-server) by [client applications](#client-application), via OAuth 2.0 [authorization server](#authorization-server)
* [Платформа согласия](#consent) для управления клиентским доступом к защищенным ресурсам на основе авторизации владельца ресурса.

See [Integrating applications with Azure Active Directory][AAD-Integrating-Apps] for more details.

## <a name="authentication"></a>authentication

Запрос допустимых учетных данных у стороны, выполняющей вход. Это основа для создания субъекта безопасности, используемого для управления доступом и удостоверениями. К примеру, во время [предоставления авторизации OAuth2](#authorization-grant) сторона, выполняющая проверку подлинности, в зависимости от используемых разрешений выполняет роль [владельца ресурса](#resource-owner) или [клиентского приложения](#client-application).

## <a name="authorization"></a>authorization

Предоставление разрешения на выполнение какого-либо действия субъекту безопасности, прошедшему проверку подлинности. Модель программирования Azure AD предусматривает два основных варианта:

* При [предоставлении авторизации OAuth2](#authorization-grant): [владелец ресурса](#resource-owner) предоставляет авторизацию для [клиентского приложения](#client-application), разрешая клиенту получать доступ к своим ресурсам.
* При доступе клиента к ресурсам: [сервер ресурсов](#resource-server) выполняет авторизацию, используя содержащиеся в [маркере доступа](#access-token) значения [утверждений](#claim), на основе которых можно принимать решения по контролю доступа.

## <a name="authorization-code"></a>Код авторизации

Кратковременный маркер, который [конечная точка авторизации](#authorization-endpoint) предоставляет для [клиентского приложения](#client-application) во время соответствующего потока. Это один из четырех типов [предоставления авторизации](#authorization-grant) OAuth2. Этот код возвращается в клиентское приложение в ответ на проверку подлинности [владельца ресурса](#resource-owner). В нем указано, что последний делегировал права на авторизацию для доступа к требуемым ресурсам. В рамках потока этот код позже меняется на [маркер доступа](#access-token).

## <a name="authorization-endpoint"></a>Конечная точка авторизации

Одна из конечных точек, реализуемых [сервером авторизации](#authorization-server). Она используется для взаимодействия с [владельцем ресурса](#resource-owner), чтобы [предоставить авторизацию](#authorization-grant) во время потока кода авторизации OAuth2. Фактическое предоставление, в том числе [код авторизации](#authorization-code) и [маркер безопасности](#security-token), может отличаться в зависимости от используемого потока предоставления авторизации.

See the OAuth2 specification's [authorization grant types][OAuth2-AuthZ-Grant-Types] and [authorization endpoint][OAuth2-AuthZ-Endpoint] sections, and the [OpenIDConnect specification][OpenIDConnect-AuthZ-Endpoint] for more details.

## <a name="authorization-grant"></a>Предоставление авторизации

Объект, предоставляющий [авторизацию](#authorization) [владельца ресурса](#resource-owner) для [клиентского приложения](#client-application), что позволяет ему получать доступ к защищенным ресурсам этого владельца. A client application can use one of the [four grant types defined by the OAuth2 Authorization Framework][OAuth2-AuthZ-Grant-Types] to obtain a grant, depending on client type/requirements: "authorization code grant", "client credentials grant", "implicit grant", and "resource owner password credentials grant". Клиенту возвращается [маркер доступа](#access-token) или [код авторизации](#authorization-code) (который позже меняется на маркер доступа) в зависимости от используемого типа предоставления авторизации.

## <a name="authorization-server"></a>сервера авторизации

As defined by the [OAuth2 Authorization Framework][OAuth2-Role-Def], the server responsible for issuing access tokens to the [client](#client-application) after successfully authenticating the [resource owner](#resource-owner) and obtaining its authorization. [Клиентское приложение](#client-application) взаимодействует с сервером авторизации во время выполнения с использованием конечных точек [авторизации](#authorization-endpoint) и [маркеров](#token-endpoint) в соответствии с [предоставлениями авторизации](#authorization-grant), определенными OAuth2.

In the case of Microsoft identity platform application integration, Microsoft identity platform implements the authorization server role for Azure AD applications and Microsoft service APIs, for example [Microsoft Graph APIs][Microsoft-Graph].

## <a name="claim"></a>Утверждение

[Маркер безопасности](#security-token) предоставляет утверждения об одной сущности (например, о [клиентском приложении](#client-application) или [владельце ресурса](#resource-owner)) другой сущности (например, [серверу ресурсов](#resource-server)). Утверждения — это пары "имя — значение", которые ретранслируют факты о субъекте маркера (например, субъекте безопасности, прошедшем проверку подлинности с помощью [сервера авторизации](#authorization-server)). Утверждения, имеющиеся в заданном маркере, зависят от нескольких переменных, в том числе от типа маркера, типа учетных данных, используемых для проверки подлинности субъекта, конфигурации приложения и т. п.

See [Microsoft identity platform token reference][AAD-Tokens-Claims] for more details.

## <a name="client-application"></a>клиентского приложения

As defined by the [OAuth2 Authorization Framework][OAuth2-Role-Def], an application that makes protected resource requests on behalf of the [resource owner](#resource-owner). Термин "клиент" не подразумевает какие-либо конкретные характеристики реализации оборудования (например, место выполнения приложения: на сервере, настольном компьютере или на других устройствах).

Клиентское приложение запрашивает [авторизацию](#authorization) от владельца ресурса для участия в потоке [предоставления авторизации OAuth2](#authorization-grant) и может получать доступ к API и данным от имени владельца ресурса. The OAuth2 Authorization Framework [defines two types of clients][OAuth2-Client-Types], "confidential" and "public", based on the client's ability to maintain the confidentiality of its credentials. Приложения могут реализовать [веб-клиент (конфиденциальный)](#web-client), выполняемый на веб-сервере, [клиент Native Client (открытый)](#native-client), установленный на устройстве, и [клиент на основе агента пользователя (открытый)](#user-agent-based-client), выполняемый в браузере устройства.

## <a name="consent"></a>Согласие

Процесс предоставления [владельцем ресурса](#resource-owner) [клиентскому приложению](#client-application) доступа к ресурсам, защищенным конкретными [разрешениями](#permissions), от своего имени. В зависимости от разрешений, запрошенных клиентом, администратору или пользователю будет отправлен запрос на разрешение доступа к рабочим или личным данным. Обратите внимание, в сценарии с [несколькими клиентами](#multi-tenant-application) [субъект-служба](#service-principal-object) приложения также записывается в клиенте пользователя, дающего согласие.

См. дополнительные сведения о [платформе предоставления согласия](consent-framework.md).

## <a name="id-token"></a>Маркер идентификации

An [OpenID Connect][OpenIDConnect-ID-Token] [security token](#security-token) provided by an [authorization server's](#authorization-server) [authorization endpoint](#authorization-endpoint), which contains [claims](#claim) pertaining to the authentication of an end user [resource owner](#resource-owner). Like an access token, ID tokens are also represented as a digitally signed [JSON Web Token (JWT)][JWT]. Тем не менее в отличие от маркера доступа утверждения маркера идентификации не используются для целей, связанных с доступом к ресурсам и контролем доступа.

See [Microsoft identity platform token reference][AAD-Tokens-Claims] for more details.

## <a name="microsoft-identity-platform"></a>Платформа удостоверений Майкрософт

Платформа удостоверений Майкрософт является результатом эволюционного развития платформы для разработчиков и службы идентификации Azure Active Directory (Azure AD). С ее помощью разработчики могут создавать приложения, которые обеспечивают вход с помощью любых удостоверений Майкрософт, получают маркеры для вызова Microsoft Graph, других интерфейсов API Майкрософт или интерфейсов API, созданных разработчиками. Это полнофункциональная платформа, включающая в себя службу аутентификации, библиотеки, службу регистрации приложений и конфигурацию, полную документацию по разработке, примеры кода и другое содержимое, предназначенное для разработчиков. Платформа удостоверений Майкрософт поддерживает стандартные отраслевые протоколы, такие как OAuth 2.0 и OpenID Connect. См. дополнительные сведения о [платформе удостоверений Майкрософт](about-microsoft-identity-platform.md).

## <a name="multi-tenant-application"></a>Мультитенантное приложение

Класс клиентского приложения, который обеспечивает вход и [согласие](#consent) пользователей, подготовленных в любом [клиенте](#tenant) Azure AD, включая клиенты, отличные от того, в котором зарегистрирован клиентский компьютер. Приложения [собственного клиента](#native-client) являются мультитенантными по умолчанию, тогда как приложения [веб-клиента](#web-client) и [веб-ресурса или веб-API](#resource-server) предусматривают возможность выбора отдельного клиента или нескольких клиентов. Веб-приложение, зарегистрированное как одноклиентское, напротив, предоставляет разрешение на вход только из учетных записей пользователей, подготовленных в том же клиенте, где было зарегистрировано приложение.

See [How to sign in any Azure AD user using the multi-tenant application pattern][AAD-Multi-Tenant-Overview] for more details.

## <a name="native-client"></a>Собственный клиент

Тип [клиентского приложения](#client-application), которое изначально установлено на устройстве. Так как весь код выполняется на устройстве, этот клиент считается общедоступным из-за неспособности хранить учетные данные в частном порядке, т. е. конфиденциально. See [OAuth2 client types and profiles][OAuth2-Client-Types] for more details.

## <a name="permissions"></a>permissions

[Клиентское приложение](#client-application) получает доступ к [серверу ресурсов](#resource-server) путем объявления запросов на разрешения. Доступно два типа таких разрешений:

* Делегированные разрешения, которые определяют доступ [на основе области](#scopes) в рамках предоставленной авторизации от выполнившего вход [владельца ресурса](#resource-owner), предъявляются ресурсу во время выполнения в виде [SCP-утверждений](#claim) в [маркере доступа](#access-token) клиента.
* Разрешения приложения, которые определяют доступ [на основе ролей](#roles), используя удостоверение или учетные данные клиентского приложения, предъявляются ресурсу во время выполнения в виде [утверждений ролей](#claim) в маркере доступа клиента.

В процессе получения [согласия](#consent) можно использовать оба типа разрешений. Они предоставляют администратору или владельцу ресурса возможность разрешить или запретить клиентскому приложению доступ к ресурсам в их клиенте.

Permission requests are configured on the **API permissions** page for an application in the [Azure portal][AZURE-portal], by selecting the desired "Delegated Permissions" and "Application Permissions" (the latter requires membership in the Global Admin role). Так как [общедоступный клиент](#client-application) не может обеспечить безопасное хранение учетных данных, он может запросить только делегированные разрешения, в то время как [конфиденциальный клиент](#client-application) может запросить и делегированные разрешения, и разрешения приложения. The client's [application object](#application-object) stores the declared permissions in its [requiredResourceAccess property][AAD-Graph-App-Entity].

## <a name="resource-owner"></a>владелец ресурса

As defined by the [OAuth2 Authorization Framework][OAuth2-Role-Def], an entity capable of granting access to a protected resource. Если владелец ресурса — человек, он называется пользователем. For example, when a [client application](#client-application) wants to access a user's mailbox through the [Microsoft Graph API][Microsoft-Graph], it requires permission from the resource owner of the mailbox.

## <a name="resource-server"></a>серверу ресурсов

As defined by the [OAuth2 Authorization Framework][OAuth2-Role-Def], a server that hosts protected resources, capable of accepting and responding to protected resource requests by [client applications](#client-application) that present an [access token](#access-token). Он также называется сервером защищенных ресурсов или приложением ресурсов.

Сервер ресурсов предоставляет API-интерфейсы и обеспечивает доступ к размещенным на нем защищенным ресурсам на основе [областей](#scopes) и [ролей](#roles), используя OAuth 2.0 Authorization Framework. Например, интерфейс API Graph Azure AD, который предоставляет доступ к данным клиента Azure AD, и интерфейс API Office 365, который предоставляет доступ к таким данным, как почта и календарь. Both of these are also accessible via the [Microsoft Graph API][Microsoft-Graph].

Как и в клиентском приложении, конфигурация удостоверения приложения ресурсов устанавливается путем [регистрации](#application-registration) в клиенте Azure AD, при этом предоставляется и объект приложения, и объект субъекта-службы. Некоторые API, предоставляемые корпорацией Майкрософт, например API Graph Azure AD, содержат заранее зарегистрированные субъекты-служб, доступные во всех клиентах во время подготовки.

## <a name="roles"></a>roles

Как и [области](#scopes), роли предоставляют способ контроля доступа к защищенным ресурсам для [сервера ресурсов](#resource-server). Существует два типа ролей: роль "пользователь" реализует управление доступом на основе ролей для пользователей или групп, которым требуется доступ к ресурсу, а роль "приложение" реализует то же самое для [клиентских приложений](#client-application) , которым нужен доступ.

Roles are resource-defined strings (for example "Expense approver", "Read-only", "Directory.ReadWrite.All"), managed in the [Azure portal][AZURE-portal] via the resource's [application manifest](#application-manifest), and stored in the resource's [appRoles property][AAD-Graph-Sp-Entity]. С помощью портала Azure можно также назначать пользователям роль "пользователь" и настраивать [разрешения приложений](#permissions) клиента для доступа к роли "приложение".

For a detailed discussion of the application roles exposed by Azure AD's Graph API, see [Graph API Permission Scopes][AAD-Graph-Perm-Scopes]. For a step-by-step implementation example, see [Manage access using RBAC and the Azure portal][AAD-RBAC].

## <a name="scopes"></a>Области

Как и [роли](#roles), области позволяют [серверу ресурсов](#resource-server) контролировать доступ к своим защищенным ресурсам. Scopes are used to implement [scope-based][OAuth2-Access-Token-Scopes] access control, for a [client application](#client-application) that has been given delegated access to the resource by its owner.

Scopes are resource-defined strings (for example "Mail.Read", "Directory.ReadWrite.All"), managed in the [Azure portal][AZURE-portal] via the resource's [application manifest](#application-manifest), and stored in the resource's [oauth2Permissions property][AAD-Graph-Sp-Entity]. Портал Azure также используется для настройки [делегированных разрешений](#permissions) клиентского приложения для доступа к области.

Рекомендуется использовать такой формат именования: resource.operation.constraint. For a detailed discussion of the scopes exposed by Azure AD's Graph API, see [Graph API Permission Scopes][AAD-Graph-Perm-Scopes]. For scopes exposed by Office 365 services, see [Office 365 API permissions reference][O365-Perm-Ref].

## <a name="security-token"></a>Маркер безопасности

Подписанный документ, содержащий утверждения, такие как маркер OAuth2 или утверждение SAML 2.0. For an OAuth2 [authorization grant](#authorization-grant), an [access token](#access-token) (OAuth2) and an [ID Token](https://openid.net/specs/openid-connect-core-1_0.html#IDToken) are types of security tokens, both of which are implemented as a [JSON Web Token (JWT)][JWT].

## <a name="service-principal-object"></a>объект субъекта-службы

When you register/update an application in the [Azure portal][AZURE-portal], the portal creates/updates both an [application object](#application-object) and a corresponding service principal object for that tenant. Объект приложения глобально *определяет* конфигурацию удостоверения для приложения (для всех клиентов, где связанному приложению предоставлен доступ) и шаблон, *на основе* которого создаются соответствующие объекты субъектов-служб для локального использования во время выполнения (в конкретном клиенте).

For more information, see [Application and Service Principal Objects][AAD-App-SP-Objects].

## <a name="sign-in"></a>Вход

Процесс, в ходе которого [клиентское приложение](#client-application) инициирует проверку подлинности пользователя и фиксирует соответствующее состояние, чтобы получить [маркер безопасности](#security-token) и использовать это состояние для сеанса приложения. Состояние может включать в себя артефакты, такие как сведения о профиле пользователя и информация, извлеченная из утверждений маркера.

Функция входа в приложение обычно используется для реализации единого входа (SSO). Ей может также предшествовать функция регистрации как точка входа для пользователя, с помощью которой предоставляется доступ к приложению (после первого входа в систему). Функция регистрации используется для сбора и сохранения дополнительного состояния конкретного пользователя и может требовать [согласия пользователя](#consent).

## <a name="sign-out"></a>Выход

Отмена аутентификации пользователя, при которой отключается отслеживание состояния пользователя, связанное с сеансом [клиентского приложения](#client-application) при [входе](#sign-in).

## <a name="tenant"></a>tenant

Экземпляр каталога Azure AD, который называют клиентом Azure AD. Он обеспечивает несколько функций, включая следующие:

* служба реестра интегрированных приложений;
* аутентификация учетных записей пользователей и зарегистрированных приложений;
* конечные точки REST, необходимые для поддержки различных протоколов, включая OAuth2 и SAML, в том числе [конечная точка авторизации](#authorization-endpoint), [конечная точка маркера](#token-endpoint) и "общие" конечные точки, используемые [мультитенантными приложениями](#multi-tenant-application).

Создание клиентов Azure AD и их привязка к подпискам Azure и Office 365 выполняется во время регистрации, что обеспечивает возможности системы управления идентификацией и доступом для подписки. Администраторы подписок Azure могут также создать дополнительные клиенты Azure AD с помощью портала Azure. See [How to get an Azure Active Directory tenant][AAD-How-To-Tenant] for details on the various ways you can get access to a tenant. See [How Azure subscriptions are associated with Azure Active Directory][AAD-How-Subscriptions-Assoc] for details on the relationship between subscriptions and an Azure AD tenant.

## <a name="token-endpoint"></a>Конечная точка маркера

Одна из конечных точек, реализуемых [сервером авторизации](#authorization-server) для поддержки [предоставлений авторизации](#authorization-grant) OAuth2. Depending on the grant, it can be used to acquire an [access token](#access-token) (and related "refresh" token) to a [client](#client-application), or [ID token](#id-token) when used with the [OpenID Connect][OpenIDConnect] protocol.

## <a name="user-agent-based-client"></a>Клиент на основе агента пользователя

Тип [клиентского приложения](#client-application), которое скачивает код с веб-сервера и выполняется в агенте пользователя (к примеру, в браузере), например одностраничное приложение (SPA). Так как весь код выполняется на устройстве, этот клиент считается общедоступным из-за неспособности хранить учетные данные в частном порядке, т. е. конфиденциально. For more information, see [OAuth2 client types and profiles][OAuth2-Client-Types].

## <a name="user-principal"></a>Субъект-пользователь

Аналогично объекту субъекта-службы, который представляет экземпляр приложения, объект субъекта-пользователя является другим типом субъекта безопасности, который представляет пользователя. The Azure AD Graph [User entity][AAD-Graph-User-Entity] defines the schema for a user object, including user-related properties such as first and last name, user principal name, directory role membership, etc. This provides the user identity configuration for Azure AD to establish a user principal at run-time. Субъект-пользователь представляет прошедшего проверку подлинности пользователя при едином входе, записи делегирования права [согласия](#consent), принятии решений по контролю доступа и т. п.

## <a name="web-client"></a>веб-клиента

Тип [клиентского приложения](#client-application), которое выполняет весь код на веб-сервере и может выступать в роли "конфиденциального" клиента, обеспечивая безопасное хранение учетных данных на сервере. For more information, see [OAuth2 client types and profiles][OAuth2-Client-Types].

## <a name="next-steps"></a>Дальнейшие действия

The [Microsoft identity platform Developer's Guide][AAD-Dev-Guide] is the landing page to use for all Microsoft identity platform development-related topics, including an overview of [application integration][AAD-How-To-Integrate] and the basics of [Microsoft identity platform authentication and supported authentication scenarios][AAD-Auth-Scenarios]. Руководства и примеры кода для быстрого начала работы можно также найти на сайте [GitHub](https://github.com/azure-samples?utf8=%E2%9C%93&q=active%20directory&type=&language=).

В следующем разделе комментариев вы можете поделиться своим мнением и помочь улучшить и сформировать материалы. Также вы можете запросить создание новых определений или обновление существующих.

<!--Image references-->

<!--Reference style links -->
[AAD-App-Manifest]:reference-app-manifest.md
[AAD-App-SP-Objects]:app-objects-and-service-principals.md
[AAD-Auth-Scenarios]:authentication-scenarios.md
[AAD-Dev-Guide]:azure-ad-developers-guide.md
[AAD-Graph-Perm-Scopes]: /graph/permissions-reference
[AAD-Graph-App-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[AAD-Graph-Sp-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity
[AAD-Graph-User-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#user-entity
[AAD-How-Subscriptions-Assoc]:../fundamentals/active-directory-how-subscriptions-associated-directory.md
[AAD-How-To-Integrate]: ./active-directory-how-to-integrate.md
[AAD-How-To-Tenant]:quickstart-create-new-tenant.md
[AAD-Integrating-Apps]:quickstart-v1-integrate-apps-with-azure-ad.md
[AAD-Multi-Tenant-Overview]:howto-convert-app-to-be-multi-tenant.md
[AAD-Security-Token-Claims]: ./active-directory-authentication-scenarios/#claims-in-azure-ad-security-tokens
[AAD-Tokens-Claims]:access-tokens.md
[AZURE-portal]: https://portal.azure.com
[AAD-RBAC]: ../../role-based-access-control/role-assignments-portal.md
[JWT]: https://tools.ietf.org/html/draft-ietf-oauth-json-web-token-32
[Microsoft-Graph]: https://developer.microsoft.com/graph
[O365-Perm-Ref]: https://msdn.microsoft.com/office/office365/howto/application-manifest
[OAuth2-Access-Token-Scopes]: https://tools.ietf.org/html/rfc6749#section-3.3
[OAuth2-AuthZ-Endpoint]: https://tools.ietf.org/html/rfc6749#section-3.1
[OAuth2-AuthZ-Grant-Types]: https://tools.ietf.org/html/rfc6749#section-1.3
[OAuth2-Client-Types]: https://tools.ietf.org/html/rfc6749#section-2.1
[OAuth2-Role-Def]: https://tools.ietf.org/html/rfc6749#page-6
[OpenIDConnect]: https://openid.net/specs/openid-connect-core-1_0.html
[OpenIDConnect-AuthZ-Endpoint]: https://openid.net/specs/openid-connect-core-1_0.html#AuthorizationEndpoint
[OpenIDConnect-ID-Token]: https://openid.net/specs/openid-connect-core-1_0.html#IDToken
