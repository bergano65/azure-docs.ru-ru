---
title: Подготовка пользователей для приложения в коллекции Azure AD выполняется час и более | Документация Майкрософт
description: Как узнать, почему подготовка для приложения может выполняться дольше, чем ожидалось
services: active-directory
documentationcenter: ''
author: barbkess
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: barbkess
ms.reviewer: asteen
ms.openlocfilehash: 94859aa57ef6f565c1fe7ed4b199aff997661d0b
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54478091"
---
# <a name="user-provisioning-to-an-azure-ad-gallery-application-is-taking-hours-or-more"></a>Подготовка пользователей для приложения в коллекции Azure AD выполняется час и более

При первом включении автоматической подготовки для приложения начальная синхронизация может выполняться от 20 минут до нескольких часов в зависимости от размера каталога Azure AD и количества подготавливаемых пользователей. 

Последующие операции синхронизации занимают меньше времени, так как служба подготовки сохраняет "водяные знаки", которые представляют состояние обеих систем после начальной синхронизации. Это позволяет повысить скорость последующих операций синхронизации.

## <a name="how-to-improve-provisioning-performance"></a>Как улучшить производительность подготовки

Если начальная синхронизация занимает несколько часов, повысить производительность можно только одним способом:

-   **Фильтры области.** Фильтры области позволяют точно настроить данные, которые служба подготовки получает из Azure AD, за счет фильтрации пользователей по значению конкретного атрибута. Дополнительные сведения о фильтрах области см. в статье [Подготовка приложений на основе атрибутов с использованием фильтров области](https://docs.microsoft.com/azure/active-directory/active-directory-saas-scoping-filters).

## <a name="next-steps"></a>Дополнительная информация
[Автоматическая подготовка пользователей и ее отзыв для приложений SaaS в Azure Active Directory](user-provisioning.md)

