---
title: Опубликовать предложение | Azure Marketplace
description: API для публикации указанного предложения.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: b7ad8086c417cf1f14d9116fa4abcb0a88030922
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73819642"
---
<a name="publish-an-offer"></a>Публикация предложения
================

Запускает процесс публикации для указанного предложения. Этот вызов является длительной операцией.

  `POST  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/publish?api-version=2017-10-31`

<a name="uri-parameters"></a>Параметры универсального кода ресурса (URI)
--------------

|  **имя**      |    **Описание**                               |  **Тип данных** |
|  ------------- |  ------------------------------------            |   -----------  |
|  publisherid   | Идентификатор издателя, например `contoso`      |   string       |
|  OfferId       | Идентификатор предложения                                 |   string       |
|  api-version   | Последняя версия API                        |   Дата         |
|  |  |


<a name="header"></a>Заголовок
------

|  **имя**        |    **Значение**          |
|  --------        |    ---------          |
|  Content-Type    | `application/json`    |
|  Авторизация   |  `Bearer YOUR_TOKEN`  |
|  |  |


<a name="body-example"></a>Пример текста
------------

### <a name="request"></a>Запрос

``` json
  { 
      'metadata': 
          { 
              'notification-emails': 'jdoe@contoso.com'
          } 
  }
```

### <a name="request-body-properties"></a>Свойства текста запроса

|  **имя**               |   **Описание**                                                                                 |
|  ---------------------  | ------------------------------------------------------------------------------------------------- |
|  электронные уведомления    | Список адресов электронной почты, разделенных запятыми, для получения уведомлений о ходе публикации. |
|  |  |


### <a name="response"></a>Ответ

   `Operation-Location: /api/operations/contoso$56615b67-2185-49fe-80d2-c4ddf77bb2e8$2$preview?api-version=2017-10-31`


### <a name="response-header"></a>Заголовок ответа

|  **имя**             |    **Значение**                                                                 |
|  -------------------- | ---------------------------------------------------------------------------- |
| Operation-Location    | URL-адрес, который можно запросить для определения текущего состояния операции.    |
|  |  |


### <a name="response-status-codes"></a>Коды состояния ответа

| **Код** |  **Описание**                                                                                                                           |
| ------   |  ----------------------------------------------------------------------------------------------------------------------------------------- |
| 202   | `Accepted`. Запрос был успешно принят. Ответ содержит местоположение, которое можно использовать для отслеживания запускаемой операции. |
| 400   | `Bad/Malformed request`. Текст ответа ошибки может предоставлять дополнительные сведения.                                                               |
| 422   | `Un-processable entity`. Указывает, что сущность, подлежащая публикации, не прошла проверку.                                                        |
| 404   | `Not found`. Объект, указанный клиентом, не существует.                                                                              |
|  |  |
