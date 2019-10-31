---
title: Делегирование управления доступом для создателей каталога в управлении назначением Azure AD — Azure Active Directory
description: Узнайте, как делегировать управление доступом от ИТ – администраторов в каталог создателям и руководителям проектов, чтобы они могли управлять доступом.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/26/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: f71007b886d3cc25a7cf9dc23d784144ed4e1fbd
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73174379"
---
# <a name="delegate-access-governance-to-catalog-creators-in-azure-ad-entitlement-management"></a>Делегирование управления доступом к авторам каталогов в управлении назначением Azure AD

Чтобы делегировать пользователям, которые не являются администраторами, создавать собственные каталоги, можно добавить этих пользователей к роли создателя каталога, определяемой службой управления назначением Azure AD. Вы можете добавить отдельных пользователей или группу, члены которой могут создавать каталоги.

## <a name="as-an-it-administrator-delegate-to-a-catalog-creator"></a>Делегирование автору каталога в качестве ИТ-администратора

Выполните следующие действия, чтобы назначить пользователя роли создателя каталога.

**Предварительная роль:** Глобальный администратор или администратор пользователей

1. На портале Azure щелкните **Azure Active Directory** и выберите **Управление удостоверениями**.

1. В меню слева в разделе " **Управление** назначением" щелкните " **Параметры**".

1. Нажмите кнопку **Изменить**.

    ![Параметры для добавления создателей каталога](./media/entitlement-management-delegate-catalog/settings-delegate.png)

1. В разделе **Делегирование управления** назначением щелкните **Добавить создатели каталога** , чтобы выбрать пользователей или группы, которым вы хотите делегировать эту роль управления назначением.

1. Нажмите кнопку **Выбрать**.

1. В нижней части страницы нажмите кнопку **Save**.

## <a name="allow-delegated-roles-to-access-the-azure-portal"></a>Разрешить делегированным ролям доступ к портал Azure

Чтобы разрешить делегированные роли, такие как создатели каталога и диспетчеры пакетов доступа, для доступа к портал Azure для управления пакетами доступа, следует проверить параметр портала администрирования.

**Предварительная роль:** Глобальный администратор или администратор пользователей

1. В портал Azure щелкните **Azure Active Directory** и выберите **Пользователи**.

1. В меню слева щелкните **Параметры пользователя**.

1. Убедитесь, что для параметра **ограничить доступ к порталу администрирования Azure AD** задано значение **нет**.

    ![Параметры пользователя Azure AD — портал администрирования](./media/entitlement-management-delegate-catalog/user-settings.png)

## <a name="next-steps"></a>Дальнейшие действия

- [Создание каталога ресурсов и управление им](entitlement-management-catalog-create.md)
- [Делегирование управления доступом для доступа к диспетчерам пакетов](entitlement-management-delegate-managers.md)

