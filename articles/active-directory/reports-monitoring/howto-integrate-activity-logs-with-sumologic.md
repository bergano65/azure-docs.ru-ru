---
title: Потоковая передача журналов в SumoLogic с помощью Azure Monitor | Документация Майкрософт
description: Узнайте, как интегрировать журналы Azure Active Directory с SumoLogic с помощью Azure Monitor
services: active-directory
documentationcenter: ''
author: cawrites
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
ms.author: chadam
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 70c3b72ab650eb506dfaac378e10a7170b8f041d
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72819755"
---
# <a name="integrate-azure-active-directory-logs-with-sumologic-using-azure-monitor"></a>Интеграция журналов Azure Active Directory с SumoLogic с помощью Azure Monitor

Из этой статьи вы узнаете, как интегрировать журналы Azure Active Directory (Azure AD) с SumoLogic с помощью Azure Monitor. Сначала вы направите журналы в концентратор событий Azure, а затем интегрируете этот концентратор событий со SumoLogic.

## <a name="prerequisites"></a>Технические условия

Для использования этой функции необходимо иметь следующее.
* Концентратор событий Azure, содержащий журналы действий Azure AD. Узнайте, как [настроить потоковую передачу журналов действий в концентратор событий](quickstart-azure-monitor-stream-logs-to-event-hub.md). 
* Подписка с поддержкой единого входа SumoLogic.

## <a name="steps-to-integrate-azure-ad-logs-with-sumologic"></a>Шаги по интеграции журналов Azure AD с SumoLogic 

1. Сначала [выполните потоковую передачу журналов Azure AD в концентратор событий Azure](quickstart-azure-monitor-stream-logs-to-event-hub.md).
2. Настройте имеющийся экземпляр SumoLogic, чтобы [собирать журналы для Azure Active Directory](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure_Active_Directory/Collect_Logs_for_Azure_Active_Directory).
3. [Установите приложение Azure AD SumoLogic](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure_Active_Directory/Install_the_Azure_Active_Directory_App_and_View_the_Dashboards), чтобы использовать предварительно настроенные панели мониторинга, которые предоставляют анализ среды в реальном времени.

   ![панель мониторинга](./media/howto-integrate-activity-logs-with-sumologic/overview-dashboard.png)

## <a name="next-steps"></a>Дальнейшие действия

* [Interpret the Azure AD audit logs schema in Azure Monitor (preview)](reference-azure-monitor-audit-log-schema.md) (Интерпретация схемы журналов аудита Azure Active Directory в Azure Monitor (предварительная версия))
* [Interpret the Azure AD sign-in logs schema in Azure Monitor (preview)](reference-azure-monitor-sign-ins-log-schema.md) (Интерпретация схемы журналов входа Azure Active Directory в Azure Monitor (предварительная версия))
* [Часто задаваемые вопросы и известные проблемы](concept-activity-logs-azure-monitor.md#frequently-asked-questions)