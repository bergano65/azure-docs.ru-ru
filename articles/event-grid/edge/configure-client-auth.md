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
ms.openlocfilehash: b5456130e89bf77e2c2ba41880323e38f6b27f4c
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/28/2019
ms.locfileid: "72992513"
---
# <a name="configure-client-authentication-of-incoming-calls"></a>Настройка проверки подлинности входящих вызовов клиента

В этом разделе приведены примеры возможных конфигураций проверки подлинности клиента для модуля "Сетка событий". Модуль "Сетка событий" поддерживает два типа проверки подлинности клиента:-

* Подпись общего доступа (SAS) на основе ключей
* на основе сертификата

Все возможные конфигурации см. в разделе руководств по [безопасности и проверке подлинности](security-authentication.md) .

## <a name="enable-certificate-based-client-authentication-no-self-signed-certificates"></a>Включение проверки подлинности клиентов на основе сертификатов, без самозаверяющих сертификатов

```json
 {
  "Env": [
    "inbound:clientAuth:sasKeys:enabled=false",
    "inbound:clientAuth:clientCert:enabled=true",
    "inbound:clientAuth:clientCert:source=IoTEdge",
    "inbound:clientAuth:clientCert:allowUnknownCA=false"
  ]
}
 ```

## <a name="enable-certificate-based-client-authentication-allow-self-signed-certificates"></a>Включение проверки подлинности клиента на основе сертификата, разрешение самозаверяющих сертификатов

```json
 {
  "Env": [
    "inbound:clientAuth:sasKeys:enabled=false",
    "inbound:clientAuth:clientCert:enabled=true",
    "inbound:clientAuth:clientCert:source=IoTEdge",
    "inbound:clientAuth:clientCert:allowUnknownCA=true"
  ]
}
```

>[!NOTE]
>Задайте для свойства **Inbound: клиентаус: клиентцерт: алловункновнка** **значение true** только в тестовых средах, так как обычно можно использовать самозаверяющие сертификаты. Для рабочих нагрузок рекомендуется присвоить этому свойству **значение false** и сертификаты из центра сертификации (ЦС).

## <a name="enable-certificate-based-and-sas-key-based-client-authentication"></a>Включение проверки подлинности клиента на основе сертификата и SAS-ключа

```json
 {
  "Env": [
    "inbound:clientAuth:sasKeys:enabled=true",
    "inbound:clientAuth:sasKeys:key1=<some-secret1-here>",
    "inbound:clientAuth:sasKeys:key2=<some-secret2-here>",
    "inbound:clientAuth:clientCert:enabled=true",
    "inbound:clientAuth:clientCert:source=IoTEdge",
    "inbound:clientAuth:clientCert:allowUnknownCA=true"
  ]
}
 ```

>[!NOTE]
>Проверка подлинности клиента на основе ключа SAS позволяет модулю, не относящейся к IoT, выполнять операции управления и выполнения, исходя из того, что порты API доступны за пределами IoT Edge сети.
