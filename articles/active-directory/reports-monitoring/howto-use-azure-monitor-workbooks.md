---
title: Используйте Azure Monitor книг отчетов Azure Active Directory | Документация Майкрософт
description: Сведения об использовании книг Azure Monitor для отчетов Azure Active Directory
services: active-directory
author: MarkusVi
manager: daveba
ms.assetid: 4066725c-c430-42b8-a75b-fe2360699b82
ms.service: active-directory
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/18/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 2c9b3d0ef110fea0629af345a71d0d7b7cce7313
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60287325"
---
# <a name="how-to-use-azure-monitor-workbooks-for-azure-active-directory-reports"></a>Практическое руководство: Использование Azure Monitor книг для отчетов Azure Active Directory

Вы хотите:

- Понять влияние вашей [политики условного доступа](../conditional-access/overview.md) на ваши пользователи входят в систему?

- Устранение ошибок входа в систему, чтобы получить более полное представление о работоспособности входа вашей организации, а также для быстрого устранения проблем?

- Узнать, кто использует устаревший проверок подлинности должны входить в вашей среде? По [блокировки проверки подлинности](../conditional-access/block-legacy-authentication.md), можно улучшить защиту вашего клиента.


[Azure Monitor книг](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks) объединить текст, запросы аналитики, метрик Azure и параметры в подробные интерактивные отчеты. Azure Active Directory позволяет книг для мониторинга, которые помогут вам найти ответы на вопросы выше.

В этой статье:

- Предполагается, что вы знаете, как [Создавайте интерактивные отчеты с книгами Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks).

- Объясняется, как можно использовать книги о мониторинге Azure Monitor позволяет ответить на вопросы выше.
 


## <a name="prerequisites"></a>Технические условия

Для использования этой функции необходимо иметь следующее.

- Клиент Azure Active Directory с лицензией premium (P1 и P2). Узнайте, как [получить лицензию уровня "премиум"](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-get-started-premium).

- Объект [рабочей области Log Analytics](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace).

## <a name="access-workbooks"></a>Получить доступ к книгам 

Для доступа к книгам:

1. Войдите в ваш [портала Azure](https://portal.azure.com).

2. На панели навигации слева щелкните **Azure Active Directory**.

3. В **мониторинг** щелкните **Insights**. 

    ![Аналитика](./media/howto-use-azure-monitor-workbooks/41.png)

4. Щелкните отчет или шаблон, или **откройте** на панели инструментов. 

    ![Коллекция](./media/howto-use-azure-monitor-workbooks/42.png)


## <a name="sign-in-analysis"></a>Анализ входа в систему

Чтобы получить доступ к книге анализа входа в систему, нажмите кнопку **входы в систему** в **использования** раздел. 

Эта книга отображаются следующие тенденции входа в систему:

- Все входы в систему

- Успешно

- Ожидается действие пользователя

- Сбой

Вы можете отфильтровать каждого тенденция по:

- Диапазон времени

- Приложения

- Пользователи

![Коллекция](./media/howto-use-azure-monitor-workbooks/43.png)


Для каждого тенденции получить разбивку:

- Location

    ![Коллекция](./media/howto-use-azure-monitor-workbooks/45.png)

- Устройство

    ![Коллекция](./media/howto-use-azure-monitor-workbooks/46.png)


## <a name="sign-ins-using-legacy-authentication"></a>Входы в систему с помощью проверки подлинности 


Для доступа к входов в систему с использованием [проверки подлинности](../conditional-access/block-legacy-authentication.md) книги, нажмите кнопку **входы в систему с помощью проверки подлинности** в **использования** раздел. 

Эта книга отображаются следующие тенденции входа в систему:

- Все входы в систему

- Успешно


Вы можете отфильтровать каждого тенденция по:

- Диапазон времени

- Приложения

- Пользователи

- Протоколы 

![Коллекция](./media/howto-use-azure-monitor-workbooks/47.png)


Для каждого тенденции вы получаете декомпозиция приложения и протокола.

![Коллекция](./media/howto-use-azure-monitor-workbooks/48.png)



## <a name="sign-ins-by-conditional-access"></a>Входы в систему с помощью условного доступа 


Для доступа к входов в систему с [политики условного доступа](../conditional-access/overview.md) книги, нажмите кнопку **входы в систему с помощью условного доступа** в **условного доступа** раздел. 

Эта книга показывает тренд для отключенного входы в систему.

Вы можете отфильтровать каждого тенденция по:

- Диапазон времени

- Приложения

- Пользователи

![Коллекция](./media/howto-use-azure-monitor-workbooks/49.png)


Для отключенного входов в систему вы получаете декомпозиция по состоянию условного доступа.

![Состояние условного доступа](./media/howto-use-azure-monitor-workbooks/conditional-access-status.png)








## <a name="sign-ins-by-grant-controls"></a>Входы в систему, элементов управления предоставлением прав

Для доступа к входов в систему с [предоставить элементы управления](../conditional-access/controls.md) книги, нажмите кнопку **входы в систему, элементов управления предоставлением прав** в **условного доступа** раздел. 

Эта книга отображаются следующие отключенные вход тенденции:

- Требовать многофакторную идентификацию
 
- Требовать условия использования

- Требовать заявление о конфиденциальности

- Другие


Вы можете отфильтровать каждого тенденция по:

- Диапазон времени

- Приложения

- Пользователи

![Коллекция](./media/howto-use-azure-monitor-workbooks/50.png)


Для каждого тенденции вы получаете декомпозиция приложения и протокола.

![Коллекция](./media/howto-use-azure-monitor-workbooks/51.png)




## <a name="sign-ins-failure-analysis"></a>Анализ сбоя входы в систему

Используйте **Анализ сбоя входы в систему** книги, чтобы устранить неполадки:

- Вход в систему
- Политики условного доступа
- Устаревшая проверка подлинности. 


Для доступа к входов в систему, условного доступа к данным, щелкните **входы в систему с использованием проверки подлинности для прежних версий** в **Устранение** раздел. 

Эта книга отображаются следующие тенденции входа в систему:

- Все входы в систему

- Успешно

- Незавершенное действие

- Сбой


Вы можете отфильтровать каждого тенденция по:

- Диапазон времени

- Приложения

- Пользователи

![Коллекция](./media/howto-use-azure-monitor-workbooks/52.png)


Для устранения неполадок входы в систему, вы получите распределение:

- Самые распространенные ошибки

    ![Коллекция](./media/howto-use-azure-monitor-workbooks/53.png)

- Входы в систему ожидает действий пользователя

    ![Коллекция](./media/howto-use-azure-monitor-workbooks/54.png)






## <a name="next-steps"></a>Дальнейшие действия

* [Создавайте интерактивные отчеты с книгами Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks)