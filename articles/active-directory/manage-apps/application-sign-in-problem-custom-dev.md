---
title: Проблемы при входе в специально разработанное приложение | Документы Майкрософт
description: Распространенные ошибки, которые могут привести к невозможности входа в приложение, разработанное с помощью Azure AD.
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: kenwith
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: cdfc2a39c6bd3b68df7feb978d2548ad67631235
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84759136"
---
# <a name="problems-signing-in-to-a-custom-developed-application"></a>Проблемы при входе в специально разработанное приложение

Существует несколько ошибок, которые могут препятствовать входу в приложение. Основной причиной является неправильная настройка приложений.

## <a name="errors-related-to--misconfigured-apps"></a>Ошибки, связанные с неправильной настройкой приложений

* Убедитесь, что конфигурации на портале соответствуют значениям в приложении. В частности, сравните идентификатор клиента и приложения, URL-адреса ответа, ключи и секреты клиентов, а также URI идентификатора приложения.

* Сравните ресурсы, к которым вы обращаетесь из кода с помощью настроенных на вкладке **Необходимые ресурсы** разрешений, чтобы запрашивались только настроенные ресурсы.

* Сведения об аналогичных ошибках или проблемах см. в разделе [StackOverflow в Azure AD](https://stackoverflow.com/questions/tagged/azure-active-directory).

## <a name="next-steps"></a>Дальнейшие шаги

[Руководство разработчика по Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide)<br>

[Согласие и интеграция приложений с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)<br>

[Разрешения и согласие для конечной точки платформы удостоверений Майкрософт](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)<br>

[StackOverflow в Azure AD](https://stackoverflow.com/questions/tagged/azure-active-directory>)
