---
title: Преобразование ресурса REST API
description: Описание преобразования ресурса с помощью REST API
author: florianborn71
ms.author: flborn
ms.date: 02/04/2020
ms.topic: how-to
ms.openlocfilehash: 889a70005f1cbabaad525147b4661ea04886138a
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/10/2020
ms.locfileid: "94445614"
---
# <a name="use-the-model-conversion-rest-api"></a>Использование REST API преобразования модели

Управление службой [преобразования модели](model-conversion.md) осуществляется с помощью [REST API](https://en.wikipedia.org/wiki/Representational_state_transfer). В этой статье описываются сведения об API-интерфейсе службы преобразования.

## <a name="regions"></a>Регионы

См. [список доступных регионов](../../reference/regions.md) для базовых URL-адресов, на которые отправляются запросы.

## <a name="common-headers"></a>Общие заголовки

### <a name="common-request-headers"></a>Общие заголовки запросов

Эти заголовки должны быть указаны для всех запросов:

- Заголовок **авторизации** должен иметь значение "Bearer [ *Token* ]", где [ *Token* ] является [маркером доступа к службе](../tokens.md).

### <a name="common-response-headers"></a>Общие заголовки ответов

Все ответы содержат следующие заголовки:

- Заголовок **MS-КП** содержит уникальную строку, которую можно использовать для трассировки вызова в службе.

## <a name="endpoints"></a>Конечные точки

Служба преобразования предоставляет три конечные точки REST API:

- Запустите преобразование модели с помощью учетной записи хранения, связанной с учетной записью удаленной подготовки Azure. 
- Запустите преобразование модели, используя предоставленные *подписанные URL-адрес (SAS)*.
- запрос состояния преобразования

### <a name="start-conversion-using-a-linked-storage-account"></a>Начать преобразование с помощью связанной учетной записи хранения
Учетной записи для удаленной подготовки Azure требуется доступ к указанной учетной записи хранения. для этого выполните инструкции по [связыванию учетных записей хранения](../create-an-account.md#link-storage-accounts).

| Конечная точка | Метод |
|-----------|:-----------|
| /v1/Accounts/ **accountID** /конверсионс/креате | POST |

Возвращает идентификатор текущего преобразования, заключенного в документ JSON. Имя поля — "Конверсионид".

#### <a name="request-body"></a>Тело запроса

> [!NOTE]
> Все, что находится в разделе `input.folderPath` , будет извлечено для выполнения преобразования в Azure. Если `input.folderPath` параметр не указан, будет получено содержимое контейнера целиком. Все получаемые BLOB-объекты и папки должны иметь [Допустимые имена файлов Windows](/windows/win32/fileio/naming-a-file#naming-conventions).

```json
{
    "input":
    {
        "storageAccountname": "<the name of a connected storage account - this does not include the domain suffix (.blob.core.windows.net)>",
        "blobContainerName": "<the name of the blob container containing your input asset data>",
        "folderPath": "<optional: can be omitted or empty - a subpath in the input blob container>",
        "inputAssetPath" : "<path to the model in the input blob container relative to the folderPath (or container root if no folderPath is specified)>"
    },
    "output":
    {
        "storageAccountname": "<the name of a connected storage account - this does not include the domain suffix (.blob.core.windows.net)>",
        "blobContainerName": "<the name of the blob container where the converted asset will be copied to>",
        "folderPath": "<optional: can be omitted or empty - a subpath in the output blob container. Will contain the asset and log files>",
        "outputAssetFileName": "<optional: can be omitted or empty. The filename of the converted asset. If provided the filename needs to end in .arrAsset>"
    }
}
```
### <a name="start-conversion-using-provided-shared-access-signatures"></a>Начать преобразование с помощью предоставленных подписей общего доступа
Если учетная запись ARR не связана с вашей учетной записью хранения, этот интерфейс позволяет предоставить доступ с помощью *подписанных URL-адрес (SAS)*.

| Конечная точка | Метод |
|-----------|:-----------|
| /v1/Accounts/ **accountID** /конверсионс/креатевисшаредакцесссигнатуре | POST |

Возвращает идентификатор текущего преобразования, заключенного в документ JSON. Имя поля — `conversionId` .

#### <a name="request-body"></a>Тело запроса

Текст запроса такой же, как и в вызове Create RESTFUL выше, но входные и выходные данные содержат маркеры подписанного URL *-адрес (SAS)*. Эти токены предоставляют доступ к учетной записи хранения для чтения входных данных и записи результата преобразования.

> [!NOTE]
> Эти маркеры URI SAS являются строками запросов, а не полным URI. 

> [!NOTE]
> Все, что находится в разделе `input.folderPath` , будет извлечено для выполнения преобразования в Azure. Если `input.folderPath` параметр не указан, будет получено содержимое контейнера целиком. Все получаемые BLOB-объекты и папки должны иметь [Допустимые имена файлов Windows](/windows/win32/fileio/naming-a-file#naming-conventions).

```json
{
    "input":
    {
        "storageAccountname": "<the name of a connected storage account - this does not include the domain suffix (.blob.core.windows.net)>",
        "blobContainerName": "<the name of the blob container containing your input asset data>",
        "folderPath": "<optional: can be omitted or empty - a subpath in the input blob container>",
        "inputAssetPath" : "<path to the model in the input blob container relative to the folderPath (or container root if no folderPath is specified)>",
        "containerReadListSas" : "<a container SAS token which gives read and list access to the given input blob container>"
    },
    "output":
    {
        "storageAccountname": "<the name of a connected storage account - this does not include the domain suffix (.blob.core.windows.net)>",
        "blobContainerName": "<the name of the blob container where the converted asset will be copied to>",
        "folderPath": "<optional: can be omitted or empty - a subpath in the output blob container. Will contain the asset and log files>",
        "outputAssetFileName": "<optional: can be omitted or empty. The filename of the converted asset. If provided the filename needs to end in .arrAsset>",
        "containerWriteSas" : "<a container SAS token which gives write access to the given output blob container>"
    }
}
```

### <a name="poll-conversion-status"></a>Состояние преобразования опроса
Состояние текущего преобразования, запущенного с помощью одного из приведенных выше вызовов, можно запросить с помощью следующего интерфейса:


| Конечная точка | Метод |
|-----------|:-----------|
| /v1/Accounts/ **accountID** /конверсионс/ **конверсионид** | GET |

Возвращает документ JSON со значением поля "Status", которое может иметь следующие значения:

- Создан
- Установлен
- Success
- Состояние

Если состояние имеет значение "сбой", появится дополнительное поле "ошибка" с вложенным полем "сообщение", содержащим сведения об ошибке. Дополнительные журналы будут отправлены в контейнер выходных данных.

## <a name="list-conversions"></a>Преобразования списка

Чтобы получить список всех преобразований для учетной записи, используйте интерфейс:

| Конечная точка | Метод |
|-----------|:-----------|
| /v1/Accounts/ **accountID** /конверсионс? skiptoken = **skiptoken** | GET |

| Параметр | Обязательно |
|-----------|:-----------|
| accountID | Да |
| skiptoken | Нет |

Возвращает документ JSON, содержащий массив преобразований и их сведения. Этот запрос возвращает не более 50 преобразований за раз. В ситуации, когда требуется получить дополнительные преобразования, ответ будет содержать свойство **nextLink** , содержащее skipToken, к которому можно получить запрос для получения следующего набора результатов.

## <a name="next-steps"></a>Дальнейшие действия

- [Использование хранилища BLOB-объектов Azure для преобразования модели](blob-storage.md)
- [Преобразование модели](model-conversion.md)