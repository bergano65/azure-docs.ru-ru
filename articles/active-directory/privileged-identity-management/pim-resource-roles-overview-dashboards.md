---
title: Панели мониторинга ресурсов для отзывов о доступе в PIM - Azure AD Документы Майкрософт
description: Сведения о том, как выполнить проверку доступа в Azure Active Directory Privileged Identity Management (PIM) с помощью панели мониторинга ресурсов.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6affa2ecc8919dabeb6173622b525280ce96bcfe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73847029"
---
# <a name="use-a-resource-dashboard-to-perform-an-access-review-in-privileged-identity-management"></a>Используйте панель мониторинга ресурсов для выполнения обзора доступа в Privileged Identity Management

Панель мониторинга ресурсов можно использовать для выполнения обзора доступа в Privileged Identity Management (PIM). Панель мониторинга Admin View в Active Directory Azure (Azure AD) имеет три основных компонента:

- Графическое представление активаций роли ресурса
- Диаграммы, отображающие распределение назначений ролей по типу назначения
- Область данных, содержащая информацию для новых назначений ролей

![Снимок экрана панели мониторинга представления администратора с графиками и диаграммами](media/pim-resource-roles-overview-dashboards/rbac-overview-top.png)

![Снимок экрана панели мониторинга представления администратора со списками данных](media/pim-resource-roles-overview-dashboards/role-settings.png)

Графическое представление активаций ролей ресурсов за последние семь дней. Эти данные привязаны к выбранному ресурсу и отображают активацию для наиболее распространенных ролей (владелец, участник, администратор доступа пользователей) и всех объединенных ролей.

С одной стороны графика активаций две диаграммы отображают распределение назначений ролей по типу назначения как для пользователей, так и для групп. При выборе фрагмента диаграммы изменяется значение в процентах (или наоборот).

Ниже диаграммы перечислены количество пользователей и групп с новыми назначениями ролей за последние 30 дней, и роли, отсортированные по общему количеству назначений в порядке убывания.

## <a name="next-steps"></a>Дальнейшие действия

- [Запуск проверки доступа для ролей ресурсов Azure в Привилегированном управлении identity](pim-resource-roles-start-access-review.md)
