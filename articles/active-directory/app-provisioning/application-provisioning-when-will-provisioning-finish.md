---
title: Подготовка пользователей к приложению из коллекции Azure AD занимает несколько часов или больше
description: Как узнать, почему подготовка для приложения может выполняться дольше, чем ожидалось
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: bb22d19733fbeb162fba55dd732c10e552879c78
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "77522651"
---
# <a name="user-provisioning-to-an-azure-ad-gallery-application-is-taking-hours-or-more"></a>Подготовка пользователей для приложения в коллекции Azure AD выполняется час и более

При первом включении автоматической подготовки для приложения начальный цикл может занять от 20 минут до нескольких часов, в зависимости от размера каталога Azure AD и числа пользователей в области для подготовки. 

Последующие операции синхронизации после начального цикла будут выполняться быстрее, так как служба подготовки сохраняет водяные знаки, представляющие состояние обеих систем после начального цикла, повышая производительность последующих синхронизаций.

## <a name="how-to-improve-provisioning-performance"></a>Как улучшить производительность подготовки

Если начальный цикл занимает больше нескольких часов, то для повышения производительности можно выполнить одно из следующих действий.

-   **Фильтры области.** Фильтры области позволяют точно настроить данные, которые служба подготовки получает из Azure AD, за счет фильтрации пользователей по значению конкретного атрибута. Дополнительные сведения о фильтрах области см. в статье [Подготовка приложений на основе атрибутов с использованием фильтров области](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

## <a name="next-steps"></a>Дальнейшие действия
[Автоматизируйте подготовку пользователей и отменяйте подготовку приложений SaaS с помощью Azure Active Directory](user-provisioning.md)

