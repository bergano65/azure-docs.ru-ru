---
title: Использование панели мониторинга ресурсов для выполнения проверки доступа в управление привилегированными пользователями-Azure Active Directory | Документация Майкрософт
description: Сведения о том, как выполнить проверку доступа в Azure Active Directory Privileged Identity Management (PIM) с помощью панели мониторинга ресурсов.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 10/23/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: de2c04a0ea109046848d443db2e6ab9a9dd21ee0
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/24/2019
ms.locfileid: "72895575"
---
# <a name="use-a-resource-dashboard-to-perform-an-access-review-in-privileged-identity-management"></a>Используйте панель мониторинга ресурсов для выполнения проверки доступа в управление привилегированными пользователями

Панель мониторинга ресурсов можно использовать для выполнения проверки доступа в управление привилегированными пользователями (PIM). На панели мониторинга представления администрирования в Azure Active Directory (Azure AD) есть три основных компонента:

- Графическое представление активаций ролей ресурсов
- Диаграммы, отображающие распределение назначений ролей по типу назначения
- Область данных, содержащая сведения о новых назначениях ролей

![Снимок экрана панели мониторинга представления администратора с графиками и диаграммами](media/pim-resource-roles-overview-dashboards/rbac-overview-top.png)

![Снимок экрана панели мониторинга представления администратора со списками данных](media/pim-resource-roles-overview-dashboards/role-settings.png)

Графическое представление активаций ролей ресурсов за последние семь дней. Эти данные привязаны к выбранному ресурсу и отображают активацию для наиболее распространенных ролей (владелец, участник, администратор доступа пользователей) и всех объединенных ролей.

На одной стороне графа активации две диаграммы отображают распределение назначений ролей по типу назначения для пользователей и групп. При выборе фрагмента диаграммы изменяется значение в процентах (или наоборот).

Под диаграммами указаны число пользователей и групп с новыми назначениями ролей за последние 30 дней, а также роли, отсортированные по общему числу назначений в порядке убывания.

## <a name="next-steps"></a>Дальнейшие действия

- [Запуск проверки доступа для ролей ресурсов Azure в управление привилегированными пользователями](pim-resource-roles-start-access-review.md)
