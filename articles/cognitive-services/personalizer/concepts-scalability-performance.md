---
title: Масштабируемость и производительность — Персонализация
titleSuffix: Azure Cognitive Services
description: 'Для высокопроизводительных веб-сайтов и приложений с большой пропускной способностью два стоит учесть два основных фактора, касающихся Персонализатора и обеспечения масштабируемости и производительности: задержка и пропускная способность обучения.'
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 06/07/2019
ms.author: diberry
ms.openlocfilehash: d116f6bd389b1404ea723c965111cd05880e6c30
ms.sourcegitcommit: e3b0fb00b27e6d2696acf0b73c6ba05b74efcd85
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/30/2019
ms.locfileid: "68662823"
---
# <a name="scalability-and-performance"></a>Масштабируемость и производительность

Для высокопроизводительных веб-сайтов и приложений с большой пропускной способностью два стоит учесть два основных фактора, касающихся Персонализатора и обеспечения масштабируемости и производительности:

* Сохранение низкой задержки при выполнении вызовов API ранжирования.
* Уверенность, что пропускная способность обучения адаптируется к входным данным событий.

Персонализация может очень быстро возвращать оценку, при этом большая часть продолжительности вызова выделяется для обмена данными через REST API. Azure выполнит автомасштабирование способности быстро отвечать на запросы.

##  <a name="low-latency-scenarios"></a>Сценарии с низкой задержкой

Некоторым приложениям требуется низкая задержка при возврате оценки. Это необходимо:

* Чтобы избавить пользователя от длительного ожидания перед отображением ранжированного содержимого.
* Чтобы помочь серверу с максимальным трафиком избежать прерываний дефицитного времени вычислений и сетевых подключений.

<!--

If your web site is scaled on your infrastructure, you can avoid making HTTP calls by hosting the Personalizer API in your own servers running a Docker container.

This change would be transparent to your application, other than using an endpoint URL referring to the running docker instances as opposed to an online service in the cloud.



### Extreme Low Latency Scenarios

If you require latencies under a millisecond, and have already tested using Personalizer via containers, please contact our support team so we can assess your scenario and provide guidance suited to your needs.

-->

## <a name="scalability-and-training-throughput"></a>Масштабируемость и пропускная способность обучения

Персонализатор работает, обновляя модель, переобученную на основе отправленных асинхронно сообщений с помощью Персонализатора, после API ранжирования и вознаграждения. Сообщений отправляются с помощью концентратора событий Azure для приложения.

 Маловероятно, что большинство приложений достигнут максимального лимита пропускной способности для присоединений и обучения Персонализатора. Достижение этого максимума не замедляет работу приложения, это означает, что очереди концентратора событий заполняются внутри быстрее, чем очищаются.

## <a name="how-to-estimate-your-throughput-requirements"></a>Как оценить требования к пропускной способности

* Оцените среднее число байт на ранжированное событие, добавляя длины документов JSON контекста и действия.
* Разделите 20 МБ в секунду на это расчетное среднее число байт.

Например, если средние полезные данные содержат 500 компонентов, причем каждый состоит из 20 знаков, то каждое событие — это приблизительно 10 КБ. При таких оценках получается 20 000 000/10 000 = 2000 событий в секунду, а это около 173 миллионов событий в день. 

Если вы достигните этих ограничений, обратитесь в службу поддержки для получения совета по архитектуре.

## <a name="next-steps"></a>Следующие шаги

[Personalizer settings](how-to-settings.md) (Параметры Персонализатора)