---
title: Массовое удаление пользователей на портале Active Directory Azure Документы Майкрософт
description: Удалить пользователей оптом в центре админ-центра Azure в Active Directory Azure
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 04/16/2020
ms.topic: conceptual
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: beb8b4f35dc5f02e59cced05a6bcfc235d42f996
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81532831"
---
# <a name="bulk-delete-users-in-azure-active-directory"></a>Массовое удаление пользователей в Active Directory Azure

Используя портал Azure Active Directory (Azure AD), можно удалить большое количество членов в группу, используя файл, разделенный на запятую (CSV), для массового удаления пользователей.

## <a name="to-bulk-delete-users"></a>Для массового удаления пользователей

1. [Восвай в организацию Azure AD](https://aad.portal.azure.com) с учетной записью, которая является администратором пользователя в организации.
1. В Azure AD выберите **Пользователей** > **Массовые удаления**.
1. На странице **пользователя Bulk удалить,** выберите **Скачать,** чтобы получить действительный файл CSV свойств пользователя.

   ![Выберите локальный файл CSV, в котором вы перечисляете пользователей, которых вы хотите удалить](./media/users-bulk-delete/bulk-delete.png)

1. Откройте файл CSV и добавьте строку для каждого пользователя, который вы хотите удалить. Единственное требуемое значение — **основное имя пользователя.** Затем сохраните файл.

   ![Файл CSV содержит имена и идентионы пользователей для удаления](./media/users-bulk-delete/delete-csv-file.png)

1. На **странице пользователя Bulk удалить,** под **загрузить ваш файл csv**, просматривать файл. При выборе файла и нажмите отправить, проверка файла CSV начинается.
1. После проверки содержимого файла вы увидите сообщение **Файл успешно передан**. Если будут обнаружены ошибки, их нужно исправить для отправки задания.
1. Когда файл проходит проверку, выберите **отправку,** чтобы начать операцию навалом Azure, которая удаляет пользователей.
1. Когда операция удаления завершится, вы увидите уведомление о том, что основная операция выполнена успешно.

Если есть ошибки, вы можете загрузить и просмотреть файл результатов на странице **результатов операции Bulk.** Файл содержит причину каждой ошибки.

## <a name="check-status"></a>Проверка состояния

Вы можете увидеть состояние всех ожидающих натковых запросов на странице **результатов операции Bulk.**

   [![](media/users-bulk-delete/bulk-center.png "Check delete status in the Bulk Operations Results page")](media/users-bulk-delete/bulk-center.png#lightbox)

Далее можно проверить, что пользователи, которые вы удалили, существуют в организации Azure AD либо на портале Azure, либо с помощью PowerShell.

## <a name="verify-deleted-users-in-the-azure-portal"></a>Проверка удаленных пользователей на портале Azure

1. Войдите на портал Azure с учетной записью администратора пользователей в организации.
1. В области навигации выберите **Azure Active Directory**.
1. В разделе **Управление** выберите **Пользователи**.
1. Под **Show**выберите только **всех пользователей** и убедитесь, что удаленные пользователи больше не перечислены.

### <a name="verify-deleted-users-with-powershell"></a>Проверить удаленных пользователей с помощью PowerShell

Выполните следующую команду:

``` PowerShell
Get-AzureADUser -Filter "UserType eq 'Member'"
```

Убедитесь, что пользователи, которые вы удалили, больше не перечислены.

## <a name="next-steps"></a>Следующие шаги

- [Массовое добавление пользователей](users-bulk-add.md)
- [Скачать список пользователей](users-bulk-download.md)
- [Массовые восстановления пользователей](users-bulk-restore.md)
