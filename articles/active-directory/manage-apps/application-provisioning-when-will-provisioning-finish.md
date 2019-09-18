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
ms.openlocfilehash: ca2257a46815dd5e685b9cc746a64cede510d10a
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/17/2019
ms.locfileid: "71034189"
---
# <a name="user-provisioning-to-an-azure-ad-gallery-application-is-taking-hours-or-more"></a>Подготовка пользователей для приложения в коллекции Azure AD выполняется час и более

При первом включении автоматической подготовки для приложения начальный цикл может занять от 20 минут до нескольких часов, в зависимости от размера каталога Azure AD и числа пользователей в области для подготовки. 

Последующие операции синхронизации после начального цикла будут выполняться быстрее, так как служба подготовки сохраняет водяные знаки, представляющие состояние обеих систем после начального цикла, повышая производительность последующих синхронизаций.

## <a name="how-to-improve-provisioning-performance"></a>Как улучшить производительность подготовки

Если начальный цикл занимает больше нескольких часов, то для повышения производительности можно выполнить одно из следующих действий.

-   **Фильтры области.** Фильтры области позволяют точно настроить данные, которые служба подготовки получает из Azure AD, за счет фильтрации пользователей по значению конкретного атрибута. Дополнительные сведения о фильтрах области см. в статье [Подготовка приложений на основе атрибутов с использованием фильтров области](https://docs.microsoft.com/azure/active-directory/active-directory-saas-scoping-filters).

## <a name="next-steps"></a>Следующие шаги
[Автоматическая подготовка пользователей и ее отзыв для приложений SaaS в Azure Active Directory](user-provisioning.md)

