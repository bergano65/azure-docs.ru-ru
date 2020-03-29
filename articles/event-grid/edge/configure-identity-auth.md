---
title: Настройка итога - Azure Event Grid IoT Edge Документы Майкрософт
description: Настройка модуля Event Grid
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/05/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 0aedeea2a6ad08e1627c2d1a6ebde6c91a4d02d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76841771"
---
# <a name="configure-identity-for-the-event-grid-module"></a>Настройка итог для модуля Event Grid

В этой статье показано, как настроить идентификацию для Grid on Edge. По умолчанию модуль Event Grid представляет свой сертификат идентификации, настроенный на daemon безопасности IoT. Event Grid on Edge представляет свой сертификат идентификации с исходящие вызовы, когда он обеспечивает события. Абонент может проверить, что это модуль Event Grid, который отправил событие перед принятием.

Смотрите руководство по [безопасности и аутентификации](security-authentication.md) для всех возможных конфигураций.

## <a name="always-present-identity-certificate"></a>Всегда приспоивайте удостоверение личности
Вот примерная конфигурация для всегда представления удостоверения личности при исходящих вызовах. 

```json
 {
  "Env": [
    "outbound__clientAuth__clientCert__enabled=true",
    "outbound__clientAuth__clientCert__source=IoTEdge"
  ]
}
 ```

## <a name="dont-present-identity-certificate"></a>Не предъявить удостоверение личности
Вот примерконфигурации не представляя удостоверение личности на исходящих вызовах. 

```json
 {
  "Env": [
    "outbound__clientAuth__clientCert__enabled=false"
  ]
}
 ```
