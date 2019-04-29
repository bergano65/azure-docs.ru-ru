---
title: Отмена операции API | Документация Майкрософт
description: Отмена операций.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: reference
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 18f00391beded0744c80eab73bb1efe1c6ab8dbc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60625046"
---
<a name="cancel-operation"></a>Отмена операции 
=================

Этот API в настоящее время отменяет операцию на предложение. Используйте [получения операций API](./cloud-partner-portal-api-retrieve-operations.md) для получения `operationId`, чтобы передать его в API. Отмена обычно выполняется в синхронной операции, однако в некоторых сложных сценариях новой операции может потребоваться отменить существующую. В этом случае в тексте ответа HTTP содержит расположение операции, который необходим для запроса состояния.

Можно указать разделенный запятыми список адресов электронной почты с запросом, и API уведомит эти адреса, о ходе выполнения операции.

  `POST https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/cancel?api-version=2017-10-31`

<a name="uri-parameters"></a>Параметры универсального кода ресурса (URI)
--------------

|  **Имя**    |      **Описание**                                  |    **Тип данных**  |
| ------------ |     ----------------                                  |     -----------   |
| publisherid  |  Идентификатор издателя, например `contoso`         |   String          |
| offerId      |  Идентификатор предложения                                     |   String          |
| api-version  |  Текущая версия клиента API                               |    Дата           |
|  |  |  |


<a name="header"></a>Верхний колонтитул
------

|  **Имя**              |  **Значение**         |
|  ---------             |  ----------        |
|  Content-Type          |  приложение/json  |
|  Авторизация         |  Маркер носителя YOUR TOKEN |
|  |  |


<a name="body-example"></a>Пример текста запроса
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

|  **Имя**                |  **Описание**                                               |
|  --------                |  ---------------                                               |
|  электронные уведомления     | Список адресов электронной почты, разделенных запятыми, для получения уведомлений о ходе публикации. |
|  |  |


### <a name="response"></a>Отклик

  `Operation-Location: https://cloudpartner.azure.com/api/publishers/contoso/offers/contoso-virtualmachineoffer/operations/56615b67-2185-49fe-80d2-c4ddf77bb2e8`


### <a name="response-header"></a>Заголовок ответа

|  **Имя**             |    **Значение**                       |
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
