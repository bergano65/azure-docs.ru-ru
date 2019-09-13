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
ms.openlocfilehash: 120b5606dd6b6f4451b563843f112bd614341eac
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/11/2019
ms.locfileid: "70914796"
---
# <a name="bulk-download-members-of-a-group-preview-in-azure-active-directory"></a>Пакетная загрузка участников группы (Предварительная версия) в Azure Active Directory

С помощью портала Azure Active Directory (Azure AD) можно выполнить пакетную загрузку членов группы в Организации в файл значений с разделителями-запятыми (CSV).

> [!NOTE]
> Групповые операции Azure AD — это общедоступная Предварительная версия Azure AD. они доступны с любым платным планом лицензирования Azure AD. Дополнительные сведения об условиях использования предварительной версии см. в разделе Дополнительные [условия использования для Microsoft Azure предварительного просмотра](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="bulk-download-service-limits"></a>Ограничения службы пакетного скачивания

Каждое групповое действие для загрузки списка членов группы может выполняться в течение одного часа. Это позволяет скачать список по крайней мере 500 000 членов.

## <a name="to-bulk-download-group-membership"></a>Для пакетного скачивания членства в группе

1. Войдите в [портал Azure](https://portal.azure.com) с помощью учетной записи администратора пользователя в Организации. Владельцы групп также могут выполнять пакетное скачивание членов групп, которыми они владеют.
1. В Azure AD выберите **группы** > **все группы**.
1. Откройте группу, членство в которой необходимо скачать, а затем выберите **члены**.
1. На странице **члены** выберите **скачать элементы** , чтобы скачать CSV-файл с перечнем членов группы.

   ![Команда скачать элементы находится на странице профиля группы.](./media/groups-bulk-download-members/download-panel.png)

## <a name="check-download-status"></a>Проверить состояние скачивания

Состояние всех ожидающих выполнения незавершенных запросов можно просмотреть на странице **Результаты групповой операции (Предварительная версия)** .

   ![На странице результатов с массовыми операциями отображается состояние неполного запроса.](./media/groups-bulk-download-members/bulk-center.png)

## <a name="next-steps"></a>Следующие шаги

- [Элементы группы группового импорта](groups-bulk-import-members.md)
- [Групповое удаление членов группы](groups-bulk-download-members.md)
