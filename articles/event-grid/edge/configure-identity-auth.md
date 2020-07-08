---
title: Настройка удостоверения — служба "Сетка событий Azure" IoT Edge | Документация Майкрософт
description: Настройка удостоверения модуля сетки событий
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/05/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 0aedeea2a6ad08e1627c2d1a6ebde6c91a4d02d9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "76841771"
---
# <a name="configure-identity-for-the-event-grid-module"></a>Настройка удостоверения для модуля "Сетка событий"

В этой статье показано, как настроить удостоверение для сетки на границе. По умолчанию модуль "Сетка событий" представляет свой сертификат удостоверений, настроенный управляющей программой IoT. Сетка событий на границе представляет сертификат удостоверения с исходящими вызовами при доходе доставки событий. Затем подписчик может проверить модуль сетки событий, который отправил событие перед принятием.

Все возможные конфигурации см. в разделе руководств по [безопасности и проверке подлинности](security-authentication.md) .

## <a name="always-present-identity-certificate"></a>Сертификат удостоверения всегда представлен
Ниже приведен пример конфигурации для постоянного представления сертификата идентификации в исходящих вызовах. 

```json
 {
  "Env": [
    "outbound__clientAuth__clientCert__enabled=true",
    "outbound__clientAuth__clientCert__source=IoTEdge"
  ]
}
 ```

## <a name="dont-present-identity-certificate"></a>Не представлять сертификат удостоверения
Ниже приведен пример конфигурации для отсутствия сертификата удостоверения для исходящих вызовов. 

```json
 {
  "Env": [
    "outbound__clientAuth__clientCert__enabled=false"
  ]
}
 ```
