---
title: Групповое скачивание списка членов группы — Azure Active Directory портала | Документация Майкрософт
description: Добавление пользователей в центре администрирования Azure.
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 09/11/2019
ms.topic: conceptual
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4e29aacb1357509e2b000a9d05c5ced8f9a30dce
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/17/2019
ms.locfileid: "72517155"
---
# <a name="bulk-download-members-of-a-group-preview-in-azure-active-directory"></a>Пакетная загрузка участников группы (Предварительная версия) в Azure Active Directory

С помощью портала Azure Active Directory (Azure AD) можно выполнить пакетную загрузку членов группы в Организации в файл значений с разделителями-запятыми (CSV).

## <a name="to-bulk-download-group-membership"></a>Для пакетного скачивания членства в группе

1. Войдите в [портал Azure](https://portal.azure.com) с помощью учетной записи администратора пользователя в Организации. Владельцы групп также могут выполнять пакетное скачивание членов групп, которыми они владеют.
1. В Azure AD выберите **группы**  > **все группы**.
1. Откройте группу, членство в которой необходимо скачать, а затем выберите **члены**.
1. На странице **члены** выберите **скачать элементы** , чтобы скачать CSV-файл с перечнем членов группы.

   ![Команда скачать элементы находится на странице профиля группы.](./media/groups-bulk-download-members/download-panel.png)

## <a name="check-download-status"></a>Проверить состояние скачивания

Состояние всех ожидающих выполнения незавершенных запросов можно просмотреть на странице **Результаты групповой операции (Предварительная версия)** .

   ![На странице результатов с массовыми операциями отображается состояние неполного запроса.](./media/groups-bulk-download-members/bulk-center.png)

## <a name="bulk-download-service-limits"></a>Ограничения службы пакетного скачивания

Каждое групповое действие для загрузки списка членов группы может выполняться в течение одного часа. Это позволяет скачать список по крайней мере 500 000 членов.

## <a name="next-steps"></a>Дальнейшие действия

- [Элементы группы группового импорта](groups-bulk-import-members.md)
- [Групповое удаление членов группы](groups-bulk-download-members.md)
