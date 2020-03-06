---
title: Устаревшие протоколы TLS 1,0 и 1,1 в центре Интернета вещей и службе подготовки устройств (DPS) | Документация Майкрософт
description: Рекомендации по прекращению использования TLS 1,0 и 1,1 и поддерживаемых шифров в центре Интернета вещей и в DPS.
author: rezasherafat
ms.author: rezas
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/16/2019
ms.openlocfilehash: d61ca8fe7c6f5e7cc400714d7c31a0a7e50b8a88
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2020
ms.locfileid: "78402789"
---
# <a name="deprecation-of-tls-10-and-11-in-iot-hub-and-device-provisioning-service"></a>Устаревшие протоколы TLS 1,0 и 1,1 в центре Интернета вещей и службе подготовки устройств

Чтобы обеспечить лучшее в своем классе шифрование, центр Интернета вещей и служба подготовки устройств (DPS) перемещаются в протокол TLS 1,2 в качестве механизма шифрования для устройств и служб Интернета вещей. 

## <a name="supported-ciphers"></a>Поддерживаемые шифры

Ниже приведена временная шкала доступности различных шифров, используемых в подтверждении TLS.

* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256 (сейчас поддерживается)
* TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384 (будет поддерживаться во второй половине 2020)
* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 (будет поддерживаться во второй половине 2020)
* TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384 (будет поддерживаться во второй половине 2020)


## <a name="customer-feedback"></a>Отзывы пользователей

Несмотря на то, что применение TLS 1,2 является лучшим в своем классе выбором шифрования и будет включено в соответствии с планом, мы по-прежнему хотели бы слышать пользователей о конкретных развертываниях и проблемах, связанных с принятием TLS 1,2. Для этой цели комментарии можно отправить в [iot_tls1_deprecation@microsoft.com](mailto:iot_tls1_deprecation@microsoft.com).
