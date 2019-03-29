---
title: С помощью мониторинга ресурсов можно выполнить проверку доступа в PIM — Azure Active Directory | Документация Майкрософт
description: Сведения о том, как выполнить проверку доступа в Azure Active Directory Privileged Identity Management (PIM) с помощью панели мониторинга ресурсов.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 03/30/2018
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 37057e531ea8387fbed84c9b03bbfb9c14d160ea
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2019
ms.locfileid: "58574966"
---
# <a name="use-a-resource-dashboard-to-perform-an-access-review-in-pim"></a>С помощью мониторинга ресурсов можно выполнить проверку доступа в PIM

На панели мониторинга ресурсов можно использовать для выполнения проверки доступа в Azure Active Directory (Azure AD) Privileged Identity Management (PIM). Панель мониторинга представления администратора содержит четыре основных компонента.

- Графическое представление активаций ролей ресурсов.
- Две диаграммы, на которых показано распределение назначений ролей по типу назначения.
- Область данных, относящихся к новым назначениям ролей.

![Снимок экрана панели мониторинга представления администратора с графиками и диаграммами](media/azure-pim-resource-rbac/rbac-overview-top.png)

![Снимок экрана панели мониторинга представления администратора со списками данных](media/azure-pim-resource-rbac/role-settings.png)

Графическое представление активаций ролей ресурсов за последние семь дней. Эти данные привязаны к выбранному ресурсу и отображают активацию для наиболее распространенных ролей (владелец, участник, администратор доступа пользователей) и всех объединенных ролей.

Справа от графика активации представлены две диаграммы, отображающие распределение назначений ролей по типу назначения для пользователей и групп. При выборе фрагмента диаграммы изменяется значение в процентах (или наоборот).

Под диаграммами отображается количество пользователей и групп с новыми назначениями ролей за последние 30 дней и список ролей, отсортированных по общему числу назначений (по убыванию).

## <a name="next-steps"></a>Дальнейшие действия

- [Запуск проверки доступа для ролей ресурсов Azure в PIM](pim-resource-roles-start-access-review.md) 
