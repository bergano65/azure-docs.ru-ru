---
title: Скачайте список групп на портале Azure Active Directory | Документация Майкрософт
description: Пакетная служба скачивает свойства группы в центре администрирования Azure в Azure Active Directory.
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
ms.openlocfilehash: 72f7776fde4558a49830ecbf14c77fa50e80bf36
ms.sourcegitcommit: d70c74e11fa95f70077620b4613bb35d9bf78484
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/11/2019
ms.locfileid: "70910966"
---
# <a name="bulk-download-a-list-of-groups-preview-in-azure-active-directory"></a>Выполнить пакетную загрузку списка групп (Предварительная версия) в Azure Active Directory

С помощью портала Azure Active Directory (Azure AD) можно выполнить пакетную загрузку списка всех групп в Организации в файл значений с разделителями-запятыми (CSV).

> [!NOTE]
> Групповые операции Azure AD — это общедоступная Предварительная версия Azure AD. они доступны с любым платным планом лицензирования Azure AD. Дополнительные сведения об условиях использования предварительной версии см. в разделе Дополнительные [условия использования для Microsoft Azure предварительного просмотра](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="bulk-download-service-limits"></a>Ограничения службы пакетного скачивания

Каждое групповое действие по созданию списка групп может выполняться в течение одного часа. Это позволяет создавать и скачивать список по крайней мере 300 000 групп.

## <a name="to-download-a-list-of-groups"></a>Загрузка списка групп

1. Войдите в [портал Azure](https://portal.azure.com) с помощью учетной записи администратора в Организации.
1. В Azure AD выберите **группы** > **скачать группы**.
1. На странице **загрузки групп** выберите **начать** , чтобы получить CSV-файл со списком ваших групп.

   ![Команда скачать группы находится на странице все группы.](./media/groups-bulk-download/bulk-download.png)

## <a name="check-download-status"></a>Проверить состояние скачивания

Состояние всех ожидающих выполнения незавершенных запросов можно просмотреть на странице **Результаты групповой операции (Предварительная версия)** .

   ![На странице результатов с массовыми операциями отображается состояние неполного запроса.](./media/groups-bulk-download/bulk-center.png)

## <a name="next-steps"></a>Следующие шаги

- [Групповое удаление членов группы](groups-bulk-remove-members.md)
- [Скачать членов группы](groups-bulk-download-members.md)
