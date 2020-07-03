---
title: Потоковая передача журналов в SumoLogic с помощью Azure Monitor | Документация Майкрософт
description: Узнайте, как интегрировать журналы Azure Active Directory с SumoLogic с помощью Azure Monitor
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 2c3db9a8-50fa-475a-97d8-f31082af6593
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/18/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6cdfc4e393ca7bf4bcbd523b4fad72690d5f2744
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "74014386"
---
# <a name="integrate-azure-active-directory-logs-with-sumologic-using-azure-monitor"></a>Интеграция журналов Azure Active Directory с SumoLogic с помощью Azure Monitor

Из этой статьи вы узнаете, как интегрировать журналы Azure Active Directory (Azure AD) с SumoLogic с помощью Azure Monitor. Сначала вы направите журналы в концентратор событий Azure, а затем интегрируете этот концентратор событий со SumoLogic.

## <a name="prerequisites"></a>Предварительные требования

Для использования этой функции необходимо иметь следующее.
* Концентратор событий Azure, содержащий журналы действий Azure AD. Узнайте, как [настроить потоковую передачу журналов действий в концентратор событий](quickstart-azure-monitor-stream-logs-to-event-hub.md). 
* Подписка с поддержкой единого входа SumoLogic.

## <a name="steps-to-integrate-azure-ad-logs-with-sumologic"></a>Шаги по интеграции журналов Azure AD с SumoLogic 

1. Сначала [выполните потоковую передачу журналов Azure AD в концентратор событий Azure](quickstart-azure-monitor-stream-logs-to-event-hub.md).
2. Настройте имеющийся экземпляр SumoLogic, чтобы [собирать журналы для Azure Active Directory](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure_Active_Directory/Collect_Logs_for_Azure_Active_Directory).
3. [Установите приложение Azure AD SumoLogic](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure_Active_Directory/Install_the_Azure_Active_Directory_App_and_View_the_Dashboards), чтобы использовать предварительно настроенные панели мониторинга, которые предоставляют анализ среды в реальном времени.

   ![Панель мониторинга](./media/howto-integrate-activity-logs-with-sumologic/overview-dashboard.png)

## <a name="next-steps"></a>Дальнейшие действия

* [Анализ схемы журналов аудита в Azure Monitor](reference-azure-monitor-audit-log-schema.md)
* [Анализ схемы журналов входа в Azure Monitor](reference-azure-monitor-sign-ins-log-schema.md)
* [Часто задаваемые вопросы и известные проблемы](concept-activity-logs-azure-monitor.md#frequently-asked-questions)