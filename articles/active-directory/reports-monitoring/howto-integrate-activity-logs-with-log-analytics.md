---
title: Потоковая передача журналов Azure Active Directory в журналы Azure Monitor (предварительная версия) | Документация Майкрософт
description: Узнайте, как интегрировать журналы Azure Active Directory с журналами Azure Monitor (предварительная версия)
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: ''
ms.assetid: 2c3db9a8-50fa-475a-97d8-f31082af6593
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7d8259cdb61d2481805dd2e07c11b539e057215c
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/26/2019
ms.locfileid: "56817071"
---
# <a name="integrate-azure-ad-logs-with-azure-monitor-logs-preview"></a>Интеграция журналов Azure AD с журналами Azure Monitor (предварительная версия)

Журналы Azure Monitor позволяют запрашивать данные для поиска определенных событий, анализировать тенденции и выполнять корреляцию между различными источниками данных. С помощью интеграции журналов действий Azure AD в журналах Azure Monitor вы можете выполнять следующие задачи:

 * сравнивать журналы входа в Azure AD для журналов безопасности, опубликованных с помощью центра безопасности Azure;

 * устранять узкие места производительности на странице входа в приложение, сопоставляя данные о производительности приложений из Azure Application Insights.  

Следующий видеоролик сеансов Ignite показывает преимущества использования журналов Azure Monitor для журналов Azure AD в практических сценариях пользователя.

> [!VIDEO https://www.youtube.com/embed/MP5IaCTwkQg?start=1894]

В этой статье вы узнаете, как интегрировать журналы Azure Active Directory (Azure AD) с Azure Monitor.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="supported-reports"></a>Поддерживаемые отчеты

Для дальнейшего анализа вы можете маршрутизировать журналы действий аудита и входа в журналы Azure Monitor. 

* **Журналы аудита**: [отчет о действиях журналов аудита](concept-audit-logs.md) предоставляет доступ к истории каждой задачи, выполняемой в клиенте.
* **Журналы входов в систему**: [отчет о действиях входов в систему](concept-sign-ins.md) позволяет определить, кто выполнил задачи, указанные в отчете журналов аудита.

> [!NOTE]
> В настоящее время журналы действий аудита и входов в систему в клиенте B2C не поддерживаются.
>

## <a name="prerequisites"></a>Технические условия 

Для использования этой функции необходимо иметь следующее.

* Подписка Azure. Если у вас нет подписки Azure, можно [зарегистрироваться и получить бесплатную пробную версию](https://azure.microsoft.com/free/).
* Клиент Azure AD.
* Пользователь, который является *глобальным администратором* или *администратором безопасности* для этого клиента Azure AD.
* Рабочая область Log Analytics в подписке Azure. Узнайте, как [создать рабочую область Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-create-workspace).

## <a name="send-logs-to-azure-monitor-logs"></a>Отправка журналов в журналы Azure Monitor

1. Войдите на [портале Azure](https://portal.azure.com). 

2. Выберите **Azure Active Directory** > **Параметры диагностики** -> **Добавить параметры диагностики**. Чтобы получить доступ к странице конфигурации параметров диагностики, можно выбрать **Параметры экспорта** со страницы **Журналы аудита** или **Вход в систему**.  
    
3. В меню **Параметры диагностики** установите флажок **Send to Log Analytics workspace**(Отправить в рабочую область Log Analytics), а затем выберите **Настроить**.

4. Выберите рабочую область Log Analytics, в которую хотите отправить журналы, или создайте новую рабочую область в предоставленном диалоговом окне.  

5. Выполните одно из приведенных ниже действий или оба:
    * Установите флажок **AuditLogs**, чтобы отправить журналы аудита в рабочую область Log Analytics. 
    * Чтобы отправить журналы входа в рабочую область Log Analytics, установите флажок **SignInLogs**.

6. Нажмите кнопку **Сохранить**, чтобы сохранить параметры.

    ![Параметры диагностики](./media/howto-integrate-activity-logs-with-log-analytics/Configure.png)

7. Примерно через 15 минут убедитесь, что события отправлены в рабочую область Log Analytics.

## <a name="next-steps"></a>Следующие шаги

* [Анализ журналов действий Azure AD с помощью журналов Azure Monitor (предварительная версия)](howto-analyze-activity-logs-log-analytics.md)
* [Установка и использование представлений Log Analytics для Azure Active Directory](howto-install-use-log-analytics-views.md)
