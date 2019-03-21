---
title: Проблемы при входе в специально разработанное приложение | Документы Майкрософт
description: Распространенные ошибки, из-за которых может не работать вход в приложение, разработанное вами с помощью Azure AD
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: celested
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: f4365c87f0028fe3a9c4ba35f40599359eb1455f
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/20/2019
ms.locfileid: "58259131"
---
# <a name="problems-signing-in-to-an-custom-developed-application"></a>Проблемы при входе в специально разработанное приложение

Существует несколько ошибок, которые могут препятствовать входу в приложение. Основной причиной является неправильная настройка приложений.

## <a name="errors-related-to--misconfigured-apps"></a>Ошибки, связанные с неправильной настройкой приложений

* Убедитесь, что конфигурации на портале соответствуют значениям в приложении. В частности, сравните идентификатор клиента и приложения, URL-адреса ответа, ключи и секреты клиентов, а также URI идентификатора приложения.

* Сравните ресурсы, к которым вы обращаетесь из кода с помощью настроенных на вкладке **Необходимые ресурсы** разрешений, чтобы запрашивались только настроенные ресурсы.

* Сведения об аналогичных ошибках или проблемах см. в разделе [StackOverflow в Azure AD](https://stackoverflow.com/questions/tagged/azure-active-directory).

## <a name="next-steps"></a>Дальнейшие действия

[Руководство разработчика по Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide)<br>

[Согласие и интеграция приложений с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)<br>

[Согласие и разрешения для конвергированных приложений в Azure AD версии 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)<br>

[StackOverflow в Azure AD](https://stackoverflow.com/questions/tagged/azure-active-directory>)
