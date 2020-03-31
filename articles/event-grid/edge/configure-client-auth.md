---
title: Настройка аутентификации клиентов входящих вызовов - Azure Event Grid IoT Edge Документы Майкрософт
description: Настройка протоколов API, выставленных Event Grid на IoT Edge.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/03/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 3363db4557dd19e8d72747ccd62bb535abb7b1e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76841797"
---
# <a name="configure-client-authentication-of-incoming-calls"></a>Настройка аутентификации клиентов входящих вызовов

В этом руководстве приводятся примеры возможных конфигураций аутентификации клиента для модуля Event Grid. Модуль Event Grid поддерживает два типа проверки подлинности клиента:

* Общая подпись доступа (SAS) на основе ключей
* На основе сертификатов

Смотрите руководство по [безопасности и аутентификации](security-authentication.md) для всех возможных конфигураций.

## <a name="enable-certificate-based-client-authentication-no-self-signed-certificates"></a>Включить проверку подлинности клиента на основе сертификатов, отсутствие самоподписанных сертификатов

```json
 {
  "Env": [
    "inbound__clientAuth__sasKeys__enabled=false",
    "inbound__clientAuth__clientCert__enabled=true",
    "inbound__clientAuth__clientCert__source=IoTEdge",
    "inbound__clientAuth__clientCert__allowUnknownCA=false"
  ]
}
 ```

## <a name="enable-certificate-based-client-authentication-allow-self-signed-certificates"></a>Включить проверку подлинности клиента на основе сертификатов, позволить самоподписанные сертификаты

```json
 {
  "Env": [
    "inbound__clientAuth__sasKeys__enabled=false",
    "inbound__clientAuth__clientCert__enabled=true",
    "inbound__clientAuth__clientCert__source=IoTEdge",
    "inbound__clientAuth__clientCert__allowUnknownCA=true"
  ]
}
```

>[!NOTE]
>Установите свойство **inbound__clientAuth__clientCert__allowUnknownCA** **верным** только в тестовых средах, поскольку обычно можно использовать сертификаты, подписанные самостоятельно. Для производственных нагрузок мы рекомендуем установить это свойство **на фальшивые** и сертификаты от сертификата органа (CA).

## <a name="enable-certificate-based-and-sas-key-based-client-authentication"></a>Включить проверку подлинности клиента на основе сертификатов и sas-key

```json
 {
  "Env": [
    "inbound__clientAuth__sasKeys__enabled=true",
    "inbound__clientAuth__sasKeys__key1=<some-secret1-here>",
    "inbound__clientAuth__sasKeys__key2=<some-secret2-here>",
    "inbound__clientAuth__clientCert__enabled=true",
    "inbound__clientAuth__clientCert__source=IoTEdge",
    "inbound__clientAuth__clientCert__allowUnknownCA=true"
  ]
}
 ```

>[!NOTE]
>Проверка подлинности клиента на основе ключевых моментов SAS позволяет модулю, не являвемуомуся IoT edge, выполнять операции управления и времени выполнения при условии, что порты API доступны за пределами сети IoT Edge.
