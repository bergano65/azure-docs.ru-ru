---
title: Настройка проверки подлинности входящих вызовов клиента с помощью службы "Сетка событий Azure" IoT Edge | Документация Майкрософт
description: Настройте протоколы API, предоставляемые службой "Сетка событий" для IoT Edge.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/03/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 3363db4557dd19e8d72747ccd62bb535abb7b1e2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "76841797"
---
# <a name="configure-client-authentication-of-incoming-calls"></a>Настройка проверки подлинности входящих вызовов клиента

В этом разделе приведены примеры возможных конфигураций проверки подлинности клиента для модуля "Сетка событий". Модуль "Сетка событий" поддерживает два типа проверки подлинности клиента:

* Подпись общего доступа (SAS) на основе ключей
* На основе сертификатов

Все возможные конфигурации см. в разделе руководств по [безопасности и проверке подлинности](security-authentication.md) .

## <a name="enable-certificate-based-client-authentication-no-self-signed-certificates"></a>Включение проверки подлинности клиентов на основе сертификатов, без самозаверяющих сертификатов

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

## <a name="enable-certificate-based-client-authentication-allow-self-signed-certificates"></a>Включение проверки подлинности клиента на основе сертификата, разрешение самозаверяющих сертификатов

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
>Присвойте свойству **inbound__clientAuth__clientCert__allowUnknownCA** **значение true** только в тестовых средах, так как обычно можно использовать самозаверяющие сертификаты. Для рабочих нагрузок рекомендуется присвоить этому свойству **значение false** и сертификаты из центра сертификации (ЦС).

## <a name="enable-certificate-based-and-sas-key-based-client-authentication"></a>Включение проверки подлинности клиента на основе сертификата и SAS-ключа

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
>Проверка подлинности клиента на основе ключа SAS позволяет модулю, не относящейся к IoT, выполнять операции управления и выполнения, исходя из того, что порты API доступны за пределами IoT Edge сети.
