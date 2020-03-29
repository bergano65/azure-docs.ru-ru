---
title: Конфигурация - Azure Event Grid IoT Edge (ru) Документы Майкрософт
description: Конфигурация в Event Grid на IoT Edge.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/03/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 841b5092775353bbe3340dbbd55610026f998a15
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76846470"
---
# <a name="event-grid-configuration"></a>Конфигурация сетки событий

Event Grid предоставляет множество конфигураций, которые могут быть изменены в среде. Следующий раздел является ссылкой на все доступные варианты и их по умолчанию.

## <a name="tls-configuration"></a>Конфигурация TLS

Чтобы узнать о проверке подлинности клиента в целом, [см.](security-authentication.md) Примеры его использования можно найти в [этой статье](configure-api-protocol.md).

| Имя свойства | Описание |
| ---------------- | ------------ |
|`inbound__serverAuth__tlsPolicy`| Политика TLS модуля Event Grid. Значение по умолчанию только HTTPS.
|`inbound__serverAuth__serverCert__source`| Источник серверного сертификата, используемого модулем Event Grid для его конфигурации TLS. Значение по умолчанию — это IoT Edge.

## <a name="incoming-client-authentication"></a>Входящие проверки подлинности клиента

Чтобы узнать о проверке подлинности клиента в целом, [см.](security-authentication.md) Примеры можно найти в [этой статье](configure-client-auth.md).

| Имя свойства | Описание |
| ---------------- | ------------ |
|`inbound__clientAuth__clientCert__enabled`| Включить/выключить проверку подлинности клиента на основе сертификата. Значение по умолчанию — true.
|`inbound__clientAuth__clientCert__source`| Источник проверки сертификатов клиента. Значение по умолчанию — это IoT Edge.
|`inbound__clientAuth__clientCert__allowUnknownCA`| Политика, позволяющая самостоятельно подписать сертификат клиента. Значение по умолчанию — true.
|`inbound__clientAuth__sasKeys__enabled`| Включить/выключить проверку подлинности клиента на основе клавиш SAS. Значение по умолчанию выключено.
|`inbound__clientAuth__sasKeys__key1`| Одно из значений для проверки входящих запросов.
|`inbound__clientAuth__sasKeys__key2`| Дополнительное второе значение для проверки входящих запросов.

## <a name="outgoing-client-authentication"></a>Исходящие проверки подлинности клиента
Чтобы узнать о проверке подлинности клиента в целом, [см.](security-authentication.md) Примеры можно найти в [этой статье](configure-identity-auth.md).

| Имя свойства | Описание |
| ---------------- | ------------ |
|`outbound__clientAuth__clientCert__enabled`| Включить/выключить присоединение сертификата идентификации для исходящих запросов. Значение по умолчанию — true.
|`outbound__clientAuth__clientCert__source`| Источник для получения исходящего сертификата модуля Event Grid. Значение по умолчанию — это IoT Edge.

## <a name="webhook-event-handlers"></a>Обработчики событий Webhook

Чтобы узнать о проверке подлинности клиента в целом, [см.](security-authentication.md) Примеры можно найти в [этой статье](configure-webhook-subscriber-auth.md).

| Имя свойства | Описание |
| ---------------- | ------------ |
|`outbound__webhook__httpsOnly`| Политика для контроля, будут ли разрешены только абоненты HTTPS. Значение по умолчанию верно (только HTTPS).
|`outbound__webhook__skipServerCertValidation`| Пометить, чтобы проверить, следует ли проверить сертификат абонента. Значение по умолчанию — true.
|`outbound__webhook__allowUnknownCA`| Политика для контроля того, может ли самоподписанный сертификат быть представлен абонентом. Значение по умолчанию — true. 

## <a name="delivery-and-retry"></a>Доставка и повторные попытки доставки

Чтобы узнать об этой функции в целом, см [Доставка и Retry](delivery-retry.md).

| Имя свойства | Описание |
| ---------------- | ------------ |
| `broker__defaultMaxDeliveryAttempts` | Максимальное количество попыток доставить событие. Значение по умолчанию — 30.
| `broker__defaultEventTimeToLiveInSeconds` | Время в жизни (TTL) в секундах, после чего событие будет удалено, если не доставлено. Значение по умолчанию составляет **7200** секунд

## <a name="output-batching"></a>Пакетная обработка выходных данных

Чтобы узнать об этой функции в целом, см [Доставка и выход пакетирования](delivery-output-batching.md).

| Имя свойства | Описание |
| ---------------- | ------------ |
| `api__deliveryPolicyLimits__maxBatchSizeInBytes` | Максимальное значение, `ApproxBatchSizeInBytes` допустимое для ручки. Значение по умолчанию: `1_058_576`.
| `api__deliveryPolicyLimits__maxEventsPerBatch` | Максимальное значение, `MaxEventsPerBatch` допустимое для ручки. Значение по умолчанию: `50`.
| `broker__defaultMaxBatchSizeInBytes` | Максимальный размер запроса на доставку, когда указано только. `MaxEventsPerBatch` Значение по умолчанию: `1_058_576`.
| `broker__defaultMaxEventsPerBatch` | Максимальное количество событий, чтобы добавить к пакету, когда только `MaxBatchSizeInBytes` указано. Значение по умолчанию: `10`.

## <a name="metrics"></a>Метрики

Чтобы узнать об использовании метрик с [monitor topics and subscriptions](monitor-topics-subscriptions.md) Event Grid на IoT Edge, см.

| Имя свойства | Описание |
| ---------------- | ------------ |
| `metrics__reporterType` | Тип репортера для метрик enpoint. По `none` умолчанию есть и отстраняет метрики. Настройка `prometheus` для вхотливых метрик в формате экспозиции Prometheus.