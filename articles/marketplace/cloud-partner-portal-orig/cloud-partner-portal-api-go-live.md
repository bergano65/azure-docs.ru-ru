---
title: Функционирование | Azure Marketplace
description: API запуска инициирует динамический перечень процессов предложения.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: reference
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: ac56f86bad132f3e00a4b5c2507d65c0722c628c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "64935493"
---
<a name="go-live"></a>Запуск
=======

Этот API запускает процесс для принудительной отправки приложения рабочей среде. Выполнение этой операции занимает много времени. Этот вызов использует список уведомлений по электронной почте из [публикации](./cloud-partner-portal-api-publish-offer.md) операции API.

 `POST  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/golive?api-version=2017-10-31` 

<a name="uri-parameters"></a>Параметры универсального кода ресурса (URI)
--------------

|  **Имя**      |   **Описание**                                                           | **Тип данных** |
|  --------      |   ---------------                                                           | ------------- |
| publisherid    | Идентификатор издателя для извлечения предложения, например `contoso`.       |  String       |
| offerId        | Идентификатор извлекаемой записи предложения.                                   |  String       |
| api-version    | Последняя версия API                                                   |  Дата         |
|  |  |  |


<a name="header"></a>Верхний колонтитул
------

|  **Имя**       |     **Значение**       |
|  ---------      |     ----------      |
| Content-Type    | `application/json`  |
| Авторизация   | `Bearer YOUR_TOKEN` |
|  |  |


<a name="body-example"></a>Пример текста запроса
------------

### <a name="response"></a>Ответ

`Operation-Location: https://cloudpartner.azure.com/api/publishers/contoso/offers/contoso-virtualmachineoffer/operations/56615b67-2185-49fe-80d2-c4ddf77bb2e8`


### <a name="response-header"></a>Заголовок ответа

|  **Имя**             |      **Значение**                                                            |
|  --------             |      ----------                                                           |
| Operation-Location    |  URL-адрес, который можно запросить для определения текущего состояния операции.            |
|  |  |


### <a name="response-status-codes"></a>Коды состояния ответа

| **Код** |  **Описание**                                                                        |
| -------- |  ----------------                                                                        |
|  202     | `Accepted` — запрос был успешно принят. Ответ содержит расположение для отслеживания состояния операции. |
|  400     | `Bad/Malformed request` — дополнительные сведения об ошибке находятся в тексте ответа. |
|  404     |  `Not found` — указанная сущность не существует.                                       |
|  |  |
