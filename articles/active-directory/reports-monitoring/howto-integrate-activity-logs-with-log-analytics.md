---
title: Как выполнять интеграцию журналов Azure Active Directory c Log Analytics с помощью Azure Monitor (предварительная версия) | Документация Майкрософт
description: Узнайте, как выполнять интеграцию журналов Azure Active Directory c Log Analytics с помощью Azure Monitor (предварительная версия)
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 2c3db9a8-50fa-475a-97d8-f31082af6593
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: report-monitor
ms.date: 10/01/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: bc918d0ee3b3b435905546507287dc655f68e8c9
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/19/2018
ms.locfileid: "49429785"
---
# <a name="integrate-azure-ad-logs-with-log-analytics-using-azure-monitor-preview"></a>Интеграция журналов Azure AD с Log Analytics с помощью Azure Monitor (предварительная версия)

Log Analytics позволяет выполнить запрос данных для поиска определенных событий, анализа тенденций и корреляции между различными источниками данных. С помощью журнала действий Azure AD в Log Analytics можно выполнять следующие задачи:

 * сравнивать журналы входа в Azure AD для журналов безопасности, опубликованных с помощью центра безопасности Azure;

 * устранять узкие места производительности на странице входа в приложение, сопоставляя данные о производительности приложений из Azure Application Insights.  

Следующий видеоролик сеансов Ignite показывает преимущества использования Log Analytics для журналов Azure AD в практических сценариях пользователя.

> [!VIDEO https://www.youtube.com/embed/MP5IaCTwkQg?start=1894]

В этой статье вы узнаете, как выполнять интеграцию журналов Azure Active Directory (Azure AD) с Log Analytics с помощью Azure Monitor.

## <a name="supported-reports"></a>Поддерживаемые отчеты

Для дальнейшего анализа можно маршрутизировать журналы аудита и активности входа в Log Analytics. 

* **Журналы аудита**. [Отчет о действиях журналов аудита](concept-audit-logs.md) предоставляет доступ к истории каждой задачи, выполняемой в клиенте.
* **Журналы входов в систему**. [Отчет о действиях входов в систему](concept-sign-ins.md) позволяет определить, кто выполнил задачи, указанные в отчете журналов аудита.

> [!NOTE]
> В настоящее время журналы действий аудита и входов в систему в клиенте B2C не поддерживаются.
>

## <a name="prerequisites"></a>Предварительные требования 

Для использования этой функции необходимо иметь следующее.

* Подписка Azure. Если у вас нет подписки Azure, можно [зарегистрироваться и получить бесплатную пробную версию](https://azure.microsoft.com/free/).
* Клиент Azure AD.
* Пользователь, который является *глобальным администратором* или *администратором безопасности* для этого клиента Azure AD.
* Рабочая область Log Analytics в подписке Azure. Узнайте, как [создать рабочую область Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-create-workspace).

## <a name="send-logs-to-log-analytics"></a>Отправка журналов в Log Analytics

1. Войдите на [портале Azure](https://portal.azure.com). 

2. Выберите **Azure Active Directory** > **Параметры диагностики** -> **Добавить параметры диагностики**. Чтобы получить доступ к странице конфигурации параметров диагностики, можно выбрать **Параметры экспорта** со страницы **Журналы аудита** или **Вход в систему**.  
    
3. В меню **Параметры диагностики** установите флажок **Отправить в Log Analytics**, а затем выберите **Настройка**.

4. Выберите рабочую область Log Analytics, в которую хотите отправить журналы, или создайте новую рабочую область в предоставленном диалоговом окне.  

5. Выполните одно из приведенных ниже действий или оба:
    * Установите флажок **AuditLogs**, чтобы отправить журналы аудита в рабочую область Log Analytics. 
    * Чтобы отправить журналы входа в рабочую область Log Analytics, установите флажок **SignInLogs**.

6. Нажмите кнопку **Сохранить**, чтобы сохранить параметры.

    ![Параметры диагностики](./media/howto-integrate-activity-logs-with-log-analytics/Configure.png)

7. Примерно через 15 минут убедитесь, что события отправлены в рабочую область Log Analytics.

## <a name="next-steps"></a>Дальнейшие действия

* [Анализ журналов действий Azure AD в Log Analytics](howto-analyze-activity-logs-log-analytics.md)
* [Установка и использование представлений Log Analytics для Azure Active Directory](howto-install-use-log-analytics-views.md)
