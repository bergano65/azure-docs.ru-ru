---
title: API операции отмены | Azure Marketplace
description: Отмена операций.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 374425dbd2abacb2114b5792d7476bc341fa353a
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73819784"
---
# <a name="cancel-operation"></a>Отмена операции 

Этот API в настоящее время отменяет операцию на предложение. Используйте [получения операций API](./cloud-partner-portal-api-retrieve-operations.md) для получения `operationId`, чтобы передать его в API. Отмена обычно выполняется в синхронной операции, однако в некоторых сложных сценариях новой операции может потребоваться отменить существующую. В этом случае в тексте ответа HTTP содержит расположение операции, который необходим для запроса состояния.

Можно указать разделенный запятыми список адресов электронной почты с запросом, и API уведомит эти адреса, о ходе выполнения операции.

  `POST https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/cancel?api-version=2017-10-31`

<a name="uri-parameters"></a>Параметры универсального кода ресурса (URI)
--------------

|  **имя**    |      **Описание**                                  |    **Тип данных**  |
| ------------ |     ----------------                                  |     -----------   |
| publisherid  |  Идентификатор издателя, например `contoso`         |   string          |
| OfferId      |  Идентификатор предложения                                     |   string          |
| api-version  |  Текущая версия клиента API                               |    Дата           |
|  |  |  |


<a name="header"></a>Заголовок
------

|  **имя**              |  **Значение**         |
|  ---------             |  ----------        |
|  Content-Type          |  приложение/json  |
|  Авторизация         |  Маркер носителя YOUR TOKEN |
|  |  |


<a name="body-example"></a>Пример текста
------------

### <a name="request"></a>Запрос

``` json
{
   "metadata": {
     "notification-emails": "jondoe@contoso.com"
    }
}     
```

### <a name="request-body-properties"></a>Свойства текста запроса

|  **имя**                |  **Описание**                                               |
|  --------                |  ---------------                                               |
|  электронные уведомления     | Список адресов электронной почты, разделенных запятыми, для получения уведомлений о ходе публикации. |
|  |  |


### <a name="response"></a>Ответ

  `Operation-Location: https://cloudpartner.azure.com/api/publishers/contoso/offers/contoso-virtualmachineoffer/operations/56615b67-2185-49fe-80d2-c4ddf77bb2e8`


### <a name="response-header"></a>Заголовок ответа

|  **имя**             |    **Значение**                       |
|  ---------            |    ----------                      |
| Operation-Location    | URL-адрес, который можно запросить для определения текущего состояния операции. |
|  |  |


### <a name="response-status-codes"></a>Коды состояния ответа

| **Код**  |  **Описание**                                                                       |
|  ------   |  ------------------------------------------------------------------------               |
|  200      | Все в порядке. Запрос успешно обработан и выполнение операции синхронно отменяется. |
|  202      | Принято. Запрос успешно обработан и происходит отмена операции. Расположение операции отмены возвращается в заголовок ответа. |
|  400      | Неправильный/недопустимый запрос. Текст ответа ошибки может предоставлять дополнительные сведения.  |
|  403      | Доступ запрещен. Клиент имеет доступ к указанному в запросе пространству имен. |
|  404      | Не найдено. Указанная сущность не существует. |
|  |  |
