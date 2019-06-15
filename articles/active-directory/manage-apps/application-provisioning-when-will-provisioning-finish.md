---
title: Подготовка пользователей для приложения в коллекции Azure AD выполняется час и более | Документация Майкрософт
description: Как узнать, почему подготовка для приложения может выполняться дольше, чем ожидалось
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8622a7bbd913710a2173399048baab7067d2fae7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65783794"
---
# <a name="user-provisioning-to-an-azure-ad-gallery-application-is-taking-hours-or-more"></a>Подготовка пользователей для приложения в коллекции Azure AD выполняется час и более

При первом включении автоматической подготовки для приложения начальная синхронизация может выполняться от 20 минут до нескольких часов в зависимости от размера каталога Azure AD и количества подготавливаемых пользователей. 

Последующие операции синхронизации занимают меньше времени, так как служба подготовки сохраняет "водяные знаки", которые представляют состояние обеих систем после начальной синхронизации. Это позволяет повысить скорость последующих операций синхронизации.

## <a name="how-to-improve-provisioning-performance"></a>Как улучшить производительность подготовки

Если начальная синхронизация занимает несколько часов, повысить производительность можно только одним способом:

-   **Фильтры области.** Фильтры области позволяют точно настроить данные, которые служба подготовки получает из Azure AD, за счет фильтрации пользователей по значению конкретного атрибута. Дополнительные сведения о фильтрах области см. в статье [Подготовка приложений на основе атрибутов с использованием фильтров области](https://docs.microsoft.com/azure/active-directory/active-directory-saas-scoping-filters).

## <a name="next-steps"></a>Дальнейшие действия
[Автоматическая подготовка пользователей и ее отзыв для приложений SaaS в Azure Active Directory](user-provisioning.md)

