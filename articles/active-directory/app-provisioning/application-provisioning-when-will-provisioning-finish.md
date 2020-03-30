---
title: Подготовка пользователей к приложению Azure AD Gallery занимает несколько часов или более
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77522651"
---
# <a name="user-provisioning-to-an-azure-ad-gallery-application-is-taking-hours-or-more"></a>Подготовка пользователей для приложения в коллекции Azure AD выполняется час и более

При первом включении автоматической подготовки приложения начальный цикл может занять от 20 минут до нескольких часов, в зависимости от размера каталога Azure AD и количества пользователей в области подготовки. 

Последующие синхронизации после первоначального цикла будут более быстрыми, так как служба обеспечения хранит водяные знаки, которые представляют состояние обеих систем после первоначального цикла, улучшая производительность последующих синхронизаций.

## <a name="how-to-improve-provisioning-performance"></a>Как улучшить производительность подготовки

Если начальный цикл занимает более нескольких часов, есть одна вещь, которую вы можете сделать, чтобы улучшить производительность:

-   **Фильтры области.** Фильтры области позволяют точно настроить данные, которые служба подготовки получает из Azure AD, за счет фильтрации пользователей по значению конкретного атрибута. Дополнительные сведения о фильтрах области см. в статье [Подготовка приложений на основе атрибутов с использованием фильтров области](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

## <a name="next-steps"></a>Дальнейшие действия
[Автоматизация обеспечения и деирование приложений SaaS с помощью active-каталога Azure](user-provisioning.md)

