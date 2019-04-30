---
title: Публикация предложения | Документация Майкрософт
description: API для публикации указанного предложения.
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
ms.openlocfilehash: cb1293a771a137f4df7e36a2b412f68b384f16ef
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "61094372"
---
<a name="publish-an-offer"></a>Публикация предложения
================

Запускает процесс публикации для указанного предложения. Этот вызов является длительной операцией.

  `POST  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/publish?api-version=2017-10-31`

<a name="uri-parameters"></a>Параметры универсального кода ресурса (URI)
--------------

|  **Имя**      |    **Описание**                               |  **Тип данных** |
|  ------------- |  ------------------------------------            |   -----------  |
|  publisherId   | Идентификатор издателя, например `contoso`      |   String       |
|  offerId       | Идентификатор предложения                                 |   String       |
|  api-version   | Последняя версия API                        |   Дата         |
|  |  |


<a name="header"></a>Верхний колонтитул
------

|  **Имя**        |    **Значение**          |
|  --------        |    ---------          |
|  Content-Type    | `application/json`    |
|  Авторизация   |  `Bearer YOUR_TOKEN`  |
|  |  |


<a name="body-example"></a>Пример текста запроса
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

|  **Имя**               |   **Описание**                                                                                 |
|  ---------------------  | ------------------------------------------------------------------------------------------------- |
|  электронные уведомления    | Список адресов электронной почты, разделенных запятыми, для получения уведомлений о ходе публикации. |
|  |  |


### <a name="response"></a>Отклик

   `Operation-Location: /api/operations/contoso$56615b67-2185-49fe-80d2-c4ddf77bb2e8$2$preview?api-version=2017-10-31`


### <a name="response-header"></a>Заголовок ответа

|  **Имя**             |    **Значение**                                                                 |
|  -------------------- | ---------------------------------------------------------------------------- |
| Operation-Location    | URL-адрес, который можно запросить для определения текущего состояния операции.    |
|  |  |


### <a name="response-status-codes"></a>Коды состояния ответа

| **Код** |  **Описание**                                                                                                                           |
| ------   |  ----------------------------------------------------------------------------------------------------------------------------------------- |
| 202   | `Accepted` — запрос был успешно принят. Ответ содержит местоположение, которое можно использовать для отслеживания запускаемой операции. |
| 400   | `Bad/Malformed request`. Текст ответа ошибки может предоставлять дополнительные сведения.                                                               |
| 422   | `Un-processable entity`. Указывает, что сущность, подлежащая публикации, не прошла проверку.                                                        |
| 404   | `Not found`. Объект, указанный клиентом, не существует.                                                                              |
|  |  |
