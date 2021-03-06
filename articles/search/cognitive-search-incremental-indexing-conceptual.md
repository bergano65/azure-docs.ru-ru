---
title: Добавочное индексирование (Предварительная версия)
titleSuffix: Azure Cognitive Search
description: Настройте конвейер обогащения искусственного интеллекта для обеспечения согласованности данных для обработки любых обновлений в навыках, навыков, индексаторах или источниках данных. Сейчас эта функция доступна в общедоступной предварительной версии
manager: nitinme
author: Vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: c44228d7e1456bce870765935beb011cb24626d5
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74790930"
---
# <a name="what-is-incremental-indexing-in-azure-cognitive-search"></a>Что такое добавочное индексирование в Azure Когнитивный поиск?

> [!IMPORTANT] 
> Добавочное индексирование в настоящее время находится в общедоступной предварительной версии. Эта предварительная версия предоставляется без соглашения об уровне обслуживания и не рекомендована для использования рабочей среде. Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Эта функция предоставляется в [версии REST API 2019-05-06-Preview](search-api-preview.md). В настоящее время отсутствует поддержка портала или пакета SDK для .NET.

Добавочное индексирование — это новая функция Когнитивный поиск Azure, которая добавляет кэширование и состояние в обогащенный набор квалификационных навыков, позволяя управлять обработкой и повторной обработкой отдельных шагов в конвейере обогащения. Это позволяет не только сохранить денежные инвестиции в обработку, но и повысить эффективность работы системы. При кэшировании структур и содержимого индексатор может определить, какие навыки были изменены, и выполнить только те из них, которые были изменены, а также все нисходящие зависимые навыки. 

При использовании добавочного индексирования текущая версия конвейера обогащения выполняет минимально необходимый объем работы для поддержания согласованности всех документов в индексе. Для сценариев, в которых требуется полный доступ, можно использовать детализированные элементы управления для переопределения ожидаемого поведения. Дополнительные сведения о конфигурации см. в разделе [Настройка добавочного индексирования](search-howto-incremental-index.md).

## <a name="indexer-cache"></a>Кэш индексатора

Добавочное индексирование добавляет в конвейер обогащения кэш индексатора. Индексатор кэширует результаты распознавания документов и выходные данные по каждому навыку для каждого документа. При обновлении набора знаний повторно запускаются только измененные навыки и те, которые выполняются после них. Обновленные результаты сохраняются в кэш, а документ обновляется в индексе и в хранилище знаний.

Физически этот кэш реализован в учетной записи хранения. Все индексы в службе поиска могут использовать для кэша индексатора одну учетную запись хранения. Каждому индексатору назначается уникальный и неизменяемый идентификатор кэша.

### <a name="cache-configuration"></a>Конфигурация кэша

Для запуска выигрыша из инкрементного индексирования необходимо задать свойство `cache` в индексаторе. В следующем примере показан индексатор с включенным кэшированием. Конкретные части этой конфигурации описаны в следующих разделах.

```json
{
    "name": "myIndexerName",
    "targetIndexName": "myIndex",
    "dataSourceName": "myDatasource",
    "skillsetName": "mySkillset",
    "cache" : {
        "storageConnectionString" : "Your storage account connection string",
        "enableReprocessing": true,
        "id" : "Auto generated Id you do not need to set"
    },
    "fieldMappings" : [],
    "outputFieldMappings": [],
    "parameters": {}
}
```

Если установить это свойство в первый раз для существующего индексатора, потребуется также сбросить его, что приведет к повторной обработке всех документов в источнике данных. Цель добавочного индексирования заключается в том, чтобы обеспечивать для документов итоговую согласованность с текущим состоянием источника данных и набора навыков. Сброс индекса является первым шагом к достижению согласованности, так как это позволяет избавиться от всех документов, обогащенных старыми версиями набора навыков. Индексатор необходимо сбросить, чтобы начать работу с согласованными ресурсами.

### <a name="cache-lifecycle"></a>Жизненный цикл кэша

Жизненным циклом кэша управляет индексатор. Если свойству `cache` в индексаторе присвоено значение null или изменена строка соединения, существующий кэш будет удален. Кроме того, жизненный цикл кэша привязан к жизненному циклу индексатора. При удалении индексатора удаляется и связанный с ним кэш.

### <a name="indexer-cache-mode"></a>Режим кэша индексатора

Кэш индексатора может действовать в разных режимах: данные только записываются в кэш или одновременно записываются в кэш и применяются для повторного обогащения документов.  Вы можете временно приостановить добавочное обогащение, задав для свойства `enableReprocessing` в кэше значение `false`, а позднее возобновить добавочное обогащение и потребовать итоговую согласованность, задав значение `true`. Этот элемент управления особенно удобно использовать, если индексирование новых документов должно иметь более высокий приоритет, чем согласованность всей совокупности документов.

## <a name="change-detection-override"></a>Переопределение метода обнаружения изменений

Добавочное индексирование дает обширный контроль над всеми элементами конвейера обогащения. Этот элемент управления позволяет справиться с ситуациями, в которых изменение может привести к непредвиденным последствиям. Например, изменение URL-адреса пользовательского навыка в наборе навыков приведет к тому, что индексатор сочтет недействительными все результаты, собранные в кэше для этого навыка. Если же вы просто перемещаете конечную точку на другую виртуальную машину или повторно развертываете этот навык с другим ключом доступа, повторно обрабатывать существующие документы смысла нет.

Чтобы в индексаторе были только явно нужные дополнения, обновления в наборе навыков могут при необходимости задать для параметра `disableCacheReprocessingChangeDetection` QueryString значение `true`. При такой настройке параметра будут зафиксированы только изменения набора навыков, без оценки влияния этих изменений на существующий набор данных.

В следующем примере демонстрируется использование строки запроса. Это часть запроса, в которой есть пары "ключ-значение", разделенные &. 

```http
PUT https://customerdemos.search.windows.net/skillsets/callcenter-text-skillset?api-version=2019-05-06-Preview&disableCacheReprocessingChangeDetection=true
```

## <a name="cache-invalidation"></a>Недействительность кэша

Может возникнуть и обратная ситуация, когда вы развертываете новую версию пользовательского навыка без изменений в конвейере обогащения, но при этом вам нужно объявить некоторый навык недействительным и повторно обработать все затронутые документы с учетом обновленных характеристик модели. В таких случаях можно вызвать для набора навыков операцию, которая объявляет навыки недействительными. API сброса навыков принимает запрос POST со списком выходных данных навыков, которые должны считаться недействительными в кэше. Дополнительные сведения об API сброса навыков см. в разделе [Сброс индексатора (поиск REST API)](https://docs.microsoft.com/rest/api/searchservice/reset-indexer).

## <a name="bi-directional-change-detection"></a>Двунаправленное обнаружение изменений

Индексаторы теперь могут перемещаться не только вперед, обрабатывая новые документы, но и назад, поддерживая согласованность уже обработанных документов. Эта новая возможность требует понимания того, как повлияют изменения в компонентах конвейера обогащения на работу индексатора. Индексатор будет ставить в очередь работу, которая будет выполняться при обнаружении изменений, которые либо недействительны, либо не согласуются с кэшированным содержимым.

### <a name="invalidating-changes"></a>Изменения, приводящие к недействительности

Изменения, приводящие к недействительности, происходят редко, но оказывают значительное воздействие на состояние конвейера обогащения. К таким изменениям относятся такие действия, после которых весь кэш становится недействительным. Например, приводящим к недействительности изменением является обновление источника данных. Для сценариев, когда известно, что изменение не должно сделать кэш недействительным, например, при смене ключа в учетной записи хранения, параметру `ignoreResetRequirement` QueryString должно быть присвоено значение `true` операции обновления конкретного ресурса, чтобы гарантировать, что операция не отклоняется.

Вот полный список изменений, которые делают кэш недействительным:

* изменение типа внешнего источника данных;
* изменение контейнера источника данных;
* учетные данные источника данных;
* политика обнаружения изменений в источнике данных;
* политика обнаружения удалений в источнике данных;
* сопоставления полей индексатора;
* Параметры индексатора
    * режим анализа;
    * исключаемые расширения имен файлов;
    * индексируемые расширения имен файлов;
    * индексация метаданных хранилища только для очень больших документов;
    * заголовки текста с разделителями;
    * разделители текста с разделителями;
    * корневой каталог документов;
    * действие с изображением (изменения в способе извлечения изображений).

### <a name="inconsistent-changes"></a>Действия, приводящие к несогласованности.

Примером изменений, приводящих к несогласованности, является изменение навыка в наборе навыков. Такое изменение может привести к несогласованности частей кэша. Индексатор определит, какую работу ему нужно выполнить для восстановления согласованности.  

Вот полный список изменений, приводящих к несогласованности кэша:

* другой тип навыка в наборе навыков; изменение типа OData для навыка;
* обновление параметров конкретного навыка, например его URL-адреса, значений по умолчанию или других параметров;
* изменение выходных данных навыков, в том числе их наличие дополнительных или других выходных данных;
* изменения в навыках, изменяющие их родственные отношения и цепочки зависимостей, например: входные данные навыков
* недействительность любого навыка на уровне выше, если он предоставляет входные данные для этого навыка;
* изменение расположения для проекции хранилища знаний, которое приводит к повторному проецированию документов;
* изменение проекций хранилища знаний, которое приводит к повторному проецированию документов;
* изменение сопоставления выходных полей в индексаторе, которое приводит к повторному проецированию документов в индекс.

## <a name="rest-api-reference-for-incremental-indexing"></a>REST API ссылка для добавочного индексирования

Функции RESTFUL `api-version=2019-05-06-Preview` предоставляют API для добавочного индексирования с дополнениями к индексаторам, навыков и источникам данных. Справочная документация в настоящее время не включает эти дополнения. В следующем разделе описываются изменения API.

### <a name="indexers"></a>Индексаторы

[Создание индексатора](https://docs.microsoft.com/rest/api/searchservice/create-indexer) и [индексатора обновления](https://docs.microsoft.com/rest/api/searchservice/update-indexer) теперь будет предоставлять новые свойства, связанные с кэшем:

* `StorageAccountConnectionString`: строка подключения к учетной записи хранения, которая будет использоваться для кэширования промежуточных результатов.

* `CacheId`. `cacheId` — это идентификатор контейнера в учетной записи хранения `annotationCache`, который будет использоваться в качестве кэша для этого индексатора. Этот кэш будет уникальным для этого индексатора. Значение `cacheId` восстанавливается, если вы удалите индексатор и повторно создадите с тем же именем. Вы не можете задать значение для `cacheId` — оно всегда определяется службой.

* `EnableReprocessing`: по умолчанию задано значение `true`. Если задано значение `false`, документы будут по прежнему записываться в кэш, но существующие документы не будут повторно обрабатываться на основе данных кэша.

Некоторые индексаторы (через [Источники данных](https://docs.microsoft.com/rest/api/searchservice/create-data-source)) получают данные через запросы. Для запросов, извлекающих данные, индексаторы также поддерживают новый параметр строки запроса: `ignoreResetRequirement` должны иметь значение `true` если действие обновления не должно сделать кэш недействительным.

### <a name="skillsets"></a>Наборы навыков

Для наборов навыков не добавилось новых операций, но они будут поддерживать новый параметр строки запроса: `disableCacheReprocessingChangeDetection` должен иметь значение `true`, если вы не хотите обновлять существующие документы по результатам выполняемого действия.

### <a name="datasources"></a>Источники данных

Для источников данных не добавилось новых операций, но они будут поддерживать новый параметр строки запроса: `ignoreResetRequirement` должен иметь значение `true`, если вы не хотите сделать кэш недействительным при операции обновления.

## <a name="best-practices"></a>Рекомендации

Мы рекомендуем использовать добавочное индексирование, указав свойство cache для нового индексатора или выполнив сброс для существующего индексатора и присвоив ему свойство cache.

Используйте `ignoreResetRequirement` экономно, так как это может привести к непредвиденному несогласованности данных, которые не будут обнаружены легко.

## <a name="takeaways"></a>Общие выводы

Добавочное индексирование — это мощная функция, которая расширяет отслеживание изменений от источника данных до всех аспектов конвейера обогащения, включая источник данных, текущую версию набора навыков и индексатор. По мере развития навыков, наборов навыков и обогащений конвейер обогащения старается с минимально возможными усилиями поддерживать согласованность итоговую документов.

## <a name="next-steps"></a>Дальнейшие действия

Начните работу с добавочным индексированием, добавив кэш к существующему индексатору или настроив кэша при определении нового индексатора.

> [!div class="nextstepaction"]
> [Настройка добавочного индексирования](search-howto-incremental-index.md)
