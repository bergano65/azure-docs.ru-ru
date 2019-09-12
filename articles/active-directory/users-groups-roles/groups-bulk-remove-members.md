---
title: Групповое удаление членов группы путем отправки CSV-файла Azure Active Directory | Документация Майкрософт
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
ms.openlocfilehash: b153db3570f10ad5ad130dedd0bd20fe22776ed6
ms.sourcegitcommit: d70c74e11fa95f70077620b4613bb35d9bf78484
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/11/2019
ms.locfileid: "70910848"
---
# <a name="bulk-remove-group-members-preview-in-azure-active-directory"></a>Групповое удаление членов группы (Предварительная версия) в Azure Active Directory

С помощью портала Azure Active Directory (Azure AD) можно удалить большое количество членов из группы, используя файл данных с разделителями-запятыми (CSV) для выполнения группового удаления членов группы.

> [!NOTE]
> Групповые операции Azure AD — это общедоступная Предварительная версия Azure AD. они доступны с любым платным планом лицензирования Azure AD. Дополнительные сведения об условиях использования предварительной версии см. в разделе Дополнительные [условия использования для Microsoft Azure предварительного просмотра](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="bulk-removal-service-limits"></a>Ограничения службы пакетного удаления

Каждое групповое действие, которое удаляет список членов группы, может выполняться в течение одного часа. Это позволяет удалить список по крайней мере 40 000 элементов.

## <a name="to-bulk-remove-group-members"></a>Групповое удаление членов группы

1. Войдите в [портал Azure](https://portal.azure.com) с помощью учетной записи администратора пользователя в Организации. Владельцы групп также могут удалять членов групп, которыми они владеют.
1. В Azure AD выберите **группы** > **все группы**.
1. Откройте группу, из которой вы удаляете участников, а затем выберите **элементы**.
1. На странице **члены** выберите **Удалить участников** для скачивания, обновления и передачи CSV-файла со списком участников, которые требуется удалить из группы.

   ![Команда "удалить члены" находится на странице профиля для группы](./media/groups-bulk-remove-members/remove-panel.png)

## <a name="check-removal-status"></a>Проверка состояния удаления

Состояние всех ожидающих выполнения незавершенных запросов можно просмотреть на странице **Результаты групповой операции (Предварительная версия)** .

   ![На странице результатов с массовыми операциями отображается состояние неполного запроса.](./media/groups-bulk-remove-members/bulk-center.png)

## <a name="next-steps"></a>Следующие шаги

- [Элементы группы группового импорта](groups-bulk-import-members.md)
- [Скачать членов группы](groups-bulk-download-members.md)
