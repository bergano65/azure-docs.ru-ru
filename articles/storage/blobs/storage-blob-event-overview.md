---
title: Реагирование на события хранилища BLOB-объектов Azure | Документация Майкрософт
description: Используйте службу "Сетка событий Azure" для подписки на события хранилища BLOB-объектов.
author: normesta
ms.author: normesta
ms.date: 01/30/2018
ms.topic: conceptual
ms.service: storage
ms.subservice: blobs
ms.reviewer: cbrooks
ms.openlocfilehash: b813ef89bb1a55f769d0ea2391855ba5d671c140
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/20/2019
ms.locfileid: "69648799"
---
# <a name="reacting-to-blob-storage-events"></a>Реагирование на события хранилища BLOB-объектов

События службы хранилища Azure позволяют приложениям реагировать на события, такие как создание и удаление больших двоичных объектов, с использованием современных бессерверных архитектур. При этом не требуется сложный код или дорогостоящие и неэффективные службы опроса.

Вместо этого события отправляются через службу " [Сетка событий Azure](https://azure.microsoft.com/services/event-grid/) " на такие подписчики, как функции azure, Azure Logic Apps или даже на собственный настраиваемый прослушиватель HTTP, и вы платите только за то, что используете.

События хранилища BLOB-объектов надежно отправляются в службу "Сетка событий", которая обеспечивает надежную работу служб доставки для приложений с помощью многофункциональных политик повтора и доставки недоставленных сообщений.

К распространенным сценариям событий хранилища BLOB-объектов относятся обработка изображений и видео, индексирование поиска и любые рабочие процессы, ориентированные на файлы. Асинхронная передача файлов идеально подходит для событий. Когда изменения вносятся редко, а сценарий требует немедленного реагирования, архитектура на основе событий может быть особенно эффективной.

Если вы хотите испытать эту возможность, ознакомьтесь с любой из следующих кратких руководств:

|Если вы хотите использовать это средство:    |См. следующую статью: |
|--|-|
|портала Azure    |[Краткое руководство Перенаправление событий хранилища BLOB-объектов в конечную точку в Интернете с помощью портал Azure](https://docs.microsoft.com/azure/event-grid/blob-event-quickstart-portal?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|PowerShell    |[Краткое руководство Маршрутизация событий хранилища в конечную точку веб-сайта с помощью PowerShell](https://docs.microsoft.com/azure/storage/blobs/storage-blob-event-quickstart-powershell?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Azure CLI    |[Краткое руководство Маршрутизация событий хранилища в конечную точку веб-сайта с Azure CLI](https://docs.microsoft.com/azure/storage/blobs/storage-blob-event-quickstart?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|

Если у вашей учетной записи есть иерархическое пространство имен, в этом учебнике показано, как подключить к ней подписку на службу "Сетка событий", функцию Azure и [Задание](https://docs.azuredatabricks.net/user-guide/jobs.html) в Azure Databricks: [Руководство. Используйте события Azure Data Lake Storage 2-го поколения для обновления разностной таблицы](data-lake-storage-events.md)кирпичей данных.

## <a name="the-event-model"></a>Модель событий

Сетка событий использует [подписки на события](../../event-grid/concepts.md#event-subscriptions) для маршрутизации сообщений о событиях подписчикам. На этом рисунке показана связь между издателями событий, подписками на события и обработчиками событий.

![Модель Сетки событий](./media/storage-blob-event-overview/event-grid-functional-model.png)

Сначала подпишите конечную точку на событие. Затем при активации события служба "Сетка событий" отправит данные об этом событии в конечную точку.

См. статью о [схеме событий хранилища BLOB-объектов](../../event-grid/event-schema-blob-storage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) для просмотра:

> [!div class="checklist"]
> * Полный список событий хранилища BLOB-объектов и процедура запуска каждого события.
> * Пример данных, которые сетка событий будет передавать для каждого из этих событий.
> * Назначение каждой пары «ключ — значение», отображаемой в данных.

## <a name="filtering-events"></a>Фильтрация событий

Подписки на события больших двоичных объектов можно фильтровать на основе типа события, а также по имени контейнера и имени созданного или удаленного большого двоичного объекта.  Фильтры могут применяться к подпискам на события во время [создания](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest) подписки на событие или [позже](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest). Фильтры темы в службе "Сетка событий" работают на основе совпадений "начинается с" и "заканчивается на", что позволяет доставлять подписчику события с соответствующей темой.

Дополнительные сведения о применении фильтров см. в разделе [Фильтрация событий для сетки событий](https://docs.microsoft.com/azure/event-grid/how-to-filter-events).

Тема событий хранилища BLOB-объектов использует следующий формат:

```
/blobServices/default/containers/<containername>/blobs/<blobname>
```

Чтобы сопоставить все события для учетной записи хранения, фильтры темы можно оставить пустыми.

Чтобы сопоставить события из больших двоичных объектов, созданных в наборе контейнеров с общим префиксом, используйте фильтр `subjectBeginsWith`, подобный этому:

```
/blobServices/default/containers/containerprefix
```

Чтобы сопоставить события из больших двоичных объектов, созданных в конкретном контейнере, используйте фильтр `subjectBeginsWith`, подобный этому:

```
/blobServices/default/containers/containername/
```

Чтобы сопоставить события из больших двоичных объектов, созданных в конкретном контейнере с общим префиксом имени большого двоичного объекта, используйте фильтр `subjectBeginsWith`, подобный этому:

```
/blobServices/default/containers/containername/blobs/blobprefix
```

Чтобы сопоставить события из больших двоичных объектов, созданных в конкретном контейнере с общим суффиксом имени большого двоичного объекта, используйте фильтр `subjectEndsWith`, такой как ".log" или ".jpg". Дополнительные сведения см. в статье [Основные понятия в службе "Сетка событий Azure"](../../event-grid/concepts.md#event-subscriptions).

## <a name="practices-for-consuming-events"></a>Рекомендации по потреблению событий

Приложения, которые обрабатывают события хранилища BLOB-объектов, должны следовать нескольким рекомендациям:
> [!div class="checklist"]
> * Так как в один обработчик событий можно настроить маршрутизацию событий из нескольких подписок, не следует предполагать, что события поступают из определенного источника. Но необходимо проверять тему сообщения, чтобы убедиться, что оно поступает из ожидаемой учетной записи хранения.
> * Подобным образом убедитесь, что вы готовы к обработке этого типа событий (eventType), а также не следует предполагать, что все получаемые события будут иметь ожидаемый тип.
> * Так как сообщения могут поступать не по порядку и после некоторой задержки, используйте поля ETag, чтобы понять, являются ли сведения об объектах актуальными.  Используйте поля sequencer для понимания последовательности событий для каждого отдельного объекта.
> * Используйте поле blobType чтобы понять, какой тип операций разрешен в большом двоичном объекте и какие типы клиентских библиотек следует использовать для доступа к большому двоичному объекту. Допустимые значения: `BlockBlob` или `PageBlob`. 
> * Для доступа к большому двоичному объекту используйте поле url с конструкторами `CloudBlockBlob` и `CloudAppendBlob`.
> * Игнорируйте поля, которые вам непонятны. Такой подход поможет обеспечить устойчивость к новым функциям, которые могут быть добавлены в будущем.
> * Если необходимо, чтобы событие **Microsoft. Storage. BlobCreated** вызывалось только при полной фиксации блочного большого двоичного объекта, отфильтруйте событие для `CopyBlob`вызовов REST API, `PutBlockList` `PutBlob`или `FlushWithClose` . Эти вызовы API активируют событие **Microsoft. Storage. BlobCreated** только после полной фиксации данных в блочном BLOB-объекте. Сведения о создании фильтра см. в разделе [Фильтрация событий для сетки событий](https://docs.microsoft.com/azure/event-grid/how-to-filter-events).


## <a name="next-steps"></a>Следующие шаги

Ознакомьтесь с дополнительными сведениями о Сетке событий и попробуйте использовать события хранилища BLOB-объектов:

- [An introduction to Azure Event Grid](../../event-grid/overview.md) (Общие сведения о службе "Сетка событий Azure")
- [Перенаправление событий хранилища BLOB-объектов в пользовательскую конечную веб-точку (предварительная версия)](storage-blob-event-quickstart.md)
