---
title: Массовый список групп скачать - Портал Активный каталог Azure (ru) Документы Майкрософт
description: Добавляйте пользователей оптом в центр админ-центра Azure.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "72517155"
---
# <a name="bulk-download-members-of-a-group-preview-in-azure-active-directory"></a>Массовые загрузки членов группы (предварительный просмотр) в Active Directory Azure

Используя портал Azure Active Directory (Azure AD), можно сбольшой суммой загрузить членов группы в организации в файл, разделенный на запятую (CSV).

## <a name="to-bulk-download-group-membership"></a>Для массового загрузки членства группы

1. Вопием на [портал Azure](https://portal.azure.com) с учетной записью администратора пользователя в организации. Владельцы групп также могут скачивать членов групп, которые им принадлежат.
1. В Azure AD выберите **группы** > **все группы.**
1. Откройте группу, членство которой вы хотите скачать, а затем выберите **участников.**
1. На странице **Участников** выберите **Скачать членов** скачать файл CSV с указанием членов группы.

   ![Команда Скачать Члены на странице профиля для группы](./media/groups-bulk-download-members/download-panel.png)

## <a name="check-download-status"></a>Проверка состояния загрузки

Вы можете увидеть состояние всех ожидающих натковых запросов на странице **результаты (предварительный просмотр) результатов операции Bulk.**

   ![Страница результатов результатов массовых операций показывает состояние запроса навалом](./media/groups-bulk-download-members/bulk-center.png)

## <a name="bulk-download-service-limits"></a>Ограничения обслуживания массовых скачиваний

Каждое массовое мероприятие для загрузки списка членов группы может работать до одного часа. Это позволяет загрузить список не менее 500 000 участников.

## <a name="next-steps"></a>Дальнейшие действия

- [Члены группы массовых импорта](groups-bulk-import-members.md)
- [Массовое удаление членов группы](groups-bulk-download-members.md)
