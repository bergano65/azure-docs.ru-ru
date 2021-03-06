---
title: Модель данных телеметрии Azure Application Insights — контекст телеметрии | Документы Майкрософт
description: Модель данных контекста телеметрии Application Insights
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 05/15/2017
ms.reviewer: sergkanz
ms.openlocfilehash: 0351f4fe65745242da58d3c3fb2f9fbe5c722d06
ms.sourcegitcommit: 1bd2207c69a0c45076848a094292735faa012d22
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/21/2019
ms.locfileid: "72677456"
---
# <a name="telemetry-context-application-insights-data-model"></a>Контекст телеметрии: модель данных Application Insights

Каждый элемент данных телеметрии может иметь строго типизированные поля контекста. Каждое поле обеспечивает определенный сценарий мониторинга. Используйте пользовательскую коллекцию свойств для хранения пользовательских или связанных с приложением контекстных данных.


## <a name="application-version"></a>Версия приложения

Сведения в полях контекста приложения всегда связаны с приложением, отправляющим данные телеметрии. Версия приложения используется для анализа изменений тенденций в работе приложения и его сопоставления с развертываниями.

Максимальная длина: 1024


## <a name="client-ip-address"></a>IP-адрес клиента

IP-адрес клиентского устройства. Поддерживаются протоколы IPv4 и IPv6. Когда данные телеметрии отправляются из службы, контекст расположения указывает на пользователя, который инициировал операцию в службе. Application Insights извлекает сведения о географическом положении из IP-адреса клиента, а затем усекает его. Поэтому IP-адрес клиента сам по себе нельзя использовать как идентификационные данные конечного пользователя. 

Максимальная длина: 46


## <a name="device-type"></a>Тип устройства

Изначально это поле использовалось для указания типа устройства, применяемого конечным пользователем приложения. В настоящее время используется в первую очередь для различения данных телеметрии JavaScript с типом устройства "Браузер" от данных телеметрии на стороне сервера с типом устройства "ПК".

Максимальная длина: 64


## <a name="operation-id"></a>Идентификатор операции

Уникальный идентификатор корневой операции. Этот идентификатор позволяет группировать данные телеметрии по нескольким компонентам. Подробные сведения см. в статье [Корреляция данных телеметрии](../../azure-monitor/app/correlation.md). Идентификатор операции создается в результате запроса или просмотра страницы. Все другие данные телеметрии присваивают этому полю значение содержащего запроса или просмотра страницы. 

Максимальная длина: 128


## <a name="parent-operation-id"></a>Идентификатор родительской операции

Уникальный идентификатор непосредственного родительского объекта элемента данных телеметрии. Подробные сведения см. в статье [Корреляция данных телеметрии](../../azure-monitor/app/correlation.md).

Максимальная длина: 128


## <a name="operation-name"></a>Имя операции

Имя (группа) операции Имя операции создается в результате запроса или просмотра страницы. Все другие элементы данных телеметрии присваивают этому полю значение содержащего запроса или просмотра страницы. Имя операции используется для нахождения всех элементов данных телеметрии для группы операций (например, "GET Home/Index"). Это свойство контекста служит для ответа на вопросы типа "какие исключения, как правило, создаются на этой странице".

Максимальная длина: 1024


## <a name="synthetic-source-of-the-operation"></a>Искусственный источник операции

Имя искусственного источника. Некоторые данные телеметрии из приложения могут представлять искусственный трафик. Он может возникать в результате индексации веб-сайта поисковым модулем, тестов доступности сайта или трассировок из библиотек диагностики, например самого пакета SDK для Application Insights.

Максимальная длина: 1024


## <a name="session-id"></a>Идентификатор сеанса

Идентификатор сеанса — экземпляр взаимодействия пользователя с приложением. Сведения в полях контекста сеанса всегда связаны с конечным пользователем. Когда данные телеметрии отправляются из службы, контекст сеанса указывает на пользователя, который инициировал операцию в службе.

Максимальная длина: 64


## <a name="anonymous-user-id"></a>Идентификатор анонимного пользователя

Идентификатор анонимного пользователя. Представляет конечного пользователя приложения. Когда данные телеметрии отправляются из службы, контекст пользователя указывает на пользователя, который инициировал операцию в службе.

[Выборка](../../azure-monitor/app/sampling.md) — один из приемов, позволяющих свести к минимуму объем собираемых данных телеметрии. Алгоритм выборки пытается произвести внутреннюю или внешнюю выборку всех сопоставленных данных телеметрии. Идентификатор анонимного пользователя используется для создания оценки выборки. Поэтому он должен представлять собой достаточно случайное значение. 

Использовать поле идентификатора анонимного пользователя для хранения имени пользователя неправильно. Используйте для этого идентификатор прошедшего проверку подлинности пользователя.

Максимальная длина: 128


## <a name="authenticated-user-id"></a>Идентификатор прошедшего проверку подлинности пользователя

Идентификатор пользователя, прошедшего проверку подлинности. Противоположность идентификатору анонимного пользователя, это поле представляет пользователя с понятным именем. Так как это персональные данные, они не собираются по умолчанию большинством пакетов SDK.

Максимальная длина: 1024


## <a name="account-id"></a>Идентификатор учетной записи

В мультитенантных приложениях это идентификатор или имя учетной записи, в которой работает пользователь. Примерами могут служить идентификатор подписки на портал Azure или имя блога на платформе для ведения блогов.

Максимальная длина: 1024


## <a name="cloud-role"></a>Облачная роль

Имя роли, к которой относится приложение. Сопоставляется непосредственно с именем роли в Azure. Также может использоваться для различения микрослужб, которые входят в состав одного приложения.

Максимальная длина: 256


## <a name="cloud-role-instance"></a>Экземпляр облачной роли

Имя экземпляра, в котором работает приложение. Имя компьютера в локальной среде; имя экземпляра для Azure.

Максимальная длина: 256


## <a name="internal-sdk-version"></a>Внутреннее: версия SDK

Версия пакета SDK. Дополнительные сведения см. на странице по адресу https://github.com/Microsoft/ApplicationInsights-Home/blob/master/SDK-AUTHORING.md#sdk-version-specification.

Максимальная длина: 64


## <a name="internal-node-name"></a>Внутреннее: имя узла

Это поле представляет имя узла, используемое для выставления счетов. Используйте его для переопределения стандартного механизма обнаружения узлов.

Максимальная длина: 256


## <a name="next-steps"></a>Дальнейшие действия

- Узнайте, как [расширять и фильтровать данные телеметрии](../../azure-monitor/app/api-filtering-sampling.md).
- В [этой статье](data-model.md) представлены типы данных и модель данных для Application Insights.
- Извлеките стандартную [конфигурацию](../../azure-monitor/app/configuration-with-applicationinsights-config.md#telemetry-initializers-aspnet) коллекции свойств контекста.
