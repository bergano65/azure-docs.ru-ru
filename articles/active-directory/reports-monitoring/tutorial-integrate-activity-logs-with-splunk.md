---
title: Stream журналов Azure Active Directory в Splunk, с помощью Azure Monitor | Документация Майкрософт
description: Узнайте, как интегрировать Azure Active Directory журналы с Splunk с помощью Azure Monitor
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: c4b605b6-6fc0-40dc-bd49-101d03f34665
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
ms.openlocfilehash: 70befad3208f34fe62fbb0a59cea4bf6ea01ce16
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/22/2019
ms.locfileid: "59999524"
---
# <a name="integrate-azure-ad-logs-with-splunk-using-azure-monitor"></a>Интеграция журналов Azure AD с Splunk, с помощью Azure Monitor

В этой статье вы узнаете, как интегрировать журналы Azure Active Directory (Azure AD) со Splunk с помощью Azure Monitor. Сначала вы направите журналы в концентратор событий Azure, а затем интегрируете этот концентратор событий со Splunk.

## <a name="prerequisites"></a>Технические условия

Для использования этой функции необходимо иметь следующее.
* Концентратор событий Azure, содержащий журналы действий Azure AD. Узнайте, как [настроить потоковую передачу журналов действий в концентратор событий](quickstart-azure-monitor-stream-logs-to-event-hub.md). 
* Надстройка Azure Monitor для Splunk. [Скачайте и настройте свой экземпляр Splunk](https://github.com/Microsoft/AzureMonitorAddonForSplunk/blob/master/README.md).

## <a name="tutorial"></a>Учебник 

1. Откройте свой экземпляр Splunk и выберите **Data Summary** (Сводка данных).

    ![Кнопка "Data Summary" (Сводка данных)](./media/tutorial-integrate-activity-logs-with-splunk/DataSummary.png)

2. Выберите вкладку **Sourcetypes** (Типы источников), а затем — **amal: aadal:audit**.

    ![Вкладка "Sourcetypes" (Типы источников) в диалоговом окне сводки данных](./media/tutorial-integrate-activity-logs-with-splunk/sourcetypeaadal.png)

    Отобразятся журналы действий Azure AD, как показано на рисунке ниже.

    ![Журналы действий](./media/tutorial-integrate-activity-logs-with-splunk/activitylogs.png)

> [!NOTE]
> Если вам не удается установить надстройку в экземпляре Splunk (например, если вы используете прокси-сервер или работаете со Splunk Cloud), можно передать эти события в сборщик событий HTTP Splunk с помощью этой [функции Azure](https://github.com/Microsoft/AzureFunctionforSplunkVS), активируемой при поступлении новых сообщений в концентратор событий. 
>

## <a name="next-steps"></a>Дальнейшие действия

* [Interpret the Azure AD audit logs schema in Azure Monitor (preview)](reference-azure-monitor-audit-log-schema.md) (Интерпретация схемы журналов аудита Azure Active Directory в Azure Monitor (предварительная версия))
* [Interpret the Azure AD sign-in logs schema in Azure Monitor (preview)](reference-azure-monitor-sign-ins-log-schema.md) (Интерпретация схемы журналов входа Azure Active Directory в Azure Monitor (предварительная версия))
* [Часто задаваемые вопросы и известные проблемы](concept-activity-logs-azure-monitor.md#frequently-asked-questions)