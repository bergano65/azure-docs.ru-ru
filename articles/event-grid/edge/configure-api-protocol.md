---
title: Настройка протоколов API в службе "Сетка событий Azure" IoT Edge | Документация Майкрософт
description: Настройте протоколы API, предоставляемые службой "Сетка событий" для IoT Edge.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: 801a320fbd66b4b8a46757ba90881da54b2721de
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/09/2020
ms.locfileid: "86171726"
---
# <a name="configure-event-grid-api-protocols"></a>Настройка протоколов API сетки событий

В этом разделе приведены примеры возможных конфигураций протокола модуля сетки событий. Модуль сетки событий предоставляет API для операций управления и выполнения. В следующей таблице записаны протоколы и порты.

| Протокол | Порт | Описание |
| ---------------- | ------------ | ------------ |
| HTTP | 5888 | Отключено по умолчанию. Используется только во время тестирования. Не подходит для рабочих нагрузок.
| HTTPS | 4438 | Значение по умолчанию

Все возможные конфигурации см. в разделе руководств по [безопасности и проверке подлинности](security-authentication.md) .

## <a name="expose-https-to-iot-modules-on-the-same-edge-network"></a>Предоставление протокола HTTPS для модулей Интернета вещей в одной и той же сети периметра

```json
 {
  "Env": [
    "inbound__serverAuth__tlsPolicy=strict",
    "inbound__serverAuth__serverCert__source=IoTEdge"
  ]
}
 ```

## <a name="enable-https-to-other-iot-modules-and-non-iot-workloads"></a>Включение протокола HTTPS для других модулей Интернета вещей и рабочих нагрузок, отличных от IoT

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
> Раздел **портбиндингс** позволяет сопоставлять внутренние порты с портами узла контейнера. Эта функция позволяет получить доступ к модулю сетки событий вне IoT Edge сети контейнеров, если устройство IoT ребр доступно в открытом доступе.

## <a name="expose-http-and-https-to-iot-modules-on-the-same-edge-network"></a>Предоставление доступа к модулям Интернета вещей по протоколу HTTP и HTTPS в одной и той же сети периметра

```json
 {
  "Env": [
    "inbound__serverAuth__tlsPolicy=enabled",
    "inbound__serverAuth__serverCert__source=IoTEdge"
  ]
}
 ```

## <a name="enable-http-and-https-to-other-iot-modules-and-non-iot-workloads"></a>Включение HTTP и HTTPS для других модулей Интернета вещей и рабочих нагрузок, отличных от IoT

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
> По умолчанию каждый модуль IoT входит в состав среды выполнения IoT Edge, созданной сетью моста. Она позволяет различным модулям Интернета вещей в одной сети взаимодействовать друг с другом. **Портбиндингс** позволяет сопоставлять внутренний порт контейнера на хост-компьютере, тем самым позволяя любому пользователю получить доступ к порту модуля сетки событий извне.

>[!IMPORTANT]
> Хотя порты могут быть доступны вне IoT Edge сети, проверка подлинности клиента обеспечивает, кто действительно разрешает выполнять вызовы в модуль.
