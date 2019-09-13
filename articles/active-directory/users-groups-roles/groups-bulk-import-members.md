---
title: Передача группового импорта для добавления участников в группу — Azure Active Directory | Документация Майкрософт
description: Добавляйте элементы группы в группу в центре администрирования Azure Active Directory.
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
ms.openlocfilehash: 0335522b398c22fb395305b33bac5a56ba565ee2
ms.sourcegitcommit: d70c74e11fa95f70077620b4613bb35d9bf78484
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/11/2019
ms.locfileid: "70911095"
---
# <a name="bulk-import-group-members-preview-in-azure-active-directory"></a>Члены группы группового импорта (Предварительная версия) в Azure Active Directory

С помощью портала Azure Active Directory (Azure AD) можно добавить большое число членов в группу, используя файл данных с разделителями-запятыми (CSV) для группового импорта элементов группы.

> [!NOTE]
> Групповые операции Azure AD — это общедоступная Предварительная версия Azure AD. они доступны с любым платным планом лицензирования Azure AD. Дополнительные сведения об условиях использования предварительной версии см. в разделе Дополнительные [условия использования для Microsoft Azure предварительного просмотра](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="bulk-import-service-limits"></a>Ограничения службы для полного импорта

Каждое групповое действие для импорта списка членов группы может выполняться в течение одного часа. Это позволяет выполнять импорт списка по крайней мере 40 000 членов.

## <a name="to-bulk-import-group-members"></a>Для группового импорта элементов группы

1. Войдите в [портал Azure](https://portal.azure.com) с помощью учетной записи администратора пользователя в Организации. Владельцы групп также могут выполнять групповое импортирование членов групп, которыми они владеют.
1. В Azure AD выберите **группы** > **все группы**.
1. Откройте группу, в которую добавляются члены, а затем выберите **элементы**.
1. На странице **члены** выберите **импортировать элементы** , чтобы скачать, обновить и передать CSV-файл со списком членов, которые требуется импортировать в группу.

   ![Команда "Импорт элементов" находится на странице профиля для группы](./media/groups-bulk-import-members/import-panel.png)

## <a name="check-import-status"></a>Проверить состояние импорта

Состояние всех ожидающих выполнения незавершенных запросов можно просмотреть на странице **Результаты групповой операции (Предварительная версия)** .

   ![На странице результатов с массовыми операциями отображается состояние неполного запроса.](./media/groups-bulk-import-members/bulk-center.png)

## <a name="next-steps"></a>Следующие шаги

- [Групповое удаление членов группы](groups-bulk-remove-members.md)
- [Скачать членов группы](groups-bulk-download-members.md)
