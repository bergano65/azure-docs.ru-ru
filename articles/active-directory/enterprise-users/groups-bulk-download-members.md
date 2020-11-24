---
title: Групповое скачивание списка членов группы — Azure Active Directory портала | Документация Майкрософт
description: Добавление пользователей в центре администрирования Azure.
services: active-directory
author: curtand
ms.author: curtand
manager: daveba
ms.date: 11/15/2020
ms.topic: how-to
ms.service: active-directory
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: f90a790f5f7862d3b19dea34505dabc64ea00172
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/23/2020
ms.locfileid: "95490799"
---
# <a name="bulk-download-members-of-a-group-in-azure-active-directory"></a>Пакетная загрузка членов группы в Azure Active Directory

С помощью портала Azure Active Directory (Azure AD) можно выполнить пакетную загрузку членов группы в Организации в файл значений с разделителями-запятыми (CSV).

## <a name="to-bulk-download-group-membership"></a>Для пакетного скачивания членства в группе

1. Войдите на [портал Azure](https://portal.azure.com) с учетной записью администратора пользователей в организации. Владельцы групп также могут выполнять пакетное скачивание членов групп, которыми они владеют.
1. В Azure AD выберите **Группы** > **Все группы**.
1. Откройте группу, членство в которой необходимо скачать, а затем выберите **члены**.
1. На странице **члены** выберите **скачать элементы** , чтобы скачать CSV-файл с перечнем членов группы.

   ![Команда скачать элементы находится на странице профиля группы.](./media/groups-bulk-download-members/download-panel.png)

## <a name="check-download-status"></a>Проверить состояние скачивания

Состояние всех ожидающих выполнения массовых запросов можно просмотреть на странице **Результаты массовой операции**.

[![Проверьте состояние на странице результатов с массовыми операциями.](./media/groups-bulk-download-members/bulk-center.png)](./media/groups-bulk-download-members/bulk-center.png#lightbox)

## <a name="bulk-download-service-limits"></a>Ограничения службы пакетного скачивания

Каждое групповое действие для загрузки списка членов группы может выполняться в течение одного часа. Это позволяет скачать список по крайней мере 500 000 членов.

## <a name="next-steps"></a>Дальнейшие действия

- [Групповой импорт участников группы](groups-bulk-import-members.md)
- [Массовое удаление участников группы](groups-bulk-download-members.md)
