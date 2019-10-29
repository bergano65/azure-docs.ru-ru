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
ms.openlocfilehash: 336b6157128468169264d6ffa9564da4d9338aae
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/28/2019
ms.locfileid: "72992448"
---
# <a name="configure-identity-for-the-event-grid-module"></a>Настройка удостоверения для модуля "Сетка событий"

В этой статье приводятся примеры возможных конфигураций удостоверений для модуля сетки событий. По умолчанию модуль "Сетка событий" будет представлять свой сертификат удостоверений, настроенный управляющей программой IoT. Сертификат удостоверения представляется модулем сетки событий в исходящих вызовах, т. е. при доходе доставки событий. После этого подписчик на событие сетки событий может проверить, что это действительно модуль сетки событий, который отправил событие перед принятием события.

Все возможные конфигурации см. в разделе руководств по [безопасности и проверке подлинности](security-authentication.md) .

## <a name="always-present-identity-certificate"></a>Сертификат удостоверения всегда представлен
Ниже приведен пример конфигурации для постоянного представления сертификата идентификации в исходящих вызовах. 

```json
 {
  "Env": [
    "outbound:clientAuth:clientCert:enabled=true",
    "outbound:clientAuth:clientCert:source=IoTEdge"
  ]
}
 ```

## <a name="dont-present-identity-certificate"></a>Не представлять сертификат удостоверения
Ниже приведен пример конфигурации для отсутствия сертификата удостоверения для исходящих вызовов. 

```json
 {
  "Env": [
    "outbound:clientAuth:clientCert:enabled=false"
  ]
}
 ```
