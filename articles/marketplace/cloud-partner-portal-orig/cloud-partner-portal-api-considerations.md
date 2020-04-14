---
title: Рассмотрение API (англ.) Лазурный рынок
description: Управление версиями, обработка ошибок и авторизации проблем благодаря использованию интерфейсов API Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: e4d4d5cb16e1037458d09f8c7681ab2d2ecf8676
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/13/2020
ms.locfileid: "81256337"
---
# <a name="api-considerations"></a>Рекомендации API

<a name="api-versioning"></a>Управление версиями API
--------------

> [!NOTE]
> AA-аДИ облачного partner Portal интегрированы с Партнерским центром и продолжат работать после того, как ваши предложения будут перенесены в Партнерский центр. Интеграция вносит небольшие изменения. Просмотрите изменения, перечисленные в [Справочнике API портала Cloud Partner,](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview) чтобы убедиться, что ваш код продолжает работать после миграции в Центр Партнеров.

Несколько версий API может бить доступно в одно и тоже время. Клиентам необходимо указать, какую версию, они будут использовать, предоставляя параметр `api-version` как часть строки запроса.

   `GET https://cloudpartner.azure.com/api/offerTypes?api-version=2017-10-31`

Ошибка HTTP с кодом 400 — это ответ на запрос неизвестной или недопустимой версии API. Эта ошибка возвращает коллекцию известных версий API в текст ответа.

``` json
    {
        "error": { 
            "code":"InvalidAPIVersion",
            "message":"Invalid api version. Allowed values are [2016-08-01-preview]"
        }
    }
```            

<a name="errors"></a>ошибки
------

API отвечает на ошибки с соответствующими кодами состояния HTTP и, необязательно, с дополнительной информацией в ответе, сериализованном как JSON.
При получении ошибки, особенно класса 400, не повторяйте запрос не исправив основную проблему. Например в ответе примера выше, перед повторной отправкой запроса, исправьте параметр версии API.

<a name="authorization-header"></a>Заголовок авторизации
--------------------

Для всех интерфейсов API в этом руководстве необходимо передать заголовок авторизации вместе с маркером носителя, полученного из Azure Active Directory (Azure AD). Этот заголовок необходим для получения допустимого ответа, если он отсутствует, ошибка `401 Unauthorized` возвращается. 

``` HTTP
  GET https://cloudpartner.azure.com/api/offerTypes?api-version=2016-08-01-preview

    Accept: application/json 
    Authorization: Bearer <YOUR_TOKEN> 
```
