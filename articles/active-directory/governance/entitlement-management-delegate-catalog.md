---
title: Делегирование управления доступом для создателей каталога в управлении назначением Azure AD (Предварительная версия) — Azure Active Directory
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
ms.date: 10/07/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: da6183599f2dacd1a98d8fe359edda741b903b50
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2019
ms.locfileid: "72170767"
---
# <a name="delegate-access-governance-to-catalog-creators-in-azure-ad-entitlement-management-preview"></a>Делегирование управления доступом к создателям каталога в управлении назначением Azure AD (Предварительная версия)

> [!IMPORTANT]
> Управление правами Azure Active Directory (Azure AD) сейчас предоставляется в общедоступной предварительной версии.
> Эта предварительная версия предоставляется без соглашения об уровне обслуживания и не рекомендована для использования рабочей среде. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены.
> Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Чтобы делегировать пользователям, которые не являются администраторами, создавать собственные каталоги, можно добавить этих пользователей к роли создателя каталога, определяемой службой управления назначением Azure AD. Вы можете добавить отдельных пользователей или группу, члены которой могут создавать каталоги.

## <a name="as-an-it-administrator-delegate-to-a-catalog-creator"></a>Делегирование автору каталога в качестве ИТ-администратора

Выполните следующие действия, чтобы назначить пользователя роли создателя каталога.

**Требуемая роль:** глобальный администратор или администратор пользователей.

1. На портале Azure щелкните **Azure Active Directory** и выберите **Управление удостоверениями**.

1. В меню слева в разделе " **Управление** назначением" щелкните " **Параметры**".

1. Нажмите кнопку **Изменить**.

    ![Параметры для добавления создателей каталога](./media/entitlement-management-delegate/settings-delegate.png)

1. В разделе **Делегирование управления** назначением щелкните **Добавить создатели каталога** , чтобы выбрать пользователей или группы, которым вы хотите делегировать эту роль управления назначением.

1. Нажмите кнопку **Выбрать**.

1. Нажмите кнопку **Сохранить**.

## <a name="next-steps"></a>Следующие шаги

- [Создание каталога ресурсов и управление им](entitlement-management-catalog-create.md)
- [Делегирование управления доступом для доступа к диспетчерам пакетов](entitlement-management-delegate-managers.md)

