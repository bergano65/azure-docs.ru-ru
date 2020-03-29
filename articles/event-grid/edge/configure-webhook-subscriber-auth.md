---
title: Налаживание аутентификации абонента веб-хука - Azure Event Grid IoT Edge Документы Майкрософт
description: Настройка аутентификации подписчика веб-перехватчика
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 101dcae5870322878cec48098f2efae32cc68c14
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76841736"
---
# <a name="configure-webhook-subscriber-authentication"></a>Настройка аутентификации подписчика веб-перехватчика

В этом руководстве приводятся примеры возможных конфигураций абонентов веб-крючка для модуля Event Grid. По умолчанию для подписчиков webhook принимаются только конечные точки HTTPS. Модуль Event Grid отклонится, если абонент представит сертификат, подписанный самостоятельно.

## <a name="allow-only-https-subscriber"></a>Разрешить только абоненту HTTPS

```json
 {
  "Env": [
    "outbound__webhook__httpsOnly=true",
    "outbound__webhook__skipServerCertValidation=false",
    "outbound__webhook__allowUnknownCA=false"
  ]
}
 ```

## <a name="allow-https-subscriber-with-self-signed-certificate"></a>Разрешить абоненту HTTPS с самоподписанным сертификатом

```json
 {
  "Env": [
    "outbound__webhook__httpsOnly=true",
    "outbound__webhook__skipServerCertValidation=false",
    "outbound__webhook__allowUnknownCA=true"
  ]
}
 ```

>[!NOTE]
>Установите `outbound__webhook__allowUnknownCA` свойство `true` только в тестовых средах, так как обычно можно использовать сертификаты, подписанные самостоятельно. Для производственных нагрузок мы рекомендуем их установить на **ложные.**

## <a name="allow-https-subscriber-but-skip-certificate-validation"></a>Разрешить httpS абонента, но пропустить проверку сертификата

```json
 {
  "Env": [
    "outbound__webhook__httpsOnly=true",
    "outbound__webhook__skipServerCertValidation=true",
    "outbound__webhook__allowUnknownCA=false"
  ]
}
 ```

>[!NOTE]
>Установите `outbound__webhook__skipServerCertValidation` свойство `true` только в тестовых средах, так как вы не можете представлять сертификат, который должен быть проверен. Для производственных нагрузок мы рекомендуем установить их на **ложные**

## <a name="allow-both-http-and-https-with-self-signed-certificates"></a>Разрешить как HTTP, так и HTTPS с самоподписанными сертификатами

```json
 {
  "Env": [
    "outbound__webhook__httpsOnly=false",
    "outbound__webhook__skipServerCertValidation=false",
    "outbound__webhook__allowUnknownCA=true"
  ]
}
 ```

>[!NOTE]
>Установите `outbound__webhook__httpsOnly` свойство `false` только в тестовых средах, как вы можете воспитывать http абонента в первую очередь. Для производственных нагрузок мы рекомендуем их установить на **истину**
