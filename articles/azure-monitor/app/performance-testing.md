---
title: Тестирование производительности и загрузки с помощью Azure Application Insights Документы Майкрософт
description: Настройка тестов производительности и загрузки с помощью Azure Application Insights
ms.topic: conceptual
ms.date: 06/19/2019
ms.reviewer: sdash
ms.openlocfilehash: 0bfb60b876add70be6cbcd3d726fb3317a8dfcf9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77669868"
---
# <a name="performance-testing"></a>Тестирование производительности

> [!NOTE]
> Облачный сервис тестирования нагрузки был утих. Более подробную информацию об амортизацию, доступность услуг и альтернативных услуг можно найти [здесь](https://docs.microsoft.com/azure/devops/test/load-test/overview?view=azure-devops).

Application Insights позволяет создавать тесты нагрузки для ваших веб-сайтов. [Например, тесты доступности,](monitor-web-app-availability.md)вы можете отправлять либо основные [запросы, либо многоступенчатые запросы](availability-multistep.md) от тестовых агентов Azure по всему миру. Тесты производительности позволяют имитировать до 20 000 одновременных пользователей на срок до 60 минут.

## <a name="create-an-application-insights-resource"></a>Создание ресурса Application Insights

Для создания теста производительности сначала необходимо создать ресурс Application Insights. Если вы уже создали ресурс, перейдите к следующему разделу.

На портале Azure выберите **«Создайте анализ приложений** > **для разработчиков инструментов»** > **Application Insights** и создайте ресурс Application Insights.

## <a name="configure-performance-testing"></a>Настройка тестирования производительности

Если это первый раз, когда вы создаете тест производительности, выберите **set Organization** и выберите организацию Azure DevOps в цель проверки производительности.

Под **настраивать,** перейдите на **тестирование производительности** и нажмите **Новый** для создания теста.

![Укажите хотя бы URL-адрес своего веб-сайта](./media/performance-testing/new-performance-test.png)

Чтобы создать базовый тест производительности, выберите тип теста **ручного теста** и заполните желаемые настройки для теста.

|Параметр| Максимальное значение
|----------|------------|
| Загрузка пользователя | 20 000 |
| Продолжительность (минуты)  | 60 |  

После создания теста нажмите **на тест Run.**

Как только тест будет завершен, вы увидите результаты, которые выглядят похожими на результаты ниже:

![Результаты теста](./media/performance-testing/test-results.png)

## <a name="configure-visual-studio-web-test"></a>Настройка визуальной студии веб-тест

Расширенные возможности тестирования производительности Application Insights построены поверх проектов визуальной студии производительности и тестирования нагрузки.

![Visual Studio ](./media/performance-testing/visual-studio-test.png)

## <a name="next-steps"></a>Дальнейшие действия

* [Многошаговые веб-тесты](availability-multistep.md)
* [Тесты Наур пинг](monitor-web-app-availability.md)
