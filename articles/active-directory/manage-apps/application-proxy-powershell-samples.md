---
title: Образцы PowerShell для прокси-приложения Azure AD
description: Используйте эти образцы PowerShell для Azure AD Application Proxy для получения информации о приложениях и разъемах приложений Application Proxy в каталоге, отсваивайте пользователей и группы в приложения и получайте информацию о сертификатах.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481268"
---
# <a name="azure-ad-powershell-examples-for-azure-ad-application-proxy"></a>Примеры Azure AD PowerShell для прокси-приложений Azure AD

В следующей таблице приведены ссылки на примеры сценариев PowerShell для прокси-приложений Azure AD. Эти образцы требуют либо [AzureAD V2 PowerShell для модуля Graph](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0) или [AzureAD V2 PowerShell для версии предварительного просмотра модуля Graph,](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview)если не указано иное.


Для получения дополнительной информации о cmdlets, используемых [Application Proxy Connector Management](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#application_proxy_connector_management)в этих образцах, [см.](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#application_proxy_application_management)

| | |
|---|---|
|**Приложения Прокси-приложения**||
| [Перечислите основную информацию для всех приложений App Proxy](scripts/powershell-get-all-app-proxy-apps-basic.md) | Перечисляет основную информацию (AppId, DisplayName, ObjId) обо всех приложениях App Proxy в вашем каталоге. |
| [Список расширенной информации для всех приложений Прокси приложений приложений](scripts/powershell-get-all-app-proxy-apps-extended.md) | Перечисляет расширенную информацию (AppId, DisplayName, ExternalUrl, InternalUrl, ExternalAuthenticationType) обо всех приложениях App Proxy в вашем каталоге.  |
| [Перечислите все приложения Прокси приложений по группе разъемов](scripts/powershell-get-all-app-proxy-apps-by-connector-group.md) | Перечисляет информацию обо всех приложениях Application Proxy в вашем каталоге и о группах разъемов приложений. |
| [Получите все приложения App Proxy с политикой срока службы маркеров](scripts/powershell-get-all-app-proxy-apps-with-policy.md) | Перечисляет все приложения Application Proxy в каталоге с политикой жизни маркеров и его деталями. Этот образец требует [AzureAD V2 PowerShell для версии предварительного просмотра модуля Graph.](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview) |
|**Группы соединителей**||
| [Получить все группы разъемов и разъемов в каталоге](scripts/powershell-get-all-connectors.md) | Перечисляет все группы разъемов и разъемов в каталоге. |
| [Переместите все приложения, назначенные группе разъемов, в другую группу разъемов](scripts/powershell-move-all-apps-to-connector-group.md) | Перемещает все приложения, в настоящее время назначенные группе разъемов, в другую группу разъемов. |
|**Пользователи и назначенные группы**||
| [Отображение пользователей и групп, назначенных приложению Прокси-приложения приложения](scripts/powershell-display-users-group-of-app.md) | Перечисляет пользователей и группы, назначенные в конкретное приложение Прокси-приложения. |
| [Назначить пользователю приложение](scripts/powershell-assign-user-to-app.md) | Назначает конкретного пользователя в приложение. |
| [Назначить группу приложения](scripts/powershell-assign-group-to-app.md) | Назначает определенную группу приложения. |
|**Конфигурация внешнего URL**||
| [Получите все приложения Application Proxy с помощью доменов по умолчанию (.msappproxy.net)](scripts/powershell-get-all-default-domain-apps.md)  | Перечисляет все приложения Application Proxy с использованием доменов по умолчанию (.msappproxy.net). |
| [Получите все приложения Application Proxy с помощью публикации подстановочных знаков](scripts/powershell-get-all-wildcard-apps.md) | Перечисляет все приложения Application Proxy с помощью публикации подстановочных знаков. |
|**Конфигурация пользовательского домена**||
| [Получите все приложения App Proxy, используя пользовательские домены и сведения о сертификатах](scripts/powershell-get-all-custom-domains-and-certs.md) | Перечисляет все приложения App Proxy, использующие пользовательские домены, и сведения о сертификатах, связанные с пользовательскими доменами. |
| [Публикация всех приложений Azure AD Proxy без загрузки сертификата](scripts/powershell-get-all-custom-domain-no-cert.md) | Перечисляет все приложения App Proxy, которые используют пользовательские домены, но не имеют действительного сертификата TLS/SSL загружены. |
| [Получите все приложения Azure AD Proxy, опубликованные с идентичным сертификатом](scripts/powershell-get-custom-domain-identical-cert.md) | Перечисляет все приложения Azure AD Proxy, опубликованные с идентичным сертификатом. |
| [Получите все приложения Azure AD Proxy, опубликованные с идентичным сертификатом, и замените его](scripts/powershell-get-custom-domain-replace-cert.md) | Для приложений Azure AD Proxy, которые публикуются с идентичным сертификатом, позволяет заменить сертификат оптом. |
