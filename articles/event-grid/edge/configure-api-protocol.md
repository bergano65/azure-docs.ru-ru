---
title: Настройка протоколов API - Azure Event Grid IoT Edge Документы Майкрософт
description: Настройка протоколов API, выставленных Event Grid на IoT Edge.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: ''
ms.date: 10/03/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 908bc941ee7379de067621e10adf5fd6ee6df559
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76841816"
---
# <a name="configure-event-grid-api-protocols"></a>Настройка aPI-протоколов API сетки событий

В этом руководстве приводятся примеры возможных конфигураций протокола модуля Event Grid. Модуль Event Grid предоставляет API для управления и работы в срок выполнения. В следующей таблице отражены протоколы и порты.

| Протокол | Порт | Описание |
| ---------------- | ------------ | ------------ |
| HTTP | 5888 | Выключен по умолчанию. Полезно только во время тестирования. Не подходит для производственных нагрузок.
| HTTPS | 4438 | Значение по умолчанию

Смотрите руководство по [безопасности и аутентификации](security-authentication.md) для всех возможных конфигураций.

## <a name="expose-https-to-iot-modules-on-the-same-edge-network"></a>Экспозиция HTTPS в IoT-модули в той же сети края

```json
 {
  "Env": [
    "inbound__serverAuth__tlsPolicy=strict",
    "inbound__serverAuth__serverCert__source=IoTEdge"
  ]
}
 ```

## <a name="enable-https-to-other-iot-modules-and-non-iot-workloads"></a>Включить HTTPS для других модулей IoT и рабочих нагрузок, не связанных с IoT

```json
 {
  "Env": [
    "inbound__serverAuth__tlsPolicy=strict",
    "inbound__serverAuth__serverCert__source=IoTEdge"
  ],
  "HostConfig": {
    "PortBindings": {
      "4438/tcp": [
        {
          "HostPort": "4438"
        }
      ]
    }
  }
}
 ```

>[!NOTE]
> Раздел **PortBindings** позволяет отображение внутренних портов в порты контейнерного хостова. Эта функция позволяет достичь модуля Event Grid из-за пределов контейнерной сети IoT Edge, если устройство IoT edge доступно публично.

## <a name="expose-http-and-https-to-iot-modules-on-the-same-edge-network"></a>Экспозиция HTTP и HTTPS модулям IoT в одной и той же сети края

```json
 {
  "Env": [
    "inbound__serverAuth__tlsPolicy=enabled",
    "inbound__serverAuth__serverCert__source=IoTEdge"
  ]
}
 ```

## <a name="enable-http-and-https-to-other-iot-modules-and-non-iot-workloads"></a>Включить HTTP и HTTPS для других модулей IoT и рабочих нагрузок, не связанных с IoT

```json
 {
  "Env": [
    "inbound__serverAuth__tlsPolicy=enabled",
    "inbound__serverAuth__serverCert__source=IoTEdge"
  ],
  "HostConfig": {
    "PortBindings": {
      "4438/tcp": [
        {
          "HostPort": "4438"
        }
      ],
      "5888/tcp": [
        {
          "HostPort": "5888"
        }
      ]
    }
  }
}
 ```

>[!NOTE]
> По умолчанию каждый модуль IoT является частью времени выполнения IoT Edge, создаваемого сетью мостов. Это позволяет различным модулям IoT в одной и той же сети общаться друг с другом. **PortBindings** позволяет сопоставить внутренний порт контейнера с принимающей машиной, что позволяет любому человеку получить доступ к порту модуля Event Grid извне.

>[!IMPORTANT]
> В то время как порты могут быть доступны за пределами сети IoT Edge, аутентификация клиента обеспечивает, кто фактически может совершать звонки в модуль.
