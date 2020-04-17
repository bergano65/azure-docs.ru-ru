---
title: Загрузите список групп на портале Active Directory Azure (ru) Документы Майкрософт
description: Загрузка групповых свойств оптом в центре админ-центра Azure в Active Directory Azure.
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
ms.openlocfilehash: 59983678c1b14d6aa87a7b500605e3abeb6a9b85
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81533550"
---
# <a name="bulk-download-a-list-of-groups-in-azure-active-directory"></a>Массовый скачивание списка групп в Active Directory Azure

Используя портал Azure Active Directory (Azure AD), можно загрузить список всех групп в организации в файл, разделенный на запятую (CSV).

## <a name="to-download-a-list-of-groups"></a>Загрузить список групп

1. Вопием [на портал Azure](https://portal.azure.com) с учетной записью администратора в организации.
1. В Azure AD выберите **группы** > **загрузки.**
1. На странице **загрузки групп** выберите **Начало** получения файла CSV с указанием ваших групп.

   ![Команда групп загрузки находится на странице All Groups](./media/groups-bulk-download/bulk-download.png)

## <a name="check-download-status"></a>Проверка состояния загрузки

Вы можете увидеть состояние всех ожидающих натковых запросов на странице **результатов операции Bulk.**

[![](media/groups-bulk-download/bulk-center.png "Check status in the Bulk Operations Results page")](media/groups-bulk-download/bulk-center.png#lightbox)

## <a name="bulk-download-service-limits"></a>Ограничения обслуживания массовых скачиваний

Каждое массовое действие для загрузки списка групп может длиться до одного часа. Это позволяет загрузить список не менее 300 000 групп.

## <a name="next-steps"></a>Следующие шаги

- [Массовое удаление членов группы](groups-bulk-remove-members.md)
- [Скачать членов группы](groups-bulk-download-members.md)
