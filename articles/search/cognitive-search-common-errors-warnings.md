---
title: Ошибки и предупреждения, касающиеся индексатора
titleSuffix: Azure Cognitive Search
description: В этой статье содержатся сведения и решения для распространенных ошибок и предупреждений, которые могут возникнуть во время обогащения искусственного интеллекта в Azure Когнитивный поиск.
manager: nitinme
author: amotley
ms.author: abmotley
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: fb8aec10d58ed4f2eca462774aeaf61f2ea21dd0
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/10/2019
ms.locfileid: "74973974"
---
# <a name="troubleshooting-common-indexer-errors-and-warnings-in-azure-cognitive-search"></a>Устранение распространенных ошибок индексатора и предупреждений в Azure Когнитивный поиск

Эта статья содержит сведения и решения для распространенных ошибок и предупреждений, которые могут возникнуть во время индексирования и обогащения искусственного интеллекта в Azure Когнитивный поиск.

Индексирование останавливается, если число ошибок превышает ["максфаиледитемс"](cognitive-search-concept-troubleshooting.md#tip-3-see-what-works-even-if-there-are-some-failures). 

Если вы хотите, чтобы индексаторы проигнорировали эти ошибки (и пропустить "документы с ошибками"), обновите `maxFailedItems` и `maxFailedItemsPerBatch`, как описано [здесь](https://docs.microsoft.com/rest/api/searchservice/create-indexer#general-parameters-for-all-indexers).

> [!NOTE]
> Каждый неудачный документ вместе с ключом документа (если он доступен) будет отображаться как ошибка в состоянии выполнения индексатора. Можно использовать [API индексов](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) , чтобы вручную отправить документы в более поздний момент, если вы настроили индексатор для допуска сбоев.

Сведения об ошибке, приведенные в этой статье, помогут устранить ошибки, позволяя продолжить индексирование.

Предупреждения не останавливают индексирование, но они указывают на условия, которые могут привести к непредвиденным результатам. Принимается ли действие или не зависит от данных и сценария.

Начиная с версии API `2019-05-06`ошибки индексатора уровня элемента и предупреждения структурированы таким образом, чтобы обеспечить улучшенную ясность по причинам и дальнейшим действиям. Они содержат следующие свойства:

| Свойство | Описание | Пример |
| --- | --- | --- |
| key | Идентификатор документа, на который влияет ошибка или предупреждение. | HTTPS:\//coromsearch.blob.core.windows.net/jfk-1k/docid-32112954.pdf |
| name | Имя операции, описывающей, где возникла ошибка или предупреждение. Он создается следующей структурой: [Category]. [Подкатегория]. [resourceType]. resourceName | Документекстрактион. azureblob. Миблобконтаинернаме обогащение. Вебапискилл. Мискиллнаме проекция. Сеарчиндекс. Аутпутфиелдмаппинг. myOutputFieldName проекция. SearchIndex. MergeOrUpload. myIndexName Проекция. Кновледжесторе. Table. Митабленаме |
| Message | Высокоуровневое описание ошибки или предупреждения. | Не удалось выполнить навык, так как произошел сбой запроса веб-API. |
| сведения | Дополнительные сведения, которые могут оказаться полезными для диагностики проблемы, например ответ WebApi, если при выполнении пользовательского навыка произошел сбой. | `link-cryptonyms-list - Error processing the request record : System.ArgumentNullException: Value cannot be null. Parameter name: source at System.Linq.Enumerable.All[TSource](IEnumerable`1 источник, Func`2 predicate) at Microsoft.CognitiveSearch.WebApiSkills.JfkWebApiSkills.`... Оставшаяся часть трассировки стека... |
| документатионлинк | Ссылка на соответствующую документацию с подробными сведениями для отладки и устранения проблемы. Эта ссылка часто будет указывать на один из следующих разделов на этой странице. | https://go.microsoft.com/fwlink/?linkid=2106475 |

<a name="could-not-read-document"/>

## <a name="error-could-not-read-document"></a>Ошибка: не удалось прочитать документ

Индексатору не удалось прочитать документ из источника данных. Это может произойти по следующим причинам.

| Причина | Сведения/пример | Разрешение |
| --- | --- | --- |
| несогласованные типы полей в разных документах | Тип значения не соответствует типу столбца. Не удалось сохранить `'{47.6,-122.1}'` в столбце Authors.  Ожидаемый тип — Жаррай. | Убедитесь, что тип каждого поля одинаков для разных документов. Например, если первый документ `'startTime'` поле является типом DateTime, а во втором — строкой, будет достигнута эта ошибка. |
| ошибки базовой службы источника данных | (от Cosmos DB) `{"Errors":["Request rate is large"]}` | Проверьте экземпляр хранилища, чтобы убедиться в его работоспособности. Возможно, потребуется настроить масштабирование или секционирование. |
| временные проблемы | ошибка на транспортном уровне при получении результатов с сервера. (поставщик: поставщик TCP, ошибка: 0 — существующее подключение было принудительно закрыто удаленным узлом | Иногда возникают непредвиденные проблемы с подключением. Попробуйте снова запустить документ через индексатор позже. |

<a name="could-not-extract-document-content"/>

## <a name="error-could-not-extract-document-content"></a>Ошибка: не удалось извлечь содержимое документа
Индексатору с источником данных большого двоичного объекта не удалось извлечь содержимое из документа (например, PDF-файл). Это может произойти по следующим причинам.

| Причина | Сведения/пример | Разрешение |
| --- | --- | --- |
| Превышен предельный размер BLOB-объекта | Документ содержит `'150441598'` байт, что превышает максимальный размер `'134217728'` байт для извлечения документа для текущего уровня служб. | [ошибки индексирования BLOB-объектов](search-howto-indexing-azure-blob-storage.md#dealing-with-errors) |
| тип содержимого BLOB-объекта не поддерживается | Документ имеет неподдерживаемый тип содержимого `'image/png'` | [ошибки индексирования BLOB-объектов](search-howto-indexing-azure-blob-storage.md#dealing-with-errors) |
| BLOB-объект зашифрован | Документ не может быть обработан. он может быть зашифрован или защищен паролем. | Вы можете пропустить большой двоичный объект с помощью [параметров больших двоичных объектов](search-howto-indexing-azure-blob-storage.md#controlling-which-parts-of-the-blob-are-indexed). |
| временные проблемы | Ошибка обработки BLOB-объекта: запрос был прерван: запрос был отменен. | Иногда возникают непредвиденные проблемы с подключением. Попробуйте снова запустить документ через индексатор позже. |

<a name="could-not-parse-document"/>

## <a name="error-could-not-parse-document"></a>Ошибка: не удалось проанализировать документ
Индексатор считывает документ из источника данных, но возникла ошибка при преобразовании содержимого документа в указанную схему сопоставления полей. Это может произойти по следующим причинам.

| Причина | Сведения/пример | Разрешение |
| --- | --- | --- |
| Отсутствует ключ документа | Ключ документа не может быть указан или пуст | Убедитесь, что все документы содержат допустимые ключи документов |
| Недопустимый ключ документа | Длина ключа документа не должна превышать 1024 символов | Измените ключ документа в соответствии с требованиями проверки. |
| Не удалось применить сопоставление полей к полю | Не удалось применить функцию сопоставления `'functionName'` к `'fieldName'`у поля. Массив не может иметь значение null. Имя параметра: байт | Дважды проверьте [сопоставления полей](search-indexer-field-mappings.md) , определенные в индексаторе, и сравните с данными указанного поля документа, в котором произошел сбой. Может потребоваться изменить сопоставления полей или данные документа. |
| Не удалось прочитать значение поля | Не удалось считать значение столбца `'fieldName'` в `'fieldIndex'`индекса. ошибка на транспортном уровне при получении результатов с сервера. (поставщик: поставщик TCP, ошибка: 0 — существующее подключение было принудительно закрыто удаленным узлом.) | Эти ошибки обычно возникают из-за непредвиденных проблем с подключением к базовой службе источника данных. Попробуйте снова запустить документ через индексатор позже. |

<a name="could-not-execute-skill"/>

## <a name="error-could-not-execute-skill"></a>Ошибка: не удалось выполнить навык
Индексатору не удалось запустить навык в наборе навыков.

| Причина | Сведения/пример | Разрешение |
| --- | --- | --- |
| Проблемы с временным подключением | Произошла временная ошибка. Повторите попытку позже. | Иногда возникают непредвиденные проблемы с подключением. Попробуйте снова запустить документ через индексатор позже. |
| Потенциальная ошибка продукта | Произошла непредвиденная ошибка. | Это указывает на неизвестный класс сбоя и может означать наличие ошибки в продукте. Отправьте запрос в [службу поддержки](https://ms.portal.azure.com/#create/Microsoft.Support) , чтобы получить помощь. |
| Во время выполнения навык обнаружил ошибку | (От навыка слияния) Одно или несколько значений смещения являются недопустимыми и не могут быть проанализированы. Элементы вставлены в конец текста | Чтобы устранить проблему, используйте сведения в сообщении об ошибке. Этот тип сбоя потребует действия для разрешения. |

<a name="could-not-execute-skill-because-the-web-api-request-failed"/>

## <a name="error-could-not-execute-skill-because-the-web-api-request-failed"></a>Ошибка: не удалось выполнить навык, так как произошел сбой запроса веб-API
Не удалось выполнить навык, так как произошел сбой при вызове веб-API. Как правило, этот класс сбоя возникает, когда используются пользовательские навыки. в этом случае для устранения проблемы потребуется отладка пользовательского кода. Если же проблема связана со встроенным навыком, обратитесь к сообщению об ошибке, чтобы получить справку по устранению проблемы.

<a name="could-not-execute-skill-because-web-api-skill-response-is-invalid"/>

## <a name="error-could-not-execute-skill-because-web-api-skill-response-is-invalid"></a>Ошибка: не удалось выполнить навык, так как отклик о навыках веб-API недопустим
Не удалось выполнить навык, так как вызов веб-API вернул недопустимый ответ. Как правило, этот класс сбоя возникает, когда используются пользовательские навыки. в этом случае для устранения проблемы потребуется отладка пользовательского кода. Если же проблема связана со встроенным навыком, отправьте [заявку в службу поддержки](https://ms.portal.azure.com/#create/Microsoft.Support) , чтобы получить помощь.

<a name="skill-did-not-execute-within-the-time-limit"/>

## <a name="error-skill-did-not-execute-within-the-time-limit"></a>Ошибка: навык не выполнялся в течение отведенного времени
Существует два варианта, при которых может возникнуть это сообщение об ошибке, каждое из которых должно обрабатываться по-разному. Следуйте приведенным ниже инструкциям в зависимости от того, какой навык возвращает эту ошибку.

### <a name="built-in-cognitive-service-skills"></a>Встроенные навыки работы со службой
Многие из встроенных функций, таких как определение языка, распознавание сущностей или распознавание текста, поддерживаются конечной точкой API-интерфейса для перекрывающегося службы. Иногда существуют временные проблемы с этими конечными точками, и время ожидания запроса истечет. Для временных проблем нет ограничений, за исключением ожидания, и повторите попытку. В качестве меры по устранению рисков рассмотрите возможность установки индексатора для [выполнения по расписанию](search-howto-schedule-indexers.md). Запланированное индексирование принимается с того места, на котором она была прервана. Если решены временные проблемы, обработка индексирования и обработки персонала должна продолжаться при следующем запланированном запуске.

Если эта ошибка продолжает отображаться в том же документе, что и встроенный навык, отправьте запрос в [службу поддержки](https://ms.portal.azure.com/#create/Microsoft.Support) , так как это не ожидается.

### <a name="custom-skills"></a>Пользовательские навыки
Если возникла ошибка времени ожидания с пользовательским навыком, который вы создали, то можете попробовать несколько вещей. Во-первых, проверьте свой пользовательский навык и убедитесь, что он не зависает в бесконечном цикле и что он возвращает результат согласованно. Убедившись в том, что это так, определите время выполнения вашего навыка. Если вы не задали явно значение `timeout` для пользовательского определения навыка, `timeout` по умолчанию составляет 30 секунд. Если 30 секунд недостаточно велик для выполнения вашего навыка, можно указать более высокое значение `timeout` для пользовательского определения навыка. Ниже приведен пример пользовательского определения навыка, в котором время ожидания равно 90 секунд:

```json
  {
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "uri": "<your custom skill uri>",
        "batchSize": 1,
        "timeout": "PT90S",
        "context": "/document",
        "inputs": [
          {
            "name": "input",
            "source": "/document/content"
          }
        ],
        "outputs": [
          {
            "name": "output",
            "targetName": "output"
          }
        ]
      }
```

Максимальное значение, которое можно задать для параметра `timeout`, — 230 секунд.  Если пользовательский навык не может выполняться постоянно в течение 230 секунд, вы можете уменьшить `batchSize` вашего пользовательского навыка, чтобы в нем было меньше документов для обработки в рамках одного выполнения.  Если вы уже установили `batchSize` в 1, вам потребуется переписать навык, чтобы он мог выполняться в течение 230 секунд или иным образом разбить его на несколько пользовательских навыков, чтобы время выполнения для любого отдельного пользовательского навыка было не более 230 секунд. Дополнительные сведения см. в [документации по пользовательской квалификации](cognitive-search-custom-skill-web-api.md) .

<a name="could-not-mergeorupload--delete-document-to-the-search-index"/>

## <a name="error-could-not-mergeorupload--delete-document-to-the-search-index"></a>Ошибка: не удалось "`MergeOrUpload`" | документ "`Delete`" в индекс поиска

Документ был прочитан и обработан, но индексатор не смог добавить его в индекс поиска. Это может произойти по следующим причинам.

| Причина | Сведения/пример | Разрешение |
| --- | --- | --- |
| Поле содержит слишком большой термин | Срок в документе превышает [ограничение в 32 КБ](search-limits-quotas-capacity.md#api-request-limits) | Это ограничение можно избежать, убедившись, что поле не настроено в качестве фильтруемого, аспекта или сортировки.
| Документ слишком велик для индексирования | Размер документа больше [максимального размера запроса API](search-limits-quotas-capacity.md#api-request-limits) | [Индексирование больших наборов данных](search-howto-large-index.md)
| Документ содержит слишком много объектов в коллекции | Коллекция в документе превышает [Максимальное число элементов в пределах всех сложных коллекций](search-limits-quotas-capacity.md#index-limits) | Рекомендуется уменьшить размер сложной коллекции в документе до предельного значения и избежать высокой загрузки хранилища.
| Не удается подключиться к целевому индексу (который сохраняется после повторных попыток), так как служба находится под другой нагрузкой, такой как запросы или индексирование. | Не удалось установить соединение с обновлением индекса. Служба поиска работает под высокой нагрузкой. | [Масштабирование службы поиска](search-capacity-planning.md)
| Служба поиска подготавливается к установке обновления службы или находится в процессе перенастройки топологии. | Не удалось установить соединение с обновлением индекса. Служба поиска в настоящее время находится в состоянии "отключена" или "служба поиска". | Настройка службы с по крайней мере 3 репликами для обеспечения доступности 99,9% на [документацию по соглашению об уровне обслуживания](https://azure.microsoft.com/support/legal/sla/search/v1_0/)
| Сбой в базовом ресурсе COMPUTE/Network (редких) | Не удалось установить соединение с обновлением индекса. Произошла неизвестная ошибка. | Настройте индексаторы для [выполнения по расписанию](search-howto-schedule-indexers.md) , чтобы выбрать состояние сбоя.
| Запрос на индексирование, выполненный в целевом индексе, не был подтвержден в течение времени ожидания из-за проблем с сетью. | Не удалось своевременно установить соединение с индексом поиска. | Настройте индексаторы для [выполнения по расписанию](search-howto-schedule-indexers.md) , чтобы выбрать состояние сбоя. Кроме того, попробуйте уменьшить [Размер пакета](https://docs.microsoft.com/rest/api/searchservice/create-indexer#parameters) индексатора в случае повторения ошибки.

<a name="could-not-index-document-because-the-indexer-data-to-index-was-invalid"/>

## <a name="error-could-not-index-document-because-the-indexer-data-to-index-was-invalid"></a>Ошибка: не удалось индексировать документ, так как данные индексатора в индексе недопустимы

Документ был прочитан и обработан, но из-за несоответствия конфигурации полей индекса и природы данных, извлеченных индексатором, его не удалось добавить в индекс поиска. Это может произойти по следующим причинам.

| Причина | Сведения/пример
| --- | ---
| Тип данных полей, извлеченных индексатором, несовместим с моделью данных соответствующего поля целевого индекса. | Поле данных "_Data_" в документе с ключом "_Data_" имеет недопустимое значение типа "EDM". строка "". Ожидался тип "Collection (EDM. String)". |
| Не удалось извлечь сущность JSON из строкового значения. | Не удалось проанализировать значение "типа" EDM. String "" поля "_Data_" как объект JSON. Ошибка: "после синтаксического анализа значения обнаружен непредвиденный символ:" ". Путь "_path_", строка 1, расположение 3162. " |
| Не удалось извлечь коллекцию сущностей JSON из строкового значения.  | Не удалось выполнить синтаксический анализ значения "типа" EDM. String "" поля "_Data_" как массива JSON. Ошибка: "после синтаксического анализа значения обнаружен непредвиденный символ:" ". Путь "[0]", строка 1, расположение 27. " |
| В исходном документе обнаружен неизвестный тип. | Неизвестный тип "_Unknown_" не может быть проиндексирован |
| В исходном документе использовалась несовместимая нотация для географических точек. | Строковые литералы точки WKT не поддерживаются. Вместо этого используйте литеральные литералы для точек в формате JSON |

Во всех этих случаях ознакомьтесь с [поддерживаемыми типами данных](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) и [сопоставлением типов данных для индексаторов](https://docs.microsoft.com/rest/api/searchservice/data-type-map-for-indexers-in-azure-search) , чтобы убедиться в правильности построения схемы индекса и настройке соответствующих [сопоставлений полей индексатора](search-indexer-field-mappings.md). В сообщении об ошибке будут содержаться сведения, которые могут помочь в отслеживании источника несоответствия.

<a name="could-not-process-document-within-indexer-max-run-time"/>

## <a name="error-could-not-process-document-within-indexer-max-run-time"></a>Ошибка: не удалось обработать документ в течение максимального времени выполнения индексатора

Эта ошибка возникает, когда индексатор не может завершить обработку одного документа из источника данных в течение допустимого времени выполнения. [Максимальное время выполнения](search-limits-quotas-capacity.md#indexer-limits) сокращается при использовании навыков. При возникновении этой ошибки, если для Максфаиледитемс задано значение, отличное от 0, индексатор обходит документ в будущих запусках, чтобы индексация могла выполняться. Если вы не можете позволить себе пропустить какой-либо документ или если эта ошибка постоянно возникает, рассмотрите возможность разбиения документов на небольшие документы, чтобы выполнить частичный ход выполнения в рамках одного индексатора.

<a name="could-not-execute-skill-because-a-skill-input-was-invalid"/>

## <a name="warning-could-not-execute-skill-because-a-skill-input-was-invalid"></a>Предупреждение: не удалось выполнить навык, так как введен недопустимый навык
Индексатору не удалось запустить навык в наборе навыков, поскольку отсутствуют входные данные для навыка, неправильный тип или недопустимый другой.

Профессиональные навыки имеют необходимые входные и необязательные входные данные. Например, [навык извлечения ключевых фраз](cognitive-search-skill-keyphrases.md) имеет два обязательных входных `text`, `languageCode`и без дополнительных входов. Если какие-либо из требуемых входных данных недопустимы, навык пропускается и выдает предупреждение. Пропущенные навыки не создают никаких выходов, поэтому, если другие навыки используют выходные данные пропущенного навыка, они могут создавать дополнительные предупреждения.

Если вы хотите предоставить значение по умолчанию в случае отсутствия входных данных, можно использовать [Условный навык](cognitive-search-skill-conditional.md) , чтобы создать значение по умолчанию, а затем использовать выходные данные [условного навыка](cognitive-search-skill-conditional.md) в качестве входного навыка.


```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document",
    "inputs": [
        { "name": "condition", "source": "= $(/document/language) == null" },
        { "name": "whenTrue", "source": "= 'en'" },
        { "name": "whenFalse", "source": "= $(/document/language)" }
    ],
    "outputs": [ { "name": "output", "targetName": "languageWithDefault" } ]
}
```

| Причина | Сведения/пример | Разрешение |
| --- | --- | --- |
| Недопустимый тип ввода навыка | Требуемый тип ввода навыка `X` не `String`ожидаемого типа. Требуемый `X` ввода навыка не находится в ожидаемом формате. | Некоторые навыки предполагают ввод определенных типов, например, [тональности навык](cognitive-search-skill-sentiment.md) ожидает, `text` быть строкой. Если входные данные указывают на нестроковое значение, то навык не выполняется и не создает выходные данные. Убедитесь, что набор данных имеет одинаковые входные значения в типе, или используйте [Пользовательский навык веб-API](cognitive-search-custom-skill-web-api.md) , чтобы предварительно обработать входные данные. Если вы выполняете итерацию навыков по массиву, проверьте контекст навыка и правильность ввода `*` на правильных позициях. Как правило, контекст и источник входных данных должны заканчиваться `*` массивов. |
| Отсутствуют входные данные о навыках | Отсутствует обязательный `X` ввода навыка. | Если все документы получают это предупреждение, скорее всего, во входных путях есть опечатка, и необходимо дважды проверить регистр имени свойства, дополнительный или отсутствующий `*` в пути, а документы из источника данных определяют необходимые входные данные. |
| Недопустимый ввод кода языка навыков | `languageCode` ввода опыта имеет следующие коды языков `X,Y,Z`, по крайней мере один из которых является недопустимым. | Дополнительные сведения см. [ниже](cognitive-search-common-errors-warnings.md#skill-input-languagecode-has-the-following-language-codes-xyz-at-least-one-of-which-is-invalid) |

<a name="skill-input-languagecode-has-the-following-language-codes-xyz-at-least-one-of-which-is-invalid"/>

## <a name="warning--skill-input-languagecode-has-the-following-language-codes-xyz-at-least-one-of-which-is-invalid"></a>Внимание! ввод навыка "languageCode" имеет следующие коды языка "X, Y, Z", по крайней мере один из которых является недопустимым.
Одно или несколько значений, переданных в необязательный `languageCode` ввода подчиненного, не поддерживаются. Это может произойти, если вы передаете выходные данные [лангуажедетектионскилл](cognitive-search-skill-language-detection.md) в последующие навыки, а выходные данные состоят из большего числа языков, чем поддерживаются в этих нижестоящих навыках.

Если известно, что набор данных находится на одном языке, следует удалить [лангуажедетектионскилл](cognitive-search-skill-language-detection.md) и `languageCode`, а также использовать параметр навыка `defaultLanguageCode` для этого навыка, предполагая, что язык поддерживается для этого навыка.

Если вы уверены, что набор данных содержит несколько языков и, следовательно, вам нужны [лангуажедетектионскилл](cognitive-search-skill-language-detection.md) и `languageCode` входные данные, рассмотрите возможность добавления [кондитионалскилл](cognitive-search-skill-conditional.md) для фильтрации текста с помощью языков, которые не поддерживаются до передачи текста в подчиненный навык.  Ниже приведен пример того, как это может выглядеть для Ентитирекогнитионскилл:

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document",
    "inputs": [
        { "name": "condition", "source": "= $(/document/language) == 'de' || $(/document/language) == 'en' || $(/document/language) == 'es' || $(/document/language) == 'fr' || $(/document/language) == 'it'" },
        { "name": "whenTrue", "source": "/document/content" },
        { "name": "whenFalse", "source": "= null" }
    ],
    "outputs": [ { "name": "output", "targetName": "supportedByEntityRecognitionSkill" } ]
}
```

Ниже приведены некоторые ссылки на языки, поддерживаемые в настоящее время для каждого из навыков, которые могут вызвать это сообщение об ошибке:
* [Анализ текста поддерживаемых языков](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages) (для [кэйфрасикстрактионскилл](cognitive-search-skill-keyphrases.md), [ентитирекогнитионскилл](cognitive-search-skill-entity-recognition.md)и [сентиментскилл](cognitive-search-skill-sentiment.md))
* [Языки, поддерживаемые переводчиками](https://docs.microsoft.com/azure/cognitive-services/translator/language-support) (для [текста транслатионскилл](cognitive-search-skill-text-translation.md))
* [Сплитскилл текста](cognitive-search-skill-textsplit.md) Поддерживаемые языки: `da, de, en, es, fi, fr, it, ko, pt`

<a name="skill-input-was-truncated"/>

## <a name="warning-skill-input-was-truncated"></a>Предупреждение: ввод навыка был усечен
Эти навыки имеют ограничения на длину текста, который можно проанализировать одновременно. Если текстовые входные данные для этих навыков превышены, мы будем усекать текст, чтобы соответствовать пределу, а затем выполнить этот Усеченный текст. Это означает, что навык выполняется, но не все данные.

В приведенном ниже примере Лангуажедетектионскилл поле ввода может активировать это предупреждение `'text'`, если оно превышает предельное число символов. Ограничения на входные данные навыков можно найти в [документации по навыкам](cognitive-search-predefined-skills.md).

```json
 {
    "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
    "inputs": [
      {
        "name": "text",
        "source": "/document/text"
      }
    ],
    "outputs": [...]
  }
```

Если вы хотите убедиться, что весь текст проанализировался, рассмотрите возможность использования функции [разбиения](cognitive-search-skill-textsplit.md).

<a name="web-api-skill-response-contains-warnings"/>

## <a name="warning-web-api-skill-response-contains-warnings"></a>Предупреждение: отклик о навыках веб-API содержит предупреждения
Индексатору удалось запустить навык в наборе навыков, но ответ от запроса веб-API сообщил о наличии предупреждений во время выполнения. Изучите предупреждения, чтобы понять, как затронутые данные и требуется ли предпринимать какие-либо действия.

<a name="the-current-indexer-configuration-does-not-support-incremental-progress"/>

## <a name="warning-the-current-indexer-configuration-does-not-support-incremental-progress"></a>Предупреждение: текущая конфигурация индексатора не поддерживает добавочный ход выполнения

Это предупреждение возникает только для источников данных Cosmos DB.

Инкрементное индексирование позволяет восстановить работу индексатора при следующем запуске с того места, в котором она была прервана из-за временных сбоев или ограничения времени выполнения, и избежать таким образом повторного индексирования всей коллекции с нуля. Это особенно важно для индексации больших коллекций.

Возможность возобновления незавершенного задания индексации выполняется с помощью предиката для документов, упорядоченных по столбцу `_ts`. Индексатор использует отметку времени, чтобы определить, какой документ следует выбрать Далее. Если столбец `_ts` отсутствует или индексатор не может определить, упорядочивается ли он с помощью пользовательского запроса, то индексатор начинается в начале, и вы увидите это предупреждение.

Это поведение можно переопределить, включив инкрементный ход выполнения и отменив это предупреждение с помощью свойства конфигурации `assumeOrderByHighWatermarkColumn`.

Дополнительные сведения см. в разделе [инкрементный прогресс и пользовательские запросы](search-howto-index-cosmosdb.md#IncrementalProgress).

<a name="some-data-was-lost-during projection-row-x-in-table-y-has-string-property-z-which-was-too-long"/>

## <a name="warning-some-data-was-lost-during-projection-row-x-in-table-y-has-string-property-z-which-was-too-long"></a>Предупреждение: некоторые данные были потеряны во время проекции. Строка "X" в таблице "Y" имеет слишком длинное строковое свойство "Z".

[Служба хранилища таблиц](https://azure.microsoft.com/services/storage/tables) имеет ограничения на то, как могут быть большие [свойства сущности](https://docs.microsoft.com/rest/api/storageservices/understanding-the-table-service-data-model#property-types) . Строки могут содержать не более 32 000 символов. Если проецируется строка со строковым свойством длиннее 32 000 символов, то сохраняются только первые 32 000 символов. Чтобы обойти эту ошибку, Избегайте проецирования строк со строковыми свойствами длиннее 32 000 символов.

<a name="truncated-extracted-text-to-x-characters"/>

## <a name="warning-truncated-extracted-text-to-x-characters"></a>Предупреждение: усеченный извлеченный текст с символами X
Индексаторы ограничивают объем текста, который можно извлечь из одного документа. Это ограничение зависит от ценовой категории: 32 000 символов для уровня Free, 64 000 для Basic и 4 000 000 для уровней Standard, Standard S2 и Standard уровня S3. Текст, который был усечен, не будет индексироваться. Чтобы избежать этого предупреждения, попробуйте разбить документы с большим объемом текста на несколько небольших документов. 

Дополнительные сведения см. в разделе [ограничения индексатора](search-limits-quotas-capacity.md#indexer-limits).

<a name="could-not-map-output-field-x-to-search-index"/>

## <a name="warning-could-not-map-output-field-x-to-search-index"></a>Предупреждение: не удалось соотнести выходное поле "X" с индексом поиска
Сопоставления полей вывода, которые ссылаются на несуществующие или нулевые данные, выдают предупреждения для каждого документа и получают пустое поле индекса. Чтобы решить эту проблему, дважды проверьте выходные данные сопоставления полей вывода для возможных опечаток или задайте значение по умолчанию, используя [Условный навык](cognitive-search-skill-conditional.md#sample-skill-definition-2-set-a-default-value-for-a-value-that-doesnt-exist).

<a name="the-data-change-detection-policy-is-configured-to-use-key-column-x"/>

## <a name="warning-the-data-change-detection-policy-is-configured-to-use-key-column-x"></a>Внимание! политика обнаружения изменений данных настроена для использования ключевого столбца "X"
[Политики обнаружения изменений данных](https://docs.microsoft.com/rest/api/searchservice/create-data-source#data-change-detection-policies) имеют определенные требования к столбцам, которые они используют для обнаружения изменений. Одно из этих требований заключается в том, что этот столбец обновляется при каждом изменении исходного элемента. Другим требованием является то, что новое значение для этого столбца больше, чем предыдущее значение. Ключевые столбцы не удовлетворяют этому требованию, так как они не меняются при каждом обновлении. Чтобы обойти эту неполадку, выберите другой столбец для политики обнаружения изменений.

<a name="document-text-appears-to-be-utf-16-encoded-but-is-missing-a-byte-order-mark"/>

## <a name="warning-document-text-appears-to-be-utf-16-encoded-but-is-missing-a-byte-order-mark"></a>Внимание! текст документа выглядит как закодированный в кодировке UTF-16, но в нем отсутствует метка порядка байтов.

[Режимы синтаксического анализа индексатора](https://docs.microsoft.com/rest/api/searchservice/create-indexer#blob-configuration-parameters) должны знать, как текст кодируется перед синтаксическим анализом. Двумя наиболее распространенными способами кодирования текста являются UTF-16 и UTF-8. UTF-8 — это кодировка переменной длины, где каждый символ имеет длину от 1 до 4 байт. UTF-16 — это кодировка фиксированной длины, в которой каждый символ имеет длину 2 байта. UTF-16 имеет два различных варианта: «с обратным порядком байтов» и «с прямым порядком байтов». Кодировка текста определяется с помощью метки порядка байтов, а также ряда байтов перед текстом.

| Сервис кодирования | Метка порядка байтов |
| --- | --- |
| UTF-16 с обратным порядком байтов | 0xFE 0xFF |
| UTF-16 с прямым порядком байтов | 0xFF 0xFE |
| UTF-8 | 0xEF 0xBB 0xBF |

Если метка порядка байтов отсутствует, предполагается, что текст кодируется как UTF-8.

Чтобы обойти это предупреждение, определите кодировку текста для этого большого двоичного объекта и добавьте соответствующую метку порядка байтов.

<a name="cosmos-db-collection-has-a-lazy-indexing-policy"/>

## <a name="warning-cosmos-db-collection-x-has-a-lazy-indexing-policy-some-data-may-be-lost"></a>Предупреждение: Cosmos DBная коллекция "X" имеет политику отложенного индексирования. Некоторые данные могут быть потеряны

Коллекции с политиками [отложенного](https://docs.microsoft.com/azure/cosmos-db/index-policy#indexing-mode) индексирования нельзя запрашивать согласованно, что приводит к отсутствующим данным в индексаторе. Чтобы обойти это предупреждение, измените политику индексирования на "постоянная".
