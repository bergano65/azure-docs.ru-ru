---
title: Производительности и нагрузочного тестирования с помощью Azure Application Insights | Документация Майкрософт
description: Настройка производительности и нагрузочных тестов с помощью Azure Application Insights
services: application-insights
author: mrbullwinkle
manager: carmonm
ms.assetid: 46dc13b4-eb2e-4142-a21c-94a156f760ee
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/19/2019
ms.reviewer: sdash
ms.author: mbullwin
ms.openlocfilehash: 55d743e32f6db0828317d3764a97bcb35b104dad
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/20/2019
ms.locfileid: "67304888"
---
# <a name="performance-testing"></a>Тестирование производительности

> [!NOTE]
> Облачные службы нагрузочного тестирования является устаревшим. Дополнительные сведения об устаревании, доступность службы и альтернативных служб можно найти [здесь](https://docs.microsoft.com/azure/devops/test/load-test/overview?view=azure-devops).

Application Insights позволяет создавать нагрузочные тесты для веб-сайтов. Как и [тесты доступности](monitor-web-app-availability.md), можно отправить либо базовые запросы или [многошаговые запросы](availability-multistep.md) из Azure агентов по всему миру тестирования. Тесты производительности дает вам возможность имитировать до 20 000 одновременных пользователей до 60 минут.

## <a name="create-an-application-insights-resource"></a>Создание ресурса Application Insights

Чтобы создать тест производительности, сначала необходимо создать ресурс Application Insights. Если вы уже создали ресурс перейдите к следующему разделу.

На портале Azure выберите **создать ресурс** > **средств разработчика** > **Application Insights** и создайте Application Insights. ресурс.

## <a name="configure-performance-testing"></a>Настроить тестирование производительности

Если это первое Создание выберите тест производительности **задать организации** и выбрать организацию DevOps в Azure в качестве источника для тестов производительности.

В разделе **Настройка**, перейдите в меню **тестирования производительности** и нажмите кнопку **New** для создания теста.

![Укажите хотя бы URL-адрес своего веб-сайта](./media/performance-testing/new-performance-test.png)

Для создания базовых показателей производительности теста, выберите тип теста **ручного теста** и заполните нужные параметры для теста.

|Параметр| Макс.
|----------|------------|
| Пользовательская нагрузка | 20 000 |
| Длительность (в минутах)  | 60 |  

После создания теста щелкните **выполнение теста**.

После завершения теста вы увидите результаты, подобные результаты ниже:

![Результаты теста](./media/performance-testing/test-results.png)

## <a name="configure-visual-studio-web-test"></a>Настройка веб-теста Visual Studio

Application Insights, возможности тестирования расширенные тесты производительности создаются на основе Visual Studio производительности и нагрузочных тестовых проектов.

![Visual Studio ](./media/performance-testing/visual-studio-test.png)

## <a name="next-steps"></a>Дальнейшие действия

* [Многошаговые веб-тесты](availability-multistep.md)
* [URL-адрес проверок связи](monitor-web-app-availability.md)