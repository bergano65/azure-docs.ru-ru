---
title: Использование Анализ текста для работоспособности
titleSuffix: Azure Cognitive Services
description: Узнайте, как извлекать и помечать медицинские сведения из неструктурированного текста клинической практике с Анализ текста для работоспособности.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 12/17/2020
ms.author: aahi
ms.custom: references_regions
ms.openlocfilehash: 8159010486371d619aa14a845050a0eb38aaeda8
ms.sourcegitcommit: e0ec3c06206ebd79195d12009fd21349de4a995d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/18/2020
ms.locfileid: "97683533"
---
# <a name="how-to-use-text-analytics-for-health-preview"></a>Как использовать Анализ текста для работоспособности (Предварительная версия)

> [!IMPORTANT] 
> Анализ текста для работоспособности — это возможность предварительной версии, предоставленная "как есть" и "со всеми ОШИБКАми". Таким образом, **анализ текста для работоспособности (Предварительная версия) не следует реализовывать и развертывать в любом рабочем использовании.** Анализ текста для обеспечения работоспособности не предназначены для использования в качестве медицинских устройств, поддержки клинической практике, диагностического средства или других технологий, предназначенных для диагностики, получения, устранения, обработки или предотвращения болезни или других условий, и ни одна из лицензий или прав не предоставляется корпорацией Майкрософт для использования этой возможности в таких целях. Эта возможность не предназначена для реализации или развертывания в качестве замены для профессиональных медицинских консультаций или здравоохранения, диагностики, лечения или клинической практике, которые являются нежелательными специалистами здравоохранения и не должны использоваться. Клиент несет ответственность за использование Анализ текста для работоспособности. Корпорация Майкрософт не гарантирует, что Анализ текста для обеспечения работоспособности или каких бы то ни было материалов, предоставляемых в связи с этой возможностью, будет достаточно для любых медицинских целей или иным образом соответствовать требованиям или медицинских нужд любого человека. 


Анализ текста для работоспособности — это функция службы API анализа текста, которая извлекает и помечает соответствующие медицинские сведения из неструктурированных текстов, таких как заметки Doctor, сводки разрядов, документы клинической практике и электронные записи о работоспособности.  Использовать эту службу можно двумя способами: 

* Веб-API (асинхронный) 
* Контейнер DOCKER (синхронный);   

> [!VIDEO https://channel9.msdn.com/Shows/AI-Show/Introducing-Text-Analytics-for-Health/player]

## <a name="features"></a>Компоненты

Анализ текста для проверки работоспособности: распознавание сущностей (NER), извлечение отношений, отрицание сущностей и связывание сущностей на английском языке для получения аналитических сведений в неструктурированном клинической практике и специальном тексте.

### <a name="named-entity-recognition"></a>[Распознавание именованных сущностей](#tab/ner)

Распознавание именованных сущностей обнаруживает слова и фразы, упомянутые в неструктурированном тексте, которые могут быть связаны с одним или несколькими семантическими типами, такими как диагностика, имя лечения, симптом, подпись или возраст.

> [!div class="mx-imgBorder"]
> ![NER работоспособности](../media/ta-for-health/health-named-entity-recognition.png)

### <a name="relation-extraction"></a>[Извлечение связей](#tab/relation-extraction)

Извлечение связей определяет осмысленные соединения между понятиями, упомянутыми в тексте. Например, связь "время условия" обнаруживается путем связывания имени условия с временем. 

> [!div class="mx-imgBorder"]
> ![Повтор работоспособности](../media/ta-for-health/health-relation-extraction.png)


### <a name="entity-linking"></a>[Связывание сущностей](#tab/entity-linking)

Сущность, связывающая сущности различения, сопоставляя именованные сущности, упомянутые в тексте, с концепциями, найденными в предопределенной базе данных концепций. Например, единая медицинское языковая система (УМЛС).

> [!div class="mx-imgBorder"]
> ![Работоспособность EL](../media/ta-for-health/health-entity-linking.png)

Анализ текста для работоспособности поддерживает связывание с работоспособностью и биомедицинских словари, найденных в источнике знаний об метасесаурусе единой системы обработки медицинских языков ([умлс](https://www.nlm.nih.gov/research/umls/sourcereleasedocs/index.html)).

### <a name="negation-detection"></a>[Обнаружение отрицания](#tab/negation-detection) 

Значение медицинских материалов сильно влияет на модификаторы, такие как отрицание, которое может иметь критически важное значение при неточной диагностике. Анализ текста для работоспособности поддерживает обнаружение отрицания для различных сущностей, упомянутых в тексте. 

> [!div class="mx-imgBorder"]
> ![РАСХОД работоспособности](../media/ta-for-health/health-negation.png)

---

Полный список поддерживаемых сущностей см. в статье [категории сущностей](../named-entity-types.md?tabs=health) , возвращенных анализ текста. Сведения о рейтинге достоверности см. в разделе [анализ текстаная заметка о прозрачности](/legal/cognitive-services/text-analytics/transparency-note#general-guidelines-to-understand-and-improve-performance?context=/azure/cognitive-services/text-analytics/context/context). 

### <a name="supported-languages-and-regions"></a>Поддерживаемые языки и регионы

Анализ текста для работоспособности поддерживает только документы на английском языке. 

Анализ текста для веб-API, размещенного в состоянии работоспособности, сейчас доступны только в следующих регионах: Западная часть США 2, Восточная часть США 2, Центральная часть США, Северная Европа и Западная Европа.

## <a name="request-access-to-the-public-preview"></a>Запрос доступа к общедоступной предварительной версии

Заполните и отправьте [форму запроса Cognitive Services](https://aka.ms/csgate) , чтобы запросить доступ к анализ текста для общедоступной предварительной версии работоспособности. Вам не будет выставлен счет за Анализ текста для использования работоспособности. 

В форме нужно указать сведения о себе, компании и пользовательском сценарии, для которого будет использоваться контейнер. После отправки формы команда Azure Cognitive Services будет просматривать ее и отправлять вам решение.

> [!IMPORTANT]
> * В форме необходимо использовать адрес электронной почты, связанный с ИДЕНТИФИКАТОРом подписки Azure.
> * Используемый ресурс Azure должен быть создан с утвержденным ИДЕНТИФИКАТОРом подписки Azure. 
> * Проверьте электронную почту (папки "Входящие" и "спам") на наличие обновлений для приложения от корпорации Майкрософт.

## <a name="using-the-docker-container"></a>Использование контейнера DOCKER 

Чтобы запустить Анализ текста для контейнера работоспособности в своей среде, выполните следующие [инструкции, чтобы скачать и установить контейнер](../how-tos/text-analytics-how-to-install-containers.md?tabs=healthcare).

## <a name="using-the-client-library"></a>Использование клиентской библиотеки

Последняя Предварительная версия клиентской библиотеки Анализ текста позволяет вызывать Анализ текста для работоспособности с помощью клиентского объекта. См. справочную документацию и примеры на сайте GitHub:
* [C#](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics)
* [Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/)
* [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics)



## <a name="sending-a-rest-api-request"></a>Отправка запроса к REST API 

### <a name="preparation"></a>Подготовка

Анализ текста для работоспособности позволяет получить более качественный результат, если вы придаете ему меньшее количество текста для работы. Это противоположено некоторым другим Анализ текстаным функциям, таким как извлечение ключевых фраз, которые лучше подходят для больших блоков текста. Чтобы получить наилучшие результаты из этих операций, рассмотрите возможность реструктуризации входных данных соответствующим образом.

Необходимы документы JSON в следующем формате: ID, текст и язык. 

Размер документа должен быть менее 5120 символов. Максимальное число документов, которые могут содержаться в коллекции, см. в статье об [ограничениях данных](../concepts/data-limits.md?tabs=version-3) в разделе "Основные понятия". Коллекция передается в тексте запроса.

### <a name="structure-the-api-request-for-the-hosted-asynchronous-web-api"></a>Структурирование запроса API для размещенного асинхронного веб-API

Для контейнера и размещенного веб-API необходимо создать запрос POST. Чтобы быстро создать и отправить запрос POST в размещенный веб-API в нужном регионе, можно [воспользоваться командой POST](text-analytics-how-to-call-api.md), фигурой или **консолью тестирования API** в [анализ текста](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/Health) . 

Ниже приведен пример JSON-файла, присоединенного к Анализ текста для текста POST запроса API работоспособности:

```json
example.json

{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "Subject was administered 100mg remdesivir intravenously over a period of 120 min"
    }
  ]
}
```

### <a name="hosted-asynchronous-web-api-response"></a>Размещенный асинхронный ответ веб-API 

Так как этот запрос POST используется для отправки задания для асинхронной операции, в объекте Response нет текста.  Однако для выполнения запроса GET для проверки состояния задания и выходных данных требуется значение ключа Operation-Location в заголовках ответа.  Ниже приведен пример значения ключа Location операции в заголовке ответа запроса POST:

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.3/entities/health/jobs/<jobID>`

Чтобы проверить состояние задания, выполните запрос GET к URL-адресу в заголовке ключа операции запроса POST.  Для отражения состояния задания используются следующие состояния: `NotStarted` , `running` ,,,, `succeeded` `failed` `rejected` `cancelling` и `cancelled` .  

Можно отменить задание с `NotStarted` состоянием или, `running` используя вызов HTTP DELETE с тем же URL-адресом, что и у запроса GET.  Дополнительные сведения о вызове DELETE доступны в [анализ текста для Справочника по API, размещенного в исправности](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/CancelHealthJob).

Ниже приведен пример ответа на запрос GET.  Обратите внимание, что выходные данные доступны для получения до тех пор, пока не `expirationDateTime` прошло (24 часа со времени создания задания), после чего выходные данные очищаются.

```json
{
    "jobId": "b672c6f5-7c0d-4783-ba8c-4d0c47213454",
    "lastUpdateDateTime": "2020-11-18T01:45:00Z",
    "createdDateTime": "2020-11-18T01:44:55Z",
    "expirationDateTime": "2020-11-19T01:44:55Z",
    "status": "succeeded",
    "errors": [],
    "results": {
        "documents": [
            {
                "id": "1",
                "entities": [
                    {
                        "offset": 25,
                        "length": 5,
                        "text": "100mg",
                        "category": "Dosage",
                        "confidenceScore": 1.0,
                        "isNegated": false
                    },
                    {
                        "offset": 31,
                        "length": 10,
                        "text": "remdesivir",
                        "category": "MedicationName",
                        "confidenceScore": 1.0,
                        "isNegated": false,
                        "links": [
                            {
                                "dataSource": "UMLS",
                                "id": "C4726677"
                            },
                            {
                                "dataSource": "MSH",
                                "id": "C000606551"
                            },
                            {
                                "dataSource": "NCI",
                                "id": "C152185"
                            },
                            {
                                "dataSource": "NCI_FDA",
                                "id": "3QKI37EEHE"
                            }
                        ]
                    },
                    {
                        "offset": 42,
                        "length": 13,
                        "text": "intravenously",
                        "category": "MedicationRoute",
                        "confidenceScore": 1.0,
                        "isNegated": false
                    },
                    {
                        "offset": 56,
                        "length": 4,
                        "text": "over",
                        "category": "Time",
                        "confidenceScore": 0.87,
                        "isNegated": false
                    },
                    {
                        "offset": 73,
                        "length": 7,
                        "text": "120 min",
                        "category": "Time",
                        "confidenceScore": 0.99,
                        "isNegated": false
                    }
                ],
                "relations": [
                    {
                        "relationType": "DosageOfMedication",
                        "bidirectional": false,
                        "source": "#/results/documents/0/entities/0",
                        "target": "#/results/documents/0/entities/1"
                    },
                    {
                        "relationType": "RouteOfMedication",
                        "bidirectional": false,
                        "source": "#/results/documents/0/entities/2",
                        "target": "#/results/documents/0/entities/1"
                    },
                    {
                        "relationType": "TimeOfMedication",
                        "bidirectional": false,
                        "source": "#/results/documents/0/entities/3",
                        "target": "#/results/documents/0/entities/1"
                    },
                    {
                        "relationType": "TimeOfMedication",
                        "bidirectional": false,
                        "source": "#/results/documents/0/entities/4",
                        "target": "#/results/documents/0/entities/1"
                    }
                ],
                "warnings": []
            }
        ],
        "errors": [],
        "modelVersion": "2020-09-03"
    }
}
```


### <a name="structure-the-api-request-for-the-container"></a>Структурирование запроса API для контейнера

Чтобы отправить запрос в развернутый контейнер, можно [использовать POST](text-analytics-how-to-call-api.md) или пример запроса с фигурными скобками, заменив `serverURL` переменную соответствующим значением.  Обратите внимание, что версия API в URL-адресе для контейнера отличается от размещенного API.

```bash
curl -X POST 'http://<serverURL>:5000/text/analytics/v3.2-preview.1/entities/health' --header 'Content-Type: application/json' --header 'accept: application/json' --data-binary @example.json

```

Ниже приведен пример JSON-файла, присоединенного к Анализ текста в теле запроса API работоспособности:

```json
example.json

{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "Patient reported itchy sores after swimming in the lake."
    },
    {
      "language": "en",
      "id": "2",
      "text": "Prescribed 50mg benadryl, taken twice daily."
    }
  ]
}
```

### <a name="container-response-body"></a>Тело ответа контейнера

Следующий код JSON является примером Анализ текста для текста ответа API работоспособности из контейнерного синхронного вызова:

```json
{
    "documents": [
        {
            "id": "1",
            "entities": [
                {
                    "id": "0",
                    "offset": 25,
                    "length": 5,
                    "text": "100mg",
                    "category": "Dosage",
                    "confidenceScore": 1.0,
                    "isNegated": false
                },
                {
                    "id": "1",
                    "offset": 31,
                    "length": 10,
                    "text": "remdesivir",
                    "category": "MedicationName",
                    "confidenceScore": 1.0,
                    "isNegated": false,
                    "links": [
                        {
                            "dataSource": "UMLS",
                            "id": "C4726677"
                        },
                        {
                            "dataSource": "MSH",
                            "id": "C000606551"
                        },
                        {
                            "dataSource": "NCI",
                            "id": "C152185"
                        },
                        {
                            "dataSource": "NCI_FDA",
                            "id": "3QKI37EEHE"
                        }
                    ]
                },
                {
                    "id": "2",
                    "offset": 42,
                    "length": 13,
                    "text": "intravenously",
                    "category": "MedicationRoute",
                    "confidenceScore": 1.0,
                    "isNegated": false
                },
                {
                    "id": "3",
                    "offset": 56,
                    "length": 4,
                    "text": "over",
                    "category": "Time",
                    "confidenceScore": 0.87,
                    "isNegated": false
                },
                {
                    "id": "4",
                    "offset": 73,
                    "length": 7,
                    "text": "120 min",
                    "category": "Time",
                    "confidenceScore": 0.99,
                    "isNegated": false
                }
            ],
            "relations": [
                {
                    "relationType": "DosageOfMedication",
                    "bidirectional": false,
                    "source": "#/documents/0/entities/0",
                    "target": "#/documents/0/entities/1"
                },
                {
                    "relationType": "RouteOfMedication",
                    "bidirectional": false,
                    "source": "#/documents/0/entities/2",
                    "target": "#/documents/0/entities/1"
                },
                {
                    "relationType": "TimeOfMedication",
                    "bidirectional": false,
                    "source": "#/documents/0/entities/3",
                    "target": "#/documents/0/entities/1"
                },
                {
                    "relationType": "TimeOfMedication",
                    "bidirectional": false,
                    "source": "#/documents/0/entities/4",
                    "target": "#/documents/0/entities/1"
                }
            ]
        }
    ],
    "errors": [],
    "modelVersion": "2020-09-03"
}
```

### <a name="negation-detection-output"></a>Вывод обнаружения отрицания

При использовании обнаружения отрицания в некоторых случаях один термин отрицания может одновременно обращаться к нескольким терминам. Отрицание распознанной сущности представлено в выходных данных JSON логическим значением `isNegated` флага, например:

```json
{
  "id": "2",
  "offset": 90,
  "length": 10,
  "text": "chest pain",
  "category": "SymptomOrSign",
  "score": 0.9972,
  "isNegated": true,
  "links": [
    {
      "dataSource": "UMLS",
      "id": "C0008031"
    },
    {
      "dataSource": "CHV",
      "id": "0000023593"
    },
    ...
```

### <a name="relation-extraction-output"></a>Выход извлечения связи

Выход извлечения связи содержит ссылки URI на *источник* связи и его *целевой объект*. Сущности с ролью отношения `ENTITY` принадлежат `target` полю. Сущности с ролью отношения `ATTRIBUTE` принадлежат `source` полю. Связи аббревиатур содержат двунаправленную форму `source` и `target` поля, `bidirectional` для которых будет задано значение `true` . 

```json
"relations": [
                {
                    "relationType": "DosageOfMedication",
                    "bidirectional": false,
                    "source": "#/documents/1/entities/0",
                    "target": "#/documents/1/entities/1"
                },
                {
                    "relationType": "FrequencyOfMedication",
                    "bidirectional": false,
                    "source": "#/documents/1/entities/2",
                    "target": "#/documents/1/entities/1"
                }
            ]
  },
...
]
```

## <a name="see-also"></a>См. также раздел

* [Text Analytics overview](../overview.md) (Общие сведения об анализе текста)
* [Категории именованных сущностей](../named-entity-types.md)
* [Новые возможности](../whats-new.md)
