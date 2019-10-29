---
title: Настройка — служба "Сетка событий Azure" IoT Edge | Документация Майкрософт
description: Настройка в сетке событий на IoT Edge.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/03/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: a9bac9a84e1ba034b011691ae82a1cb67eb71af0
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/28/2019
ms.locfileid: "72992435"
---
# <a name="event-grid-configuration"></a>Конфигурация сетки событий

Сетка событий предоставляет множество конфигураций, которые можно изменять для каждой среды. В следующем разделе приведена ссылка на все доступные параметры и их значения по умолчанию.

## <a name="tls-configuration"></a>Конфигурация TLS

Общие сведения о проверке подлинности клиента см. в разделе [безопасность и проверка подлинности](security-authentication.md). Примеры использования можно найти в [этой статье](configure-api-protocol.md).

| Имя свойства | Описание |
| ---------------- | ------------ |
|`inbound:serverAuth:tlsPolicy`| Политика TLS модуля "Сетка событий". Значение по умолчанию — только HTTPS.
|`inbound:serverAuth:serverCert:source`| Источник сертификата сервера, используемый модулем сетки событий для своей конфигурации TLS. Значение по умолчанию — IoT Edge.

## <a name="incoming-client-authentication"></a>Входящая проверка подлинности клиента

Общие сведения о проверке подлинности клиента см. в разделе [безопасность и проверка подлинности](security-authentication.md). Примеры можно найти в [этой статье](configure-client-auth.md).

| Имя свойства | Описание |
| ---------------- | ------------ |
|`inbound:clientAuth:clientCert:enabled`| Включение и отключение проверки подлинности клиента на основе сертификата. Значение по умолчанию — true.
|`inbound:clientAuth:clientCert:source`| Источник для проверки сертификатов клиента. Значение по умолчанию — IoT Edge.
|`inbound:clientAuth:clientCert:allowUnknownCA`| Политика, разрешающая самозаверяющий сертификат клиента. Значение по умолчанию — true.
|`inbound:clientAuth:sasKeys:enabled`| Включение/выключение проверки подлинности клиента на основе ключа SAS. Значение по умолчанию — OFF.
|`inbound:clientAuth:sasKeys:key1`| Одно из значений для проверки входящих запросов.
|`inbound:clientAuth:sasKeys:key2`| Необязательное второе значение для проверки входящих запросов.

## <a name="outgoing-client-authentication"></a>Исходящая проверка подлинности клиента
Общие сведения о проверке подлинности клиента см. в разделе [безопасность и проверка подлинности](security-authentication.md). Примеры можно найти в [этой статье](configure-identity-auth.md).

| Имя свойства | Описание |
| ---------------- | ------------ |
|`outbound:clientAuth:clientCert:enabled`| Включение и отключение подключения сертификата удостоверения для исходящих запросов. Значение по умолчанию — true.
|`outbound:clientAuth:clientCert:source`| Источник для получения исходящего сертификата модуля сетки событий. Значение по умолчанию — IoT Edge.

## <a name="webhook-event-handlers"></a>Обработчики событий веб-перехватчика

Общие сведения о проверке подлинности клиента см. в разделе [безопасность и проверка подлинности](security-authentication.md). Примеры можно найти в [этой статье](configure-webhook-subscriber-auth.md).

| Имя свойства | Описание |
| ---------------- | ------------ |
|`outbound:webhook:httpsOnly`| Политика, определяющая, будут ли разрешены только подписчики HTTPS. Значение по умолчанию — true (только HTTPS).
|`outbound:webhook:skipServerCertValidation`| Флаг, определяющий, следует ли проверять сертификат подписчика. Значение по умолчанию — true.
|`outbound:webhook:allowUnknownCA`| Политика для управления тем, может ли подписчик предоставлять самозаверяющий сертификат. Значение по умолчанию — true. 

## <a name="delivery-and-retry"></a>Доставка и повторные попытки доставки

Общие сведения об этой функции см. в разделе [Доставка и повторная попытка](delivery-retry.md).

| Имя свойства | Описание |
| ---------------- | ------------ |
| `broker:defaultMaxDeliveryAttempts` | Максимальное число попыток доставки события. Значение по умолчанию — 30.
| `broker:defaultEventTimeToLiveInSeconds` | Срок жизни (TTL) (в секундах), после которого событие будет удалено, если оно не доставлено. Значение по умолчанию — **7200** секунд.

## <a name="output-batching"></a>Пакетная обработка выходных данных

Общие сведения об этой функции см. в статье [Пакетная обработка доставки и вывода данных](delivery-output-batching.md).

| Имя свойства | Описание |
| ---------------- | ------------ |
| `api:deliveryPolicyLimits:maxBatchSizeInBytes` | Максимальное значение, допустимое для `ApproxBatchSizeInBytes`ного регулятора. По умолчанию имеет значение `1_058_576`.
| `api:deliveryPolicyLimits:maxEventsPerBatch` | Максимальное значение, допустимое для `MaxEventsPerBatch`ного регулятора. По умолчанию имеет значение `50`.
| `broker:defaultMaxBatchSizeInBytes` | Максимальный размер запроса на доставку, если указан только `MaxEventsPerBatch`. По умолчанию имеет значение `1_058_576`.
| `broker:defaultMaxEventsPerBatch` | Максимальное число событий, добавляемых в пакет, если указано только `MaxBatchSizeInBytes`. По умолчанию имеет значение `10`.
