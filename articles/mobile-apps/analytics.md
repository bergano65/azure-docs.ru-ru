---
title: Понимание использования мобильных приложений и поведения пользователей с помощью центра приложений Visual Studio и служб Azure
description: Узнайте о таких службах, как центр приложений, которые помогут принимать бизнес-решения, зная, как пользователи используют ваше мобильное приложение.
author: elamalani
ms.assetid: 34a8a070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: ff25df55e8489819ca9400205cdea1b8ceb8bf80
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75454537"
---
# <a name="analyze-and-understand-mobile-application-use"></a>Анализ и понимание использования мобильных приложений
Насколько хорошо вы понимаете, как пользователи используют ваши приложения? Сколько активных пользователей имеет ваше приложение и как с течением времени меняется использование? Какие функции они используют, и какие из них используются чаще всего? Где эти пользователи основаны? Сколько пользователей использует последнюю версию приложения? Все эти вопросы важны для того, чтобы превратить приложение в успешное предприятие. Чтобы ответить на эти вопросы по аналитике использования, необходимо получить данные об использовании из приложений.

Чем больше вы пройдете в данных, тем больше могут оказаться способы улучшить приложение и обеспечить их удовлетворительное качество. Важно использовать данные для поиска ценной информации и сохранения соответствия пользователей.

## <a name="importance-of-analytics"></a>Важность аналитики
- Вы узнаете о пользователях, о том, как они взаимодействуют с приложением, и что возвращает их для точной настройки приложения и предоставления превосходных возможностей для роста бизнеса.
- Следите за показателями использования, чтобы принимать взвешенные решения о том, как выдвигать приложение и лучше обслуживать клиентов.
- Оцените производительность приложения.
- Сведения о том, какие части значения и производительности на диске приложения.
- Получение ценных сведений о проблемах, связанных с обработкой и хранением данных.

Используйте следующие службы для включения аналитики мобильных приложений.

## <a name="visual-studio-app-center"></a>Центр приложений Visual Studio.
[Аналитика центра приложений](/appcenter/analytics/) позволяет расширить аудиторию, чтобы сосредоточиться на важных возможностях. Он обеспечивает глубокую отчетность и аналитические сведения о пользовательских сеансах, основных устройствах, версиях ОС и аналитике поведения. Легко создавайте пользовательские события для отслеживания любых сведений с помощью обширной аналитики приложений.

   **Основные возможности**
   - Бесплатно Следите за шаблонами использования, внедрением пользователей и другими показателями участия.
   - Выявление тенденций, поведения пользователей и обязательств с помощью пользовательских событий.
   - Получите готовые метрики и подробную информацию об использовании приложений (ежедневно, еженедельно, ежемесячно), сеансах, свойствах устройств и демографических данных пользователей на одной панели мониторинга.
   - Непрерывно Экспортируйте все данные аналитики центра приложений в Azure для неограниченного хранения. Аналитика центра приложений поддерживает экспорт в хранилище BLOB-объектов Azure и Azure Application Insights.
   - Интегрируйтесь с Azure Application Insights для получения более глубокого анализа, например для хранения, воронки и когорты.
   - Используйте однострочную интеграцию пакета SDK, чтобы начать работу за считаные минуты.
   - Получите поддержку платформы для iOS, Android, macOS, tvOS, Xamarin, реагирования на машинный код, Unity и Cordova.

   **Справочные материалы**
   - [Регистрация в центре приложений](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
   - [Приступая к работе с аналитикой центра приложений](/appcenter/analytics/)

## <a name="azure-monitor"></a>Azure Monitor
Azure Monitor включает [Application Insights](/azure/azure-monitor/app/app-insights-overview), которые предоставляют средства для сбора и анализа данных телеметрии для повышения производительности и мониторинга мобильного приложения. Вы можете воспользоваться преимуществами Application Insights, используя аналитику App Center, чтобы настроить экспорт в Application Insights. Application Insights могут запрашивать, сегментировать, фильтровать и анализировать данные телеметрии пользовательских событий из ваших приложений, помимо средств аналитики, предоставляемых Центром приложений.

**Основные возможности**
   - Запросите данные телеметрии пользовательских событий.
   - Отфильтруйте данные телеметрии событий с помощью мощных возможностей сегментирования.
   - Анализируйте шаблоны преобразования, хранения и навигации в приложении. Вы можете использовать:
     - Воронки для анализа и отслеживания скорости преобразования.
     - Хранение для анализа того, насколько хорошо приложение удерживает пользователей с течением времени.
     - Книги для объединения визуализаций и текста в общий отчет.
     - Когорты имена и сохраняйте определенные группы пользователей или событий, чтобы к ним можно было легко обращаться из других средств аналитики.

**Справочные материалы**
- [Портал Azure](https://portal.azure.com/)
- [Анализ мобильного приложения с помощью App Center и Application Insights](/azure/azure-monitor/learn/mobile-center-quickstart)
- [Использование аналитики центра приложений с Application Insights](/azure/azure-monitor/app/usage-overview)

## <a name="azure-playfab"></a>Azure PlayFab
[Azure PlayFab](https://playfab.com/) предлагает комплексную серверную платформу с игровыми службами, аналитикой в режиме реального времени и LiveOps, которые необходимы для создания игр, подключенных к облаку мирового класса. Эти службы снижают барьеры, которые могут быть запущены для разработчиков игр. Они предлагают как крупные, так и небольшие Studios решения разработки, масштабируемые с помощью игр. Эти службы помогают studiosть, хранить и монетизации игроков. С помощью PlayFab разработчики могут использовать интеллектуальное облако для создания и эксплуатации игр, анализа данных игр и улучшения общих компьютерных возможностей.

**Основные возможности**
   - Мониторинг панелей мониторинга в режиме реального времени.
   - Оцените производительность игры с помощью основных метрик.
   - Просмотрите сводку по ежедневной и месячной производительности игры с помощью автоматически сформированных отчетов. Отчеты можно просматривать в диспетчере игр и загружать или доставлять в папку "Входящие" ежедневно.
   - Используйте проверку A/B для выполнения экспериментов и определения оптимального параметра для конкретной переменной.
   - Использование сегментации для игроков, чтобы определить автоматизированные группы игроков.
    
**Справочные материалы**
- [Портал PlayFab](https://developer.playfab.com/en-US/sign-up)
- [Analytics](/gaming/playfab/#pivot=documentation&panel=analytics)
- [Краткие руководства](/gaming/playfab/#pivot=documentation&panel=quickstarts) 
