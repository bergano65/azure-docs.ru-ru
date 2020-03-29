---
title: Устранение проблемы с привилегированным управлением идентификацией - Активный каталог Azure (ru) Документы Майкрософт
description: Узнайте, как устранить ошибки системы с ролями в Azure AD Privileged Identity Management (PIM).
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
ms.openlocfilehash: 474f2634e6f7ddc1840548c39ae86cb54c3bf08e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78299692"
---
# <a name="troubleshoot-a-problem-with-privileged-identity-management"></a>Устранение проблемы с привилегированным управлением идентификацией

Возникли ли проблемы с привилегированным управлением идентификацией (PIM) в active-каталоге Azure (Azure AD)? Указанные ниже далее сведения могут помочь вам восстановить нормальную работу.

## <a name="access-to-azure-resources-denied"></a>Отказ в доступе к ресурсам Azure

### <a name="problem"></a>Проблема

Как активный владелец или администратор доступа пользователей для ресурса Azure, вы можете видеть свой ресурс внутри Privileged Identity Management, но не можете выполнять любые действия, такие как назначение на получение подходящего или просмотр списка назначений ролей из ресурса страница обзора. Любое из этих действий приводит к ошибке авторизации.

### <a name="cause"></a>Причина

Эта проблема может произойти, когда роль администратора пользовательского доступа для принципала службы PIM была случайно удалена из подписки. Для того чтобы служба Privileged Identity Management могла получить доступ к ресурсам Azure, директору службы MS-PIM всегда должна быть назначена [роль администратора пользовательского доступа](../../role-based-access-control/built-in-roles.md#user-access-administrator) по подписке Azure.

### <a name="resolution"></a>Решение

Назначаем роль администратора пользовательского доступа главному имени службы управления привилегированными удостоверениями (MS-PIM) на уровне подписки. Это назначение должно позволить службе управления привилегированными удостоверениями получить доступ к ресурсам Azure. Роль может быть назначена на уровне группы управления или на уровне подписки, в зависимости от ваших требований. Для получения дополнительной информации директоров службы, [см.](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#assign-a-role-to-the-application)

## <a name="next-steps"></a>Дальнейшие действия

- [License requirements to use Privileged Identity Management](subscription-requirements.md) (Требования к лицензии для использования PIM)
- [Защита привилегированного доступа для гибридных и облачных развертываний в Azure AD](../users-groups-roles/directory-admin-roles-secure.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json)
- [Развертывание управления привилегированными пользователями](pim-deployment-plan.md)
