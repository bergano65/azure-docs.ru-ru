---
title: Массовый список групп скачать - Портал Активный каталог Azure (ru) Документы Майкрософт
description: Добавляйте пользователей оптом в центр админ-центра Azure.
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
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81533698"
---
# <a name="bulk-download-members-of-a-group-in-azure-active-directory"></a>Массовые загрузки членов группы в Active Directory Azure

Используя портал Azure Active Directory (Azure AD), можно сбольшой суммой загрузить членов группы в организации в файл, разделенный на запятую (CSV).

## <a name="to-bulk-download-group-membership"></a>Для массового загрузки членства группы

1. Вопием на [портал Azure](https://portal.azure.com) с учетной записью администратора пользователя в организации. Владельцы групп также могут скачивать членов групп, которые им принадлежат.
1. В Azure AD выберите **группы** > **все группы.**
1. Откройте группу, членство которой вы хотите скачать, а затем выберите **участников.**
1. На странице **Участников** выберите **Скачать членов** скачать файл CSV с указанием членов группы.

   ![Команда Скачать Члены на странице профиля для группы](./media/groups-bulk-download-members/download-panel.png)

## <a name="check-download-status"></a>Проверка состояния загрузки

Вы можете увидеть состояние всех ожидающих натковых запросов на странице **результатов операции Bulk.**

[![](media/groups-bulk-download-members/bulk-center.png "Check status in the Bulk Operations Results page")](media/groups-bulk-download-members/bulk-center.png#lightbox)

## <a name="bulk-download-service-limits"></a>Ограничения обслуживания массовых скачиваний

Каждое массовое мероприятие для загрузки списка членов группы может работать до одного часа. Это позволяет загрузить список не менее 500 000 участников.

## <a name="next-steps"></a>Следующие шаги

- [Члены группы массовых импорта](groups-bulk-import-members.md)
- [Массовое удаление членов группы](groups-bulk-download-members.md)
