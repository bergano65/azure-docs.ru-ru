---
title: Обзор устойчивых функций — Azure
description: Общие сведения о расширении устойчивых функций для Функций Azure.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: overview
ms.date: 12/22/2018
ms.author: glenga
ms.reviewer: azfuncdf
ms.openlocfilehash: 323ab530d8199dd154e5d3568c09f86f6f52d702
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70087197"
---
# <a name="what-are-durable-functions"></a>Что такое Устойчивые функции?

*Устойчивые функции* — это расширение [Функций Azure](../functions-overview.md), которое позволяет писать функции с отслеживанием состояния в беcсерверной среде. Расширение автоматически управляет состоянием, создает контрольные точки и перезагружается.

## <a name="benefits"></a>Преимущества

Расширение позволяет вам определять рабочие процессы с отслеживанием состояния с помощью [*Функции оркестратора*](durable-functions-types-features-overview.md#orchestrator-functions), которая предоставляет следующие преимущества.

* Рабочие процессы вы можете определить в коде. Не требуются схемы JSON или конструкторы.
* Другие функции можно вызывать как синхронно, так и асинхронно. Выходные данные вызванной функции могут быть сохранены в локальных переменных.
* Ход выполнения автоматически фиксируется, когда функция ожидает. Локальное состояние не теряется при перезапуске процесса или при перезагрузке виртуальной машины.

## <a name="application-patterns"></a>Шаблоны приложений

Основной вариант использования Устойчивых функций — это упрощение комплексных требований координации с отслеживанием состояния в безсерверных приложениях. Ниже приведены типичные шаблоны приложений, в которых можно эффективно использовать устойчивые функции.

* [Построение цепочек](durable-functions-concepts.md#chaining)
* [Развертывание и объединение](durable-functions-concepts.md#fan-in-out)
* [Асинхронные API-интерфейсы HTTP](durable-functions-concepts.md#async-http)
* [Мониторинг](durable-functions-concepts.md#monitoring)
* [Участие пользователя](durable-functions-concepts.md#human)

## <a name="language-support"></a>Поддерживаемые языки

Устойчивые функции в настоящее время поддерживают следующие языки.

* **C#** : обе [предварительно скомпилированные библиотеки классов](../functions-dotnet-class-library.md) и [сценарий C#](../functions-reference-csharp.md).
* **F#** : предварительно скомпилированные библиотеки классов и сценарий F#. Сценарий F# (.fsx) поддерживается только в среде выполнения Функций Azure версии 1.x.
* **JavaScript**: поддерживается только для версии 2.x среды выполнения Функций Azure. Требуется расширение устойчивых функций версии 1.7.0 или более поздней версии. 

Целью устойчивых функций является поддержка всех [языков Функций Azure](../supported-languages.md). См. [здесь](https://github.com/Azure/azure-functions-durable-extension/issues) список проблем Устойчивых функций для получения последних сведений о состоянии работы для поддержки дополнительных языков.

Подобно Функциям Azure, доступны шаблоны, которые помогут вам в разработке устойчивых функций с помощью [Visual Studio 2019](durable-functions-create-first-csharp.md), [Visual Studio Code](quickstart-js-vscode.md) и [портала Azure](durable-functions-create-portal.md).

## <a name="billing"></a>Выставление счетов

Счета за устойчивые функции выставляются так же, как и за Функции Azure. Дополнительные сведения см. на странице [цен на Функции Azure](https://azure.microsoft.com/pricing/details/functions/).

## <a name="jump-right-in"></a>Приступайте к работе

Вы можете начать работу с Устойчивыми функциями менее чем за 10 минут, просмотрев одно из следующих кратких руководств по конкретным языкам.

* [Создание устойчивой функции на C#](durable-functions-create-first-csharp.md)
* [Создание устойчивой функции с помощью JavaScript](quickstart-js-vscode.md)

В обоих кратких руководствах вы локально создаете и тестируете устойчивую функцию "Hello world". Затем вы опубликуете код функции в Azure. Функция, которую вы создаете, организовывает и объединяет в цепочку вызовы других функций.

## <a name="learn-more"></a>Подробнее

В следующем видео показаны преимущества Устойчивых функций.

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Durable-Functions-in-Azure-Functions/player] 

Так как Устойчивые функции — это дополнительное расширение решения [Функции Azure](../functions-overview.md), оно подходит не для всех приложений. Дополнительные сведения об устойчивых функций см. в разделе [Durable Functions patterns and technical concepts](durable-functions-concepts.md) (Шаблоны устойчивых функций и технические концепции). Сравнение с другими технологиями оркестрации Azure см. в разделе [Сравнение служб "Функции Azure" и Azure Logic Apps](../functions-compare-logic-apps-ms-flow-webjobs.md#compare-azure-functions-and-azure-logic-apps).

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Durable Functions patterns and technical concepts](durable-functions-concepts.md) (Шаблоны устойчивых функций и технические концепции)
