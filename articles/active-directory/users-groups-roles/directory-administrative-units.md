---
title: Управление административными единицами (предварительный просмотр) - Azure AD Документы Майкрософт
description: Использование административных единиц для делегирования разрешений в Azure Active Directory на фрагментарном уровне
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: article
ms.subservice: users-groups-roles
ms.workload: identity
ms.date: 11/13/2019
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3c4958975a080a98fb900bf317229ba9eda795d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74028413"
---
# <a name="administrative-units-management-in-azure-active-directory-preview"></a>Управление административными подразделениями в Active Directory Azure (предварительный просмотр)

В этой статье описаны административные единицы в Active Directory Azure (Azure AD). Административная единица — это ресурс Azure AD, который может быть контейнером для других ресурсов Azure AD. В этом выпуске предварительной версии ресурсами могут быть только пользователи. Например, администратор-администратор учетной записи пользователя может обновлять информацию о профиле, сбросить пароли и назначать лицензии пользователям только в их административном подразделении.

Можно использовать административные единицы для делегирования административных разрешений на подмножества пользователей и применения политик для подмножества пользователей. Можно использовать административные единицы для делегирования разрешений региональным администраторам или для настройки политики на гранулированном уровне.

## <a name="deployment-scenario"></a>Сценарий развертывания

Административные подразделения могут быть полезны в организациях с независимыми подразделениями. Рассмотрим пример крупного университета, состоящего из многих автономных школ (Школа бизнеса, Инженерная школа и т.д.), в каждом из которых есть свои ИТ-администраторы, которые контролируют доступ, управляют пользователями и устанавливают политики для своей школы. Центральный администратор может создать административную единицу для Школы бизнеса и заселить ее только студентами и персоналом бизнес-школы. Затем центральный администратор может добавить ИТ-персонал бизнес-школы в масштабную роль, которая предоставляет административные разрешения только пользователям Azure AD в административном подразделении бизнес-школы.

## <a name="license-requirements"></a>Требования лицензий

Для использования административных единиц требуется лицензия Azure Active Directory Premium для каждого административного адм-амин. Для получения дополнительной [Getting started with Azure AD Premium](../fundamentals/active-directory-get-started-premium.md)информации см.

## <a name="managing-administrative-units"></a>Управление административными единицами

В этом выпуске предварительного просмотра единственным способом создания и управления административными единицами является использование модуля Active Directory Module Azure для cmdlets Windows PowerShell, как описано в [работе с административными единицами](https://docs.microsoft.com/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0)

Для получения дополнительной информации о требованиях к программному обеспечению и установке модуля Azure AD, а также справочной [информации](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0)о cmdlets Azure AD Module для управления административными единицами, включая синтаксис, описания параметров и примеры, см.

## <a name="next-steps"></a>Дальнейшие действия

[Выпуски Azure Active Directory](../fundamentals/active-directory-whatis.md)
