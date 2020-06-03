---
title: API Microsoft Graph для PIM (предварительная версия) — Azure AD | Документация Майкрософт
description: Сведения об использовании API Microsoft Graph для Azure Active Directory Privileged Identity Management (PIM) (предварительная версия).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: pim
ms.topic: overview
ms.date: 01/02/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 35d5bb13cf9c3735c61173177a3b94f4594c9272
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2020
ms.locfileid: "83714140"
---
# <a name="microsoft-graph-apis-for-privileged-identity-management-preview"></a>API-интерфейсы Microsoft Graph для управления привилегированными пользователями (предварительная версия)

Вы можете выполнить задачи управления привилегированными пользователями с помощью [интерфейсов API Microsoft Graph](https://developer.microsoft.com/graph/docs/concepts/overview) для Azure Active Directory. В этой статье описаны принципы, которые важно учесть при использовании API-интерфейсов Microsoft Graph для управления привилегированными пользователями.

Дополнительные сведения об API-интерфейсах Microsoft Graph см. в [справочнике по Azure AD Privileged Identity Management API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/privilegedidentitymanagement_root).

> [!IMPORTANT]
> API-интерфейсы бета-версии в Microsoft Graph доступны в предварительной версии и могут быть изменены. Использование этих API для приложений в рабочей среде не поддерживается.

## <a name="required-permissions"></a>Необходимые разрешения

Чтобы вызвать API-интерфейсы Microsoft Graph для управления привилегированными пользователями, необходимо иметь **одно или несколько** следующих разрешений:

- `Directory.AccessAsUser.All`
- `Directory.Read.All`
- `Directory.ReadWrite.All`
- `PrivilegedAccess.ReadWrite.AzureAD`

### <a name="set-permissions"></a>Установка разрешений

Чтобы приложения могли вызывать API Microsoft Graph для управления привилегированными пользователями, они должны иметь необходимые разрешения. Самый простой способ указать необходимые разрешения — воспользоваться [платформой предоставления согласия Azure AD](../develop/consent-framework.md).

### <a name="set-permissions-in-graph-explorer"></a>Указание разрешений в песочнице Graph

Если вы используете песочницу Graph Explorer для тестирования вызовов, можно указать разрешения в ней.

1. Войдите в [песочницу Graph](https://developer.microsoft.com/graph/graph-explorer) как глобальный администратор.

1. Щелкните **Изменить разрешения**.

    ![Изменение разрешений в песочнице Graph](./media/pim-apis/graph-explorer.png)

1. Установите флажки рядом с разрешениями, которые нужно указать. Песочница Graph еще не поддерживает `PrivilegedAccess.ReadWrite.AzureAD`.

    ![Изменение разрешений в песочнице Graph](./media/pim-apis/graph-explorer-modify-permissions.png)

1. Щелкните **Изменить разрешения**, чтобы применить изменения разрешений.

## <a name="next-steps"></a>Дальнейшие действия

- [Справочник по API Azure AD Privileged Identity Management](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/privilegedidentitymanagement_root)
