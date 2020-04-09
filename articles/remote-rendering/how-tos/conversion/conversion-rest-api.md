---
title: Преобразование активов REST API
description: Описывает, как конвертировать актив через REST API
author: florianborn71
ms.author: flborn
ms.date: 02/04/2020
ms.topic: how-to
ms.openlocfilehash: 38116efc9e87eca8e2514a0a84045a69b8d42326
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80887050"
---
# <a name="use-the-model-conversion-rest-api"></a>Использование REST API преобразования модели

Служба [преобразования моделей](model-conversion.md) контролируется через [REST API.](https://en.wikipedia.org/wiki/Representational_state_transfer) В этой статье описаны данные Службы Конверсии API.

## <a name="regions"></a>Регионы

Ознакомьтесь со [списком доступных регионов](../../reference/regions.md) для базовых URL-адресов для отправки запросов.

## <a name="common-headers"></a>Общие заголовки

### <a name="common-request-headers"></a>Общие заголовки запросов

Эти заголовки должны быть указаны для всех запросов:

- Заголовок **авторизации** должен иметь значение "Bearer и*TOKEN",* где [токен доступа](../tokens.md)*к*службе .

### <a name="common-response-headers"></a>Общие заголовки ответов

Все ответы содержат следующие заголовки:

- Заголовок **MS-CV** содержит уникальную строку, которая может быть использована для отслеживания вызова в службе.

## <a name="endpoints"></a>Конечные точки

Служба конверсии предоставляет три конечные точки REST API:

- начать преобразование модели с помощью учетной записи хранилища, связанной с учетной записью удаленного рендеринга Azure. 
- преобразование модели запуска с использованием предоставленных *общих подписей доступа (SAS).*
- запрос статуса преобразования

### <a name="start-conversion-using-a-linked-storage-account"></a>Начало конверсии с помощью связанной учетной записи хранения
Учетная запись удаленного отсчета Azure должна иметь доступ к предоставленной учетной записи хранилища, выполняя следующие шаги по учету [данных хранилища ссылки.](../create-an-account.md#link-storage-accounts)

| Конечная точка | Метод |
|-----------|:-----------|
| /v1/accounts/**accountID**accountID/conversions/create | POST |

Возвращает идентификатор текущего преобразования, завернутый в документ JSON. Название поля — «преобразование».

#### <a name="request-body"></a>Тело запроса


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
### <a name="start-conversion-using-provided-shared-access-signatures"></a>Начало конверсии с помощью предоставленных общих подписей доступа
Если ваша учетная запись ARR не связана с учетной записью хранилища, этот интерфейс REST позволяет предоставлять доступ с помощью *общих подписей доступа (SAS).*

| Конечная точка | Метод |
|-----------|:-----------|
| /v1/accounts/**accountID**accountID/conversions/createWithSharedAccessSignature | POST |

Возвращает идентификатор текущего преобразования, завернутый в документ JSON. Название поля — «преобразование».

#### <a name="request-body"></a>Тело запроса

Тело запроса такое же, как и в приведенном выше вызове REST, но ввода и вывод содержат *маркеры общих подписей доступа (SAS).* Эти токены обеспечивают доступ к учетной записи хранилища для чтения входных данных и записи результата преобразования.

> [!NOTE]
> Эти токены SAS URI являются строками запроса, а не полным URI. 


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

### <a name="poll-conversion-status"></a>Статус конверсии опроса
Состояние текущего преобразования, начатого с одного из вышеуказанных вызовов REST, можно позапросу с помощью следующего интерфейса:


| Конечная точка | Метод |
|-----------|:-----------|
| /v1/accounts/**accountID/conversions/****conversionId** | GET |

Возвращает документ JSON с полем "статус", которое может иметь следующие значения:

- "Бегущий"
- Success
- "Неудача"

Если статус "Отказ", будет дополнительное поле "ошибка" с подполем "сообщение", содержащим информацию об ошибках. Дополнительные журналы будут загружены в ваш выходной контейнер.

## <a name="next-steps"></a>Дальнейшие действия

- [Использование хранилища BLOB-объектов Azure для преобразования модели](blob-storage.md)
- [Преобразование модели](model-conversion.md)
