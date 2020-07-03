---
title: Рекомендации по API | Azure Marketplace
description: Управление версиями, обработка ошибок и авторизации проблем благодаря использованию интерфейсов API Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: e4d4d5cb16e1037458d09f8c7681ab2d2ecf8676
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81256337"
---
# <a name="api-considerations"></a>Рекомендации API

<a name="api-versioning"></a>Управление версиями API
--------------

> [!NOTE]
> Портал Cloud Partner API интегрированы с центром партнеров и продолжат работать после переноса ваших предложений в центр партнеров. Интеграция содержит небольшие изменения. Ознакомьтесь с изменениями, приведенными в [справочнике по портал Cloud Partner API](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview) , чтобы убедиться, что код будет продолжать работать после перехода в центр партнеров.

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
