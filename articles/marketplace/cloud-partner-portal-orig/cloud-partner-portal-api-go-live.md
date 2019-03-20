---
title: Запуск | Документация Майкрософт
description: API запуска инициирует динамический перечень процессов предложения.
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
ms.openlocfilehash: ea3f26d70c4a4ce07c988612890687504a4cf5ac
ms.sourcegitcommit: a8948ddcbaaa22bccbb6f187b20720eba7a17edc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/21/2019
ms.locfileid: "56594684"
---
<a name="go-live"></a>Запуск
=======

Этот API запускает процесс для принудительной отправки приложения рабочей среде. Выполнение этой операции занимает много времени. Этот вызов использует список уведомлений по электронной почте из [публикации](./cloud-partner-portal-api-publish-offer.md) операции API.

 `POST  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/golive?api-version=2017-10-31` 

<a name="uri-parameters"></a>Параметры универсального кода ресурса (URI)
--------------

|  **Имя**      |   **Описание**                                                           | **Тип данных** |
|  --------      |   ---------------                                                           | ------------- |
| publisherid    | Идентификатор издателя для извлечения предложения, например `contoso`.       |  Строка       |
| offerId        | Идентификатор извлекаемой записи предложения.                                   |  Строка       |
| api-version    | Последняя версия API                                                   |  Дата         |
|  |  |  |


<a name="header"></a>Заголовок
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
