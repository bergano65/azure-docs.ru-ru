---
title: Производительность и нагрузочное тестирование с помощью Azure Application Insights | Документация Майкрософт
description: Настройка производительности и нагрузочных тестов с помощью Azure Application Insights
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 06/19/2019
ms.reviewer: sdash
ms.openlocfilehash: 9c86b69239bed1a15c754ce28232b97e8439942b
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72819107"
---
# <a name="performance-testing"></a>Тестирование производительности

> [!NOTE]
> Облачная служба нагрузочного тестирования устарела. Дополнительные сведения об устаревании, доступности службы и альтернативных службах можно найти [здесь](https://docs.microsoft.com/azure/devops/test/load-test/overview?view=azure-devops).

Application Insights позволяет создавать нагрузочные тесты для веб-сайтов. Как и [тесты доступности](monitor-web-app-availability.md), вы можете отправить как базовые запросы, так и [многоэтапные запросы](availability-multistep.md) от агентов тестирования Azure по всему миру. Тесты производительности позволяют имитировать до 20 000 одновременных пользователей в течение 60 минут.

## <a name="create-an-application-insights-resource"></a>Создание ресурса Application Insights

Чтобы создать тест производительности, сначала необходимо создать ресурс Application Insights. Если вы уже создали ресурс, перейдите к следующему разделу.

На портал Azure выберите **создать ресурс** > **средства для разработчиков** > **Application Insights** и создайте ресурс Application Insights.

## <a name="configure-performance-testing"></a>Настройка тестирования производительности

Если вы впервые создаете тест производительности, выберите **задать организацию** и выберите организацию Azure DevOps в качестве источника для тестов производительности.

В разделе **Настройка**перейдите к разделу **Тестирование производительности** и нажмите кнопку **создать** , чтобы создать тест.

![Укажите хотя бы URL-адрес своего веб-сайта](./media/performance-testing/new-performance-test.png)

Чтобы создать базовый тест производительности, выберите тип теста **ручной тест** и Заполните нужные параметры теста.

|Параметр| Максимальное значение
|----------|------------|
| Пользовательская нагрузка | 20 000 |
| Длительность (в минутах)  | 60 |  

После создания теста щелкните **запустить тест**.

После завершения теста отобразятся результаты, аналогичные приведенным ниже.

![результаты теста](./media/performance-testing/test-results.png)

## <a name="configure-visual-studio-web-test"></a>Настройка веб-теста Visual Studio

Application Insights расширенные возможности тестирования производительности построены на основе проектов производительности и нагрузочных тестов Visual Studio.

![Visual Studio ](./media/performance-testing/visual-studio-test.png)

## <a name="next-steps"></a>Дальнейшие действия

* [Многошаговые веб-тесты](availability-multistep.md)
* [Проверки связи URL-адреса](monitor-web-app-availability.md)