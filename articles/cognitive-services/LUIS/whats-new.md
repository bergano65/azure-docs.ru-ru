---
title: Новые возможности Language Understanding (LUIS)
description: В этой статье регулярно обновляются новости о API распознавания речи Cognitive Services Azure.
ms.topic: conceptual
ms.date: 02/11/2020
ms.openlocfilehash: 716860b54e7d8e75984c0365cac61d14153c09ff
ms.sourcegitcommit: b95983c3735233d2163ef2a81d19a67376bfaf15
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/11/2020
ms.locfileid: "77137809"
---
# <a name="whats-new-in-language-understanding"></a>Новые возможности Language Understanding

Узнайте о новых возможностях службы. К этим элементам относятся заметки о выпуске, видео, записи в блоге и другие типы информации. Создайте закладку для этой страницы, чтобы получать последние сведения об обновлениях службы.

## <a name="release-notes"></a>Заметки о выпуске

### <a name="november-4-2019---ignite"></a>4 ноября 2019-Ignite

* Видео- [сложные модели естественного Language Understanding (НЛУ) с использованием Luis и Azure Cognitive Services | BRK2188](https://www.youtube.com/watch?v=JdJEV2jV0_Y)

* Улучшенная производительность разработчиков
    * Общая доступность нашей [конечной точки прогнозирования версии 3](luis-migration-api-v3.md).
    * Возможность импорта и экспорта приложений с помощью формата Lu ([лудовн](https://github.com/microsoft/botbuilder-tools/tree/master/packages/Ludown)). Это создает условия способ эффективного процесса CI/CD.
* Расширение языка
    * [Арабский и хинди](luis-language-support.md) в общедоступной предварительной версии.
* Предварительно созданные модели
    * Предварительно [созданные домены](luis-reference-prebuilt-domains.md) теперь общедоступны (GA)
    * Японские предварительно [подготовленные сущности](luis-reference-prebuilt-entities.md#japanese-entity-support) — возраст, валюта, число и проценты не поддерживаются в версии 3.
    * Предварительно [собранные сущности](luis-reference-prebuilt-entities.md#italian-entity-support) итальянского размера, валюты, измерения, числа и разрешения в процентах изменились с v2.
* Улучшенное взаимодействие с пользователем на [портале Preview.Luis.AI](https://preview.luis.ai) . Усовершенствованный интерфейс добавления меток, позволяющий создавать и отлаживать сложные модели. Попробуйте ознакомиться с учебниками по порталу предварительной версии:
    * [Только для целей](tutorial-intents-only.md)
    * [Делимыми сущность, занятая компьютером](tutorial-machine-learned-entity.md)
* Дополнительные возможности языкового понимания — [Создание сложных языковых моделей](luis-concept-entity-types.md) с меньшими усилиями.
* Определение функций машинного обучения на уровне модели и включение использования моделей в качестве сигналов для других моделей, например использование сущностей в качестве функций для целей и других сущностей.
* Новые, расширенные [ограничения](luis-boundaries.md) — более высокие значения для списков фраз и итоговых фраз, Новая модель — ограничения функций
* Извлекайте информацию из текста в формате структуры глубокой иерархии, делая диалоговые приложения более мощными.

    ![образ сущности, полученный от компьютера](./media/whats-new/deep-entity-extraction-example.png)

### <a name="september-3-2019"></a>3 сентября 2019 г.

* Azure Authoring Resource. [выполните миграцию сейчас](luis-migration-authoring.md).
    * 500 приложений на ресурс Azure
    * 100 версий на приложение
* Поддержка турецкого языка для предварительно созданных сущностей
* Поддержка итальянского языка для datetimeV2

### <a name="july-23-2019"></a>23 июля 2019 г.

* Обновление [распознавателей — текст](https://github.com/microsoft/Recognizers-Text/releases/tag/dotnet-v1.2.3) на 1.2.3
    * Распознаватели Age, температуре, измерений и валют на итальянском языке.
    * Улучшение распознавания праздников на английском языке для правильного вычисления дат на основе благодарения.
    * Улучшения на французском языке DateTime для сокращения количества ложных срабатываний сущностей, не являющихся датами и не являющихся временными.
    * Поддержка календарного, учебного и финансового года и акронимов на английском DateRange.
    * Улучшенное распознавание PhoneNumber в китайском и японском языках.
    * Улучшенная поддержка Нумберранже на английском языке.
    * Повышение производительности.

### <a name="june-24-2019"></a>24 июня 2019 г.

* [OrdinalV2 предварительно созданную сущность](luis-reference-prebuilt-ordinal-v2.md) для поддержки упорядочивания, например следующего, предыдущего и последнего. Только английский язык.

### <a name="may-6-2019---build-conference"></a>6 мая, 2019-//Build Конференция

Следующие функции были выпущены на Конференции Build 2019:

* [Предварительная версия инструкции по миграции API V3](luis-migration-api-v3.md)
* [Улучшенная панель мониторинга аналитики](luis-how-to-use-dashboard.md)
* [Улучшенные предварительно созданные домены](luis-reference-prebuilt-domains.md)
* [Сущности динамического списка](luis-migration-api-v3.md#dynamic-lists-passed-in-at-prediction-time)
* [Внешние сущности](luis-migration-api-v3.md#external-entities-passed-in-at-prediction-time)

## <a name="blogs"></a>Блоги

[Bot Framework](https://blog.botframework.com/)

## <a name="videos"></a>Видео

### <a name="2019-ignite-videos"></a>видео 2019 Ignite

[Сложные модели Natural Language Understanding (НЛУ) с использованием LUIS и Azure Cognitive Services | BRK2188](https://www.youtube.com/watch?v=JdJEV2jV0_Y)

### <a name="2019-build-videos"></a>видео о сборке 2019

[Как использовать службу искусственного интеллекта Azure для масштабирования вашего бизнеса для следующего поколения](https://www.youtube.com/watch?v=_k97jd-csuk&feature=youtu.be)

## <a name="service-updates"></a>Обновления службы

[Объявление об обновлениях Azure для Cognitive Services](https://azure.microsoft.com/updates/?product=cognitive-services)
