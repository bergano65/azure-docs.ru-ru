---
title: Что нового - Язык Понимание (LUIS)
description: Эта статья регулярно пополняется новостями о API изучения языка когнитивных служб Azure.
ms.topic: conceptual
ms.date: 02/11/2020
ms.openlocfilehash: 6334b157f68f09f2b165c6073ba63f45a0caf3c4
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81538024"
---
# <a name="whats-new-in-language-understanding"></a>Что нового в понимании языка

Узнайте о новых возможностях службы. Эти элементы включают в себя выпуск заметок, видео, сообщения в блоге и другие типы информации. Создайте закладку для этой страницы, чтобы получать последние сведения об обновлениях службы.

## <a name="release-notes"></a>Заметки о выпуске

### <a name="march-2020"></a>Март 2020 г.

* TLS 1.2 теперь применяется для всех запросов HTTP к этой службе. Для получения дополнительной [Azure Cognitive Services security](../cognitive-services-security.md)информации см.

### <a name="november-4-2019---ignite"></a>4 ноября 2019 - Зажигание

* Видео - [Расширенный естественный язык Понимание (NLU) модели с использованием LUIS и Azure когнитивных услуг (ru) BRK2188](https://www.youtube.com/watch?v=JdJEV2jV0_Y)

* Повышение производительности разработчика
    * Общая доступность нашей [конечной точки прогнозирования V3](luis-migration-api-v3.md).
    * Возможность импорта и экспорта приложений с форматом .lu ([LUDown).](https://github.com/microsoft/botbuilder-tools/tree/master/packages/Ludown) Это открывает путь к эффективному процессу CI/CD.
* Расширение языка
    * [Арабский и хинди](luis-language-support.md) в публичном предварительном просмотре.
* Предварительно созданные модели
    * [Предварительно построенные домены](luis-reference-prebuilt-domains.md) теперь, как правило, доступны (GA)
    * Японские [предпостроенные сущности](luis-reference-prebuilt-entities.md#japanese-entity-support) - возраст, валюта, число и процент не поддерживаются в V3.
    * Итальянский [предпостроенный субъектов](luis-reference-prebuilt-entities.md#italian-entity-support) - возраст, валюта, размер, число, и процентное разрешение изменено с V2.
* Улучшенный пользовательский опыт в [preview.luis.ai портале](https://preview.luis.ai) - обновленный опыт маркировки, позволяющий строить и отлаживать сложные модели. Попробуйте предварительный портал учебники:
    * [Только намерения](tutorial-intents-only.md)
    * [Разлагаемая машино-изученная сущность](tutorial-machine-learned-entity.md)
* Предварительные возможности понимания языка - [создание сложных языковых моделей](luis-concept-entity-types.md) с меньшими усилиями.
* Определите функции машинного обучения на уровне модели и позволяет использовать модели в качестве сигналов другим моделям, например, используя объекты в качестве объектов для намерений и для других сущностей.
* Новые, [расширенные лимиты](luis-boundaries.md) - более высокий максимум для списков фраз и общих фраз, новая модель как ограничение функций
* Извлекайте информацию из текста в формате глубокой структуры иерархии, делая разговорные приложения более мощными.

    ![машинно-изученное изображение сущности](./media/whats-new/deep-entity-extraction-example.png)

### <a name="september-3-2019"></a>3 сентября 2019 г.

* Ресурс авторизации Azure - [мигрировать сейчас](luis-migration-authoring.md).
    * 500 приложений на ресурс Azure
    * 100 версий на приложение
* Турецкая поддержка заранее построенных предприятий
* Итальянская поддержка datetimeV2

### <a name="july-23-2019"></a>23 июля 2019 года

* Обновление [распознавателей-Текст](https://github.com/microsoft/Recognizers-Text/releases/tag/dotnet-v1.2.3) до 1.2.3
    * Возраст, температура, измерение и распознавание валют на итальянском языке.
    * Улучшение распознавания праздников на английском языке, чтобы правильно рассчитать даты, основанные на День Благодарения.
    * Улучшения во французском DateTime для уменьшения ложных срабатываний не-Дата и не-времени лиц.
    * Поддержка календаря/школы/финансового года и аббревиатур на английском языке DateRange.
    * Улучшенное распознавание PhoneNumber на китайском и японском языках.
    * Улучшенная поддержка NumberRange на английском языке.
    * повышение производительности;

### <a name="june-24-2019"></a>24 июня 2019 года

* [OrdinalV2 предварительно построенная сущность](luis-reference-prebuilt-ordinal-v2.md) для поддержки заказа, например, следующего, предыдущего и последнего. Только английская культура.

### <a name="may-6-2019---build-conference"></a>6 мая 2019 - //Build Conference

На конференции Build 2019 были опубликованы следующие функции:

* [Предварительный просмотр руководства по миграции API V3](luis-migration-api-v3.md)
* [Улучшенная панель мониторинга аналитики](luis-how-to-use-dashboard.md)
* [Улучшенные предварительно построенные домены](luis-reference-prebuilt-domains.md)
* [Объекты динамического списка](schema-change-prediction-runtime.md#dynamic-lists-passed-in-at-prediction-time)
* [Внешние субъекты](schema-change-prediction-runtime.md#external-entities-passed-in-at-prediction-time)

## <a name="blogs"></a>Блоги

[Bot Framework](https://blog.botframework.com/)

## <a name="videos"></a>Видео

### <a name="2019-ignite-videos"></a>2019 Зажигание видео

[Расширенные модели понимания естественного языка (NLU) с использованием когнитивных служб LUIS и Azure BRK2188](https://www.youtube.com/watch?v=JdJEV2jV0_Y)

### <a name="2019-build-videos"></a>2019 Сборка видео

[Как использовать Azure Conversational AI для масштабирования бизнеса для следующего поколения](https://www.youtube.com/watch?v=_k97jd-csuk&feature=youtu.be)

## <a name="service-updates"></a>Обновления службы

[Объявление об обновлениях Azure для Cognitive Services](https://azure.microsoft.com/updates/?product=cognitive-services)
