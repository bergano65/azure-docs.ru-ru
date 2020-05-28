---
title: Перевод за брандмауэром — переводчик
titleSuffix: Azure Cognitive Services
description: Транслятор Azure Cognitive Services может переноситься за брандмауэрами с помощью доменного имени или фильтрации IP-адресов.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: swmachan
ms.openlocfilehash: 78a53c99f5f184c1b6b45d59d86c23efb898d7dc
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/27/2020
ms.locfileid: "83996964"
---
# <a name="how-to-translate-behind-ip-firewalls-with-translator"></a>Как выполнить перевод брандмауэров IP-адресов с помощью переводчика

Переводчик может переноситься за брандмауэром с помощью доменного имени или фильтрации IP-адресов. Фильтрация по доменным именам является предпочтительной. Мы **не рекомендуем** использовать Microsoft Translator за пределами брандмауэра для отфильтрованных IP-адресов. Это конфигурация, скорее всего, изменится в будущем без предварительного уведомления.

## <a name="translator-ip-addresses"></a>IP-адреса для преобразования
IP-адреса для api.cognitive.microsofttranslator.com-Translator от 21 августа 2019:

* **Азиатско-Тихоокеанский регион:** 20.40.125.208, 20.43.88.240, 20.184.58.62, 40.90.139.163, 104.44.89.44
* **Европа:** 40.90.138.4, 40.90.141.99, 51.105.170.64, 52.155.218.251
* **Северная Америка:** 40.90.139.36, 40.90.139.2, 40.119.2.134, 52.224.200.129, 52.249.207.163

## <a name="next-steps"></a>Дальнейшие действия
> [!div class="nextstepaction"]
> [Преобразование за брандмауэром IP в переводчике](reference/v3-0-translate.md)
