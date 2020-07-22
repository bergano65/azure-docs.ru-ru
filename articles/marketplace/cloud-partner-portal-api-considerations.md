---
title: Рекомендации по API — Azure Marketplace
description: Управление версиями, обработка ошибок и авторизации проблем благодаря использованию интерфейсов API Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/08/2020
ms.openlocfilehash: bde55c48e8a3730727af7f3930b2a507c03e3ff3
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/08/2020
ms.locfileid: "86102724"
---
# <a name="api-considerations"></a>Рекомендации API

<a name="api-versioning"></a>Управление версиями API
--------------

> [!NOTE]
> Программные интерфейсы Портала Cloud Partner интегрированы с Центром партнеров и продолжат работать после миграции предложений в Центр партнеров. Интеграция включает небольшие изменения. Ознакомьтесь с изменениями, приведенными в [справочнике по портал Cloud Partner API](./cloud-partner-portal-api-overview.md) , чтобы убедиться, что код будет продолжать работать после перехода в центр партнеров.

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

<a name="errors"></a>Ошибки
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
