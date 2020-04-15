---
title: Удручающий TLS 1.0 и 1.1 в концентраторе IoT Документы Майкрософт
description: Руководящие принципы по амортизацию TLS 1.0 и 1.1 и поддерживаемые шифры в IoT Hub.
author: jlian
ms.author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: a887dd4df44ba58b0e6646ffb1c10eb21edf3e69
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81381301"
---
# <a name="deprecation-of-tls-10-and-11-in-iot-hub"></a>Изуза TLS 1.0 и 1.1 в концентраторе IoT

Чтобы обеспечить лучшее в своем классе шифрование, IoT Hub переходит на Transport Layer Security (TLS) 1.2 в качестве механизма шифрования для устройств и служб IoT. 

## <a name="timeline"></a>Временная шкала

IoT Концентратор будет поддерживать TLS 1.0/1.1 до дальнейшего уведомления. Тем не менее, мы рекомендуем всем клиентам мигрировать в TLS 1.2 как можно скорее.

## <a name="supported-ciphers"></a>Поддерживаемые шифры

Сроки наличия различных шифров, используемых в рукопожатии TLS, следующие:

* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256 (в настоящее время поддерживается)
* TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384 (будет поддержанво во второй половине 2020 года)
* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 (будет поддержан во второй половине 2020 года)
* TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384 (будет поддержан во второй половине 2020 года)

## <a name="customer-feedback"></a>Отзывы пользователей

В то время как правоприменение TLS 1.2 является лучшим в отрасли выбором шифрования и будет включено в плановом порядке, мы все равно хотели бы услышать от клиентов об их конкретных развертываниях и трудностях принятия TLS 1.2. Для этого вы можете отправить [iot_tls1_deprecation@microsoft.com](mailto:iot_tls1_deprecation@microsoft.com)свои комментарии .
