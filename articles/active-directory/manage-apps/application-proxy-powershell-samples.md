---
title: Примеры PowerShell для AD Application Proxy Azure
description: Используйте эти примеры PowerShell для AD Application Proxy Azure, чтобы получить сведения о приложениях и соединителях прокси приложения в каталоге, назначить пользователей и группы для приложений и получить сведения о сертификате.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 12/05/2019
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 476bdfdd2c0b81d86900cee18024cc261ee80a07
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "79481268"
---
# <a name="azure-ad-powershell-examples-for-azure-ad-application-proxy"></a>Примеры Azure AD PowerShell для Azure AD Application Proxy

В следующей таблице приведены ссылки на примеры сценариев PowerShell для Azure AD Application Proxy. Для этих примеров требуется [модуль PowerShell AzureAD v2 для Graph](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0) или [версия AzureAD v2 PowerShell для предварительной версии модуля Graph](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview), если не указано иное.


Дополнительные сведения о командлетах, используемых в этих примерах, см. в разделе Управление [приложениями прокси](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#application_proxy_application_management) приложения и [Управление соединителем прокси приложения](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#application_proxy_connector_management).

| | |
|---|---|
|**Приложения прокси приложения**||
| [Вывод основных сведений обо всех приложениях прокси приложения](scripts/powershell-get-all-app-proxy-apps-basic.md) | Список основных сведений (AppId, DisplayName, ObjId) обо всех приложениях прокси приложения в каталоге. |
| [Вывод расширенных сведений обо всех приложениях прокси приложения](scripts/powershell-get-all-app-proxy-apps-extended.md) | Список расширенных сведений (AppId, DisplayName, Екстерналурл, InternalUrl, Екстерналаусентикатионтипе) обо всех приложениях прокси приложения в каталоге.  |
| [Вывод списка всех приложений прокси приложения по группам соединителей](scripts/powershell-get-all-app-proxy-apps-by-connector-group.md) | Выводит сведения обо всех приложениях прокси приложения в каталоге, а также о том, к каким группам соединителей назначены приложения. |
| [Получение всех приложений прокси приложения с помощью политики времени жизни маркеров](scripts/powershell-get-all-app-proxy-apps-with-policy.md) | Список всех приложений прокси приложения в каталоге с политикой времени существования маркера и сведениями о нем. Для работы с этим образцом требуется [AzureAD v2 PowerShell для предварительной версии модуля Graph](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview). |
|**Группы соединителей**||
| [Получение всех групп соединителей и соединителей в каталоге](scripts/powershell-get-all-connectors.md) | Список всех групп соединителей и соединителей в каталоге. |
| [Перемещение всех приложений, назначенных группе соединителей, в другую группу соединителей](scripts/powershell-move-all-apps-to-connector-group.md) | Перемещает все приложения, назначенные в данный момент группе соединителей, в другую группу соединителей. |
|**Пользователи и назначенные группы**||
| [Отображение пользователей и групп, назначенных приложению прокси приложения](scripts/powershell-display-users-group-of-app.md) | Список пользователей и групп, назначенных конкретному приложению прокси приложения. |
| [Назначение пользователя для приложения](scripts/powershell-assign-user-to-app.md) | Назначает конкретному пользователю приложение. |
| [Назначение группы для приложения](scripts/powershell-assign-group-to-app.md) | Назначает определенную группу для приложения. |
|**Настройка внешнего URL-адреса**||
| [Получение всех приложений прокси приложения с помощью доменов по умолчанию (. msappproxy.net)](scripts/powershell-get-all-default-domain-apps.md)  | Список всех приложений прокси приложения, использующих домены по умолчанию (. msappproxy.net). |
| [Получение всех приложений прокси приложения с помощью публикации с использованием подстановочных знаков](scripts/powershell-get-all-wildcard-apps.md) | Список всех приложений прокси приложения, использующих публикацию с подстановочными знаками. |
|**Конфигурация пользовательского домена**||
| [Получение всех приложений прокси приложения, использующих личные домены и сведения о сертификате](scripts/powershell-get-all-custom-domains-and-certs.md) | Список всех приложений прокси приложения, использующих личные домены, и сведения о сертификате, связанные с пользовательскими доменами. |
| [Получение всех опубликованных приложений прокси-сервера Azure AD без отправленного сертификата](scripts/powershell-get-all-custom-domain-no-cert.md) | Список всех приложений прокси приложения, использующих пользовательские домены, но не имеющих действительного сертификата TLS/SSL. |
| [Получение всех приложений прокси-сервера Azure AD, опубликованных с помощью идентичного сертификата](scripts/powershell-get-custom-domain-identical-cert.md) | Список всех приложений прокси-сервера Azure AD, опубликованных с помощью идентичного сертификата. |
| [Получить все приложения прокси-сервера Azure AD, опубликованные с помощью идентичного сертификата, и заменить их](scripts/powershell-get-custom-domain-replace-cert.md) | Для приложений прокси-сервера Azure AD, опубликованных с помощью идентичного сертификата, можно выполнить небольшую замену сертификата. |
