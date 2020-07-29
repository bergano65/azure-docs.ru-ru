---
title: Устранение ошибок в API отчетов Azure Active Directory | Документы Майкрософт
description: Описание устранения ошибок, возникающих при вызове API отчетов Azure Active Directory.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 0030c5a4-16f0-46f4-ad30-782e7fea7e40
ms.service: active-directory
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: eec5c5a3d810fdd2d561313e3a355e872fb525c2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85608098"
---
# <a name="troubleshoot-errors-in-azure-active-directory-reporting-api"></a>Устранение ошибок в API отчетов Azure Active Directory

В этой статье перечислены распространенные сообщения об ошибках, с которыми можно столкнуться при доступе к отчетам о действиях с помощью Microsoft Graph API и действиях по их устранению.

### <a name="500-http-internal-server-error-while-accessing-microsoft-graph-v2-endpoint"></a>500 HTTP internal server error while accessing Microsoft Graph V2 endpoint (Внутренняя ошибка сервера 500 HTTP при обращении к конечной точке Microsoft Graph версии 2)

Конечная точка Microsoft Graph версии 2 сейчас не поддерживается, для доступа к журналам действий используйте конечную точку Microsoft Graph версии 1.

### <a name="error-neither-tenant-is-b2c-or-tenant-doesnt-have-premium-license"></a>Error: Neither tenant is B2C or tenant doesn't have premium license (Ошибка: клиент не относится к типу B2C и у него нет лицензии уровня "Премиум")

Для доступа к отчетам о входе требуется лицензия Azure Active Directory Premium 1 (P1). Если вы видите это сообщение об ошибке при обращении ко входам, убедитесь, что клиент лицензируется по лицензии Azure AD P1.

### <a name="error-user-is-not-in-the-allowed-roles"></a>Error: User is not in the allowed roles (Ошибка: пользователь не относится к разрешенным ролям) 

Если вы видите это сообщение об ошибке при обращении к журналам аудита или входам с помощью API, убедитесь, что ваша учетная запись является частью роли **читателя сведений о безопасности** или **читателя отчетов** в клиенте Azure Active Directory. 

### <a name="error-application-missing-aad-read-directory-data-permission"></a>Error: Application missing AAD 'Read directory data' permission (Ошибка: у приложения нет разрешения "Чтение данных каталога" AAD) 

Выполните шаги, описанные в разделе [Предварительные требования для доступа к API отчетов Azure Active Directory](howto-configure-prerequisites-for-reporting-api.md), чтобы убедиться в наличии подходящего набора разрешений для приложения. 

### <a name="error-application-missing-microsoft-graph-api-read-all-audit-log-data-permission"></a>Ошибка: в приложении отсутствует разрешение на чтение всех данных журнала аудита Microsoft Graph API

Выполните шаги, описанные в разделе [Предварительные требования для доступа к API отчетов Azure Active Directory](howto-configure-prerequisites-for-reporting-api.md), чтобы убедиться в наличии подходящего набора разрешений для приложения. 

## <a name="next-steps"></a>Следующие шаги

[Использование справочника](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) 
 по API аудита [Использование справочника по API отчетов о действиях при входе](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)
