---
title: Устаревание протоколов TLS 1.0 и 1.1 в Центре Интернета вещей | Документация Майкрософт
description: Рекомендации, связанные с устареванием протоколов TLS 1.0 и 1.1, и сведения о поддерживаемых шифрах в Центре Интернета вещей.
author: jlian
ms.author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: 5c717a02c2008436617d16f08625a1cecc204340
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/26/2020
ms.locfileid: "83849524"
---
# <a name="deprecation-of-tls-10-and-11-in-iot-hub"></a>Устаревание протоколов TLS 1.0 и 1.1 в Центре Интернета вещей

Чтобы обеспечить лучшее в своем классе шифрование, Центр Интернета вещей переходит на использование протокола TLS 1.2 в качестве стандартного механизма шифрования для устройств и служб Интернета вещей. 

## <a name="timeline"></a>Временная шкала

Центр Интернета вещей продолжит поддержку протоколов TLS 1.0/1.1 до дальнейших указаний. Но мы рекомендуем всем клиентам как можно скорее перейти на использование TLS 1.2.

## <a name="deprecating-tls-11-ciphers"></a>Устаревшие шифры TLS 1.1

* `TLS_ECDHE_RSA_WITH_AES_256_CBC_SH`
* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA`
* `TLS_RSA_WITH_AES_256_CBC_SHA`
* `TLS_RSA_WITH_AES_128_CBC_SHA`
* `TLS_RSA_WITH_3DES_EDE_CBC_SHA`

## <a name="deprecating-tls-10-ciphers"></a>Устаревшие шифры TLS 1.0

* `TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA`
* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA`
* `TLS_RSA_WITH_AES_256_CBC_SHA`
* `TLS_RSA_WITH_AES_128_CBC_SHA`
* `TLS_RSA_WITH_3DES_EDE_CBC_SHA`

## <a name="tls-12-ciphers"></a>Шифры TLS 1.2

См. раздел "Рекомендуемые шифры" из [этой статьи](iot-hub-tls-support.md#recommended-ciphers).
 
## <a name="customer-feedback"></a>Отзывы пользователей

Несмотря на то, что применение TLS 1.2 является передовой и общепринятой отраслевой практикой, а протокол будет обязательно внедряться нами в соответствии с планом, мы будем рады узнать от клиентов об их конкретных случаях развертывания и проблемах, связанных с переходом на TLS 1.2. Комментарии на эту тему отправляйте на адрес [iot_tls1_deprecation@microsoft.com](mailto:iot_tls1_deprecation@microsoft.com).
