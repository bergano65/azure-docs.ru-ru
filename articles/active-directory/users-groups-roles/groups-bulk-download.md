---
title: Загрузите список групп на портале Active Directory Azure (ru) Документы Майкрософт
description: Загрузка групповых свойств оптом в центре админ-центра Azure в Active Directory Azure.
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
ms.openlocfilehash: 4b08e807e179270b63ca81d3777c230c3e129c3a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "72517150"
---
# <a name="bulk-download-a-list-of-groups-preview-in-azure-active-directory"></a>Массовый скачивание списка групп (предварительный просмотр) в Active Directory Azure

Используя портал Azure Active Directory (Azure AD), можно загрузить список всех групп в организации в файл, разделенный на запятую (CSV).

## <a name="to-download-a-list-of-groups"></a>Загрузить список групп

1. Вопием [на портал Azure](https://portal.azure.com) с учетной записью администратора в организации.
1. В Azure AD выберите **группы** > **загрузки.**
1. На странице **загрузки групп** выберите **Начало** получения файла CSV с указанием ваших групп.

   ![Команда групп загрузки находится на странице All Groups](./media/groups-bulk-download/bulk-download.png)

## <a name="check-download-status"></a>Проверка состояния загрузки

Вы можете увидеть состояние всех ожидающих натковых запросов на странице **результаты (предварительный просмотр) результатов операции Bulk.**

   ![Страница результатов результатов массовых операций показывает состояние запроса навалом](./media/groups-bulk-download/bulk-center.png)

## <a name="bulk-download-service-limits"></a>Ограничения обслуживания массовых скачиваний

Каждое массовое действие для загрузки списка групп может длиться до одного часа. Это позволяет загрузить список не менее 300 000 групп.

## <a name="next-steps"></a>Дальнейшие действия

- [Массовое удаление членов группы](groups-bulk-remove-members.md)
- [Скачать членов группы](groups-bulk-download-members.md)
