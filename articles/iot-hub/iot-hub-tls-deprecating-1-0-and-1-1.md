---
title: Депрекатация TLS 1.0 и 1.1 в IoT концентраторе и службе обеспечения устройств (DPS) Документы Майкрософт
description: Руководящие принципы по амортизацию TLS 1.0 и 1.1 и поддерживаемые шифры в IoT Hub и DPS.
author: rezasherafat
ms.author: rezas
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/16/2019
ms.openlocfilehash: d61ca8fe7c6f5e7cc400714d7c31a0a7e50b8a88
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78402789"
---
# <a name="deprecation-of-tls-10-and-11-in-iot-hub-and-device-provisioning-service"></a>Изуза TLS 1.0 и 1.1 в IoT концентраторе и службе обеспечения устройств

Чтобы обеспечить лучшее в своем классе шифрование, IoT Hub и Служба обеспечения устройств (DPS) переходят на Transport Layer Security (TLS) 1.2 в качестве механизма шифрования для устройств и служб IoT. 

## <a name="supported-ciphers"></a>Поддерживаемые шифры

Сроки наличия различных шифров, используемых в рукопожатии TLS, следующие:

* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256 (в настоящее время поддерживается)
* TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384 (будет поддержанво во второй половине 2020 года)
* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 (будет поддержан во второй половине 2020 года)
* TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384 (будет поддержан во второй половине 2020 года)


## <a name="customer-feedback"></a>Отзывы пользователей

В то время как правоприменение TLS 1.2 является лучшим в отрасли выбором шифрования и будет включено в плановом порядке, мы все равно хотели бы услышать от клиентов об их конкретных развертываниях и трудностях принятия TLS 1.2. Для этого вы можете отправить [iot_tls1_deprecation@microsoft.com](mailto:iot_tls1_deprecation@microsoft.com)свои комментарии .
