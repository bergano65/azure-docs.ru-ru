---
title: Групповое скачивание списка членов группы — Azure Active Directory портала | Документация Майкрософт
description: Добавление пользователей в центре администрирования Azure.
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
ms.openlocfilehash: 3faca8d1a2538ed03a917d6db8d54323fe626369
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81533698"
---
# <a name="bulk-download-members-of-a-group-in-azure-active-directory"></a>Пакетная загрузка членов группы в Azure Active Directory

С помощью портала Azure Active Directory (Azure AD) можно выполнить пакетную загрузку членов группы в Организации в файл значений с разделителями-запятыми (CSV).

## <a name="to-bulk-download-group-membership"></a>Для пакетного скачивания членства в группе

1. Войдите в [портал Azure](https://portal.azure.com) с помощью учетной записи администратора пользователя в Организации. Владельцы групп также могут выполнять пакетное скачивание членов групп, которыми они владеют.
1. В Azure AD выберите **группы** > **все группы**.
1. Откройте группу, членство в которой необходимо скачать, а затем выберите **члены**.
1. На странице **члены** выберите **скачать элементы** , чтобы скачать CSV-файл с перечнем членов группы.

   ![Команда скачать элементы находится на странице профиля группы.](./media/groups-bulk-download-members/download-panel.png)

## <a name="check-download-status"></a>Проверить состояние скачивания

Состояние всех ожидающих выполнения незавершенных запросов можно просмотреть на странице **результатов групповой операции** .

[![](media/groups-bulk-download-members/bulk-center.png "Check status in the Bulk Operations Results page")](media/groups-bulk-download-members/bulk-center.png#lightbox)

## <a name="bulk-download-service-limits"></a>Ограничения службы пакетного скачивания

Каждое групповое действие для загрузки списка членов группы может выполняться в течение одного часа. Это позволяет скачать список по крайней мере 500 000 членов.

## <a name="next-steps"></a>Дальнейшие шаги

- [Элементы группы группового импорта](groups-bulk-import-members.md)
- [Групповое удаление членов группы](groups-bulk-download-members.md)
