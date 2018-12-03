---
title: API-интерфейсы Microsoft Graph для Azure AD Privileged Identity Management (PIM) (предварительная версия) | Документация Майкрософт
description: Сведения об использовании API-интерфейсов Microsoft Graph для Azure Active Directory Privileged Identity Management (PIM) (предварительная версия).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: pim
ms.topic: overview
ms.date: 11/13/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: e27f4c937a97b955d4f0b879ad0199cc35dcadce
ms.sourcegitcommit: 345b96d564256bcd3115910e93220c4e4cf827b3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/28/2018
ms.locfileid: "52500215"
---
# <a name="microsoft-graph-apis-for-pim-preview"></a>API Microsoft Graph для PIM (предварительная версия)

Большинство задач, выполняемых в Azure AD Privileged Identity Management (PIM) на портале Azure, можно выполнять и с помощью [API-интерфейсов Microsoft Graph](https://developer.microsoft.com/graph/docs/concepts/overview). В этой статье описаны некоторые принципы, которые важно учесть при использовании API-интерфейсов Microsoft Graph для PIM. Дополнительные сведения об API-интерфейсах Microsoft Graph см. в [справочнике по Azure AD Privileged Identity Management API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/privilegedidentitymanagement_root).

> [!IMPORTANT]
> API-интерфейсы бета-версии в Microsoft Graph доступны в предварительной версии и могут быть изменены. Использование этих API для приложений в рабочей среде не поддерживается.

## <a name="required-permissions"></a>Необходимые разрешения

Чтобы вызвать API-интерфейсы Microsoft Graph для PIM, необходимо иметь **одно или несколько** следующих разрешений:

- `Directory.AccessAsUser.All`
- `Directory.Read.All`
- `Directory.ReadWrite.All`
- `PrivilegedAccess.ReadWrite.AzureAD`

### <a name="set-permissions"></a>Установка разрешений

Чтобы приложения могли вызывать API Microsoft Graph для PIM, они должны иметь необходимые разрешения. Самый простой способ указать необходимые разрешения — воспользоваться [платформой предоставления согласия Azure AD](../develop/consent-framework.md).

### <a name="set-permissions-in-graph-explorer"></a>Указание разрешений в песочнице Graph

Если вы используете песочницу Graph Explorer для тестирования вызовов, можно указать разрешения в ней.

1. Войдите в [песочницу Graph](https://developer.microsoft.com/graph/graph-explorer) как глобальный администратор.

1. Щелкните **Изменить разрешения**.

    ![Изменение разрешений в песочнице Graph](./media/pim-apis/graph-explorer.png)

1. Установите флажки рядом с разрешениями, которые нужно указать. Песочница Graph еще не поддерживает `PrivilegedAccess.ReadWrite.AzureAD`.

    ![Изменение разрешений в песочнице Graph](./media/pim-apis/graph-explorer-modify-permissions.png)

1. Щелкните **Изменить разрешения**, чтобы применить изменения разрешений.

## <a name="next-steps"></a>Дополнительная информация

- [Справочник по API Azure AD Privileged Identity Management](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/privilegedidentitymanagement_root)
