---
title: Устранение неполадок с управление привилегированными пользователями-Azure Active Directory | Документация Майкрософт
description: Сведения об устранении системных ошибок с помощью ролей в Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 10/18/2019
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: f54382d652c3fc59b8ff462d41ec8c0fbdbe8498
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/18/2019
ms.locfileid: "72596693"
---
# <a name="troubleshoot-a-problem-with-privileged-identity-management"></a>Устранение неполадок с управление привилегированными пользователями

Возникла проблема с управление привилегированными пользователями (PIM) в Azure Active Directory (Azure AD)? Указанные ниже далее сведения могут помочь вам восстановить нормальную работу.

## <a name="access-to-azure-resources-denied"></a>Доступ к ресурсам Azure запрещен

### <a name="problem"></a>Проблема

Как активный владелец или администратор доступа пользователей для ресурса Azure вы можете увидеть ресурс в управление привилегированными пользователями но не можете выполнять какие-либо действия, такие как создание подходящего назначения или Просмотр списка назначений ролей из ресурса. Обзорная страница. Любое из этих действий приводит к ошибке авторизации.

### <a name="cause"></a>Причина:

Эта проблема может возникать, когда роль администратора доступа пользователей для субъекта-службы PIM была случайно удалена из подписки. Для того чтобы служба управление привилегированными пользователями могла получить доступ к ресурсам Azure, субъекту-службе службы MS PIM всегда должна быть назначена [роль администратора доступа пользователей](../../role-based-access-control/built-in-roles.md#user-access-administrator) по подписке Azure.

### <a name="resolution"></a>Разрешение

Назначьте роль администратора доступа пользователей привилегированному имени участника-службы (MS – PIM) на уровне подписки. Это назначение должно предоставлять службе привилегированного управления удостоверениями доступ к ресурсам Azure. Роль может быть назначена на уровне группы управления или на уровне подписки в зависимости от требований. Дополнительные сведения о субъектах-службах см. в статье [Назначение приложения роли](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#assign-the-application-to-a-role).

## <a name="next-steps"></a>Дальнейшие действия

- [Требования к лицензии для использования управление привилегированными пользователями](subscription-requirements.md)
- [Защита привилегированного доступа для гибридных и облачных развертываний в Azure AD](../users-groups-roles/directory-admin-roles-secure.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json)
- [Развертывание управление привилегированными пользователями](pim-deployment-plan.md)
