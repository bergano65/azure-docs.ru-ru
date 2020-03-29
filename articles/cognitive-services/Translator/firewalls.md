---
title: Преобразование за брандмауэрами с помощью API перевода текстов
titleSuffix: Azure Cognitive Services
description: Azure Cognitive Services Translator Text PI может переводить за брандмауэрами с помощью доменного имени или IP-фильтрации.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: cd7904fedd3ab3f64315cb6f98d99b8fd12254f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "73837400"
---
# <a name="how-to-translate-behind-ip-firewalls-with-the-translator-text-api"></a>Преобразование за брандмауэрами с помощью API перевода текстов

API перевода текстов может выполнять преобразование за брандмауэрами, используя фильтрацию по доменным именам или IP-адресам. Фильтрация по доменным именам является предпочтительной. Мы **не рекомендуем** использовать Microsoft Translator за пределами брандмауэра для отфильтрованных IP-адресов. Это конфигурация, скорее всего, изменится в будущем без предварительного уведомления.

## <a name="translator-ip-addresses"></a>IP-адреса для преобразования
IP-адреса для api.cognitive.microsofttranslator.com - Microsoft Translator Text IPI от 21 августа 2019 года:

* **Азиатско-Тихоокеанский регион:** 20.40.125.208, 20.43.88.240, 20.184.58.62, 40.90.139.163, 104.44.89.44
* **Европа:** 40.90.138.4, 40.90.141.99, 51.105.170.64, 52.155.218.251
* **Северная Америка:** 40.90.139.36, 40.90.139.2, 40.119.2.134, 52.224.200.129, 52.249.207.163


## <a name="next-steps"></a>Дальнейшие действия
> [!div class="nextstepaction"]
> [Преобразование за брандмауэрами IP-адресов при вызове API перевода](reference/v3-0-translate.md)
