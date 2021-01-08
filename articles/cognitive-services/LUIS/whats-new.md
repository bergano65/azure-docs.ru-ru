---
title: Новые возможности службы "Распознавание речи" (LUIS)
description: Эта статья регулярно обновляется последними сведениями об API Распознавания речи в Azure Cognitive Services.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: overview
ms.date: 06/15/2020
ms.openlocfilehash: 52f8aad0a429d3378d46265fab719b1cb9f07fd0
ms.sourcegitcommit: 1140ff2b0424633e6e10797f6654359947038b8d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/30/2020
ms.locfileid: "97813196"
---
# <a name="whats-new-in-language-understanding"></a>Новые возможности службы "Распознавание речи"

Узнайте о новых возможностях службы. Это могут быть заметки о выпуске, видео, записи блогов и другие типы информации. Создайте закладку для этой страницы, чтобы получать последние сведения об обновлениях службы.

## <a name="release-notes"></a>Заметки о выпуске

### <a name="december-2020"></a>Декабрь 2020 г.

* Все пользователи LUIS должны [перейти на ресурс authorint LUIS](luis-migration-authoring.md)

### <a name="june-2020"></a>Июнь 2020 г.

* [Предварительная версия 3.0 (разработка)](luis-migration-authoring-entities.md), пакет SDK:
    * Версия 3.2.0-preview.3: [.NET — NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Authoring/)
    * Версия 4.0.0-preview.3: [JS — NPM](https://www.npmjs.com/package/@azure/cognitiveservices-luis-authoring)
* Применение методик DevOps в LUIS
    * Основные понятия
        * [Рекомендации по использованию DevOps с LUIS](luis-concept-devops-sourcecontrol.md)
        * [Рабочие процессы непрерывной интеграции и непрерывной поставки для LUIS DevOps](luis-concept-devops-automation.md)
        * [Тестирование для LUIS DevOps](luis-concept-devops-testing.md)
    * Практическое руководство
        * [Применение DevOps при разработке приложений LUIS с помощью GitHub Actions](luis-how-to-devops-with-github.md)
    * [Полный репозиторий кода GitHub](https://github.com/Azure-Samples/LUIS-DevOps-Template)

### <a name="may-2020---build"></a>Май 2020 года — //Build

* Выпущена в качестве **общедоступной версии** (GA)
    * [Контейнер Распознавания речи](luis-container-howto.md)
    * Замена предварительной версии портала [текущей](https://www.luis.ai) ([предыдущий](https://previous.luis.ai) портал по-прежнему доступен)
    * новый интерфейс для создания сущностей машинного обучения и их маркировки;
    * [процесс обновления](migrate-from-composite-entity.md) составных и простых сущностей до сущностей машинного обучения;
    * [Настройка поддержки](how-to-application-settings-portal.md) нормализации вариантов слов
* Изменения в предварительной версии API разработки
    * схема приложения 7.x для вложенных сущностей машинного обучения;
    * [Миграция на требуемую функцию](luis-migration-authoring-entities.md#api-change-constraint-replaced-with-required-feature)
* Новые ресурсы для разработчиков:
    * [средства непрерывной интеграции](developer-reference-resource.md#continuous-integration-tools);
    * семинар с рекомендациями по [_распознаванию естественной речи_ (NLU) с использованием LUIS](developer-reference-resource.md#workshops).
* [Управляемые пользователем ключи](luis-encryption-of-data-at-rest.md) — шифруйте все данные, используемые в LUIS, с помощью собственного ключа.
* [Мероприятие, посвященное ИИ](https://channel9.msdn.com/Shows/AI-Show/New-Features-in-Language-Understanding) (видео) — узнайте о новых функциях LUIS.



### <a name="march-2020"></a>Март 2020 г.

* TLS 1.2 теперь применяется для всех HTTP-запросов к этой службе. Дополнительные сведения см. в статье [Безопасность в Azure Cognitive Services](../cognitive-services-security.md).

### <a name="november-4-2019---ignite"></a>4 ноября 2019 года — Ignite

* Видео. [Расширенные модели естественного распознавания (NLU), использующие LUIS и Azure Cognitive Services | BRK2188](https://www.youtube.com/watch?v=JdJEV2jV0_Y).

* Повышена продуктивность разработки.
    * Общая доступность нашей [конечной точки прогнозирования версии 3](luis-migration-api-v3.md).
    * Возможность импортировать и экспортировать приложения в формате `.lu` ([LUDown](https://github.com/microsoft/botbuilder-tools/tree/master/packages/Ludown)). Это создает условия для эффективного процесса CI/CD.
* Добавлены новые языки.
    * [Арабский и хинди](luis-language-support.md) в общедоступной предварительной версии.
* Предварительно созданные модели
    * Теперь общедоступны [предварительно созданные домены](luis-reference-prebuilt-domains.md).
    * В версии 3 не поддерживаются [предварительно созданные сущности](luis-reference-prebuilt-entities.md#japanese-entity-support) для японского (возраст, валюта, число и процент).
    * По сравнению с версией 2 изменилось разрешение [предварительно созданных сущностей](luis-reference-prebuilt-entities.md#italian-entity-support) (возраст, валюта, измерение, число и процент).
* Улучшенное взаимодействие с пользователем на [портале preview.luis.ai](https://preview.luis.ai) — усовершенствованные возможности маркировки, позволяющие создавать и отлаживать сложные модели. Попробуйте ознакомиться с учебниками по порталу предварительной версии:
    * [Только намерения](tutorial-intents-only.md)
    * [Делимые сущности машинного обучения](tutorial-machine-learned-entity.md)
* Расширенные возможности распознавания речи — [создание сложных языковых моделей](luis-concept-entity-types.md) с меньшими усилиями.
* Определение признаков машинного обучения на уровне модели и обеспечение возможности использования моделей в качестве сигналов для других моделей, например использования сущностей в качестве признаков для намерений и других сущностей.
* Новые увеличенные [предельные значения](luis-limits.md) — более высокое максимальное значение для списков фраз и общего количества фраз, новая модель, используемая в качестве предельных значений признаков.
* Извлечение информации из текста в формате структуры глубокой иерархии, расширяющее возможности приложений для общения.

    ![Изображение, демонстрирующее сущность машинного обучения](./media/whats-new/deep-entity-extraction-example.png)

### <a name="september-3-2019"></a>3 сентября 2019 г.

* Ресурс создания Azure — [мигрировать сейчас](luis-migration-authoring.md).
    * 500 приложений на ресурс Azure
    * 100 версий на приложение
* Поддержка турецкого языка для предварительно созданных сущностей
* Поддержка итальянского языка для datetimeV2

### <a name="july-23-2019"></a>23 июля 2019 г.

* Обновление [Recognizers-Text](https://github.com/microsoft/Recognizers-Text/releases/tag/dotnet-v1.2.3) до версии 1.2.3.
    * Распознаватели возраста, температуры, измерений и валют на итальянском языке.
    * Улучшенное распознавание праздников на английском языке для правильного вычисления дат на основе Дня благодарения.
    * Улучшения в сущности DateTime во французском языке, сокращающие количество ложноположительных результатов для сущностей, не являющихся датой или временем.
    * Поддержка календарного, учебного и финансового года и акронимов в сущности DateRange для английского языка.
    * Улучшенное распознавание сущности PhoneNumber в китайском и японском языках.
    * Улучшенная поддержка сущности NumberRange в английском языке.
    * Повышение производительности.

### <a name="june-24-2019"></a>24 июня 2019 г.

* [Предварительно созданная сущность OrdinalV2](luis-reference-prebuilt-ordinal-v2.md) для поддержки упорядочивания, например следующего, предыдущего и последнего. Только для английского языка и региональных параметров.

### <a name="may-6-2019---build-conference"></a>6 мая 2019 г. — //Конференция Build

На конференции Build 2019 г были представлены следующие возможности:

* [Предварительная версия инструкции по миграции API версии 3](luis-migration-api-v3.md)
* [Улучшенная панель мониторинга аналитики](luis-how-to-use-dashboard.md)
* [Улучшенные предварительно созданные домены](luis-reference-prebuilt-domains.md)
* [Сущности динамического списка](schema-change-prediction-runtime.md#dynamic-lists-passed-in-at-prediction-time)
* [Внешние сущности](schema-change-prediction-runtime.md#external-entities-passed-in-at-prediction-time)

## <a name="blogs"></a>Блоги

[Bot Framework](https://blog.botframework.com/)

## <a name="videos"></a>Видео

### <a name="2019-ignite-videos"></a>Видео с Ignite 2019

[Расширенные модели естественного распознавания (NLU), использующие LUIS и Azure Cognitive Services | BRK2188](https://www.youtube.com/watch?v=JdJEV2jV0_Y)

### <a name="2019-build-videos"></a>Видео с Build 2019

[Как использовать службу Azure Conversational AI для масштабирования вашего бизнеса для следующего поколения](https://www.youtube.com/watch?v=_k97jd-csuk&feature=youtu.be)

## <a name="service-updates"></a>Обновления службы

[Объявление об обновлениях Azure для Cognitive Services](https://azure.microsoft.com/updates/?product=cognitive-services)
