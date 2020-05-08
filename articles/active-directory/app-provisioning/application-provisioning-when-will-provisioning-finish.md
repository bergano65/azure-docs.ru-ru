---
title: Подготовка пользователей к приложению из коллекции Azure AD занимает несколько часов или больше
description: Как узнать, почему подготовка для приложения может выполняться дольше, чем ожидалось
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.reviewer: asteen
ms.openlocfilehash: d663962dab058a108faa7d5310a3f2892c345a75
ms.sourcegitcommit: 3abadafcff7f28a83a3462b7630ee3d1e3189a0e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/30/2020
ms.locfileid: "82593902"
---
# <a name="user-provisioning-to-an-azure-ad-gallery-application-is-taking-hours-or-more"></a>Подготовка пользователей для приложения в коллекции Azure AD выполняется час и более

При первом включении автоматической подготовки для приложения начальный цикл может занять от 20 минут до нескольких часов, в зависимости от размера каталога Azure AD и числа пользователей в области для подготовки. 

Последующие операции синхронизации после начального цикла будут выполняться быстрее, так как служба подготовки сохраняет водяные знаки, представляющие состояние обеих систем после начального цикла, повышая производительность последующих синхронизаций.

## <a name="how-to-improve-provisioning-performance"></a>Как улучшить производительность подготовки

Если начальный цикл занимает больше нескольких часов, то для повышения производительности можно выполнить одно из следующих действий.

-   **Фильтры области.** Фильтры области позволяют точно настроить данные, которые служба подготовки получает из Azure AD, за счет фильтрации пользователей по значению конкретного атрибута. Дополнительные сведения о фильтрах области см. в статье [Подготовка приложений на основе атрибутов с использованием фильтров области](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

## <a name="next-steps"></a>Дальнейшие действия
[Автоматизируйте подготовку пользователей и отменяйте подготовку приложений SaaS с помощью Azure Active Directory](user-provisioning.md)

