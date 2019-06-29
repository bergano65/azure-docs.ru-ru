---
title: Преобразование за брандмауэрами с помощью API перевода текстов
titlesuffix: Azure Cognitive Services
description: Преобразование за брандмауэрами с помощью API перевода текстов.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: d0ebce2dd41b170a18057ca76dd3ae4cf3ce0be2
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67435977"
---
# <a name="how-to-translate-behind-ip-firewalls-with-the-translator-text-api"></a>Преобразование за брандмауэрами с помощью API перевода текстов

API перевода текстов может выполнять преобразование за брандмауэрами, используя фильтрацию по доменным именам или IP-адресам. Фильтрация по доменным именам является предпочтительной. Мы **не рекомендуем** использовать Microsoft Translator за пределами брандмауэра для отфильтрованных IP-адресов. Это конфигурация, скорее всего, изменится в будущем без предварительного уведомления.

## <a name="translator-ip-addresses"></a>IP-адреса для преобразования
IP-адреса для api.cognitive.microsofttranslator.com (API перевода текстов Microsoft Translator) по состоянию на 20 ноября 2018 г.:

* **Азиатско-Тихоокеанский регион**: 40.90.139.163, 104.44.89.44
* **Европа** 40.90.138.4, 40.90.141.99
* **Северная Америка** 40.90.139.36, 40.90.139.2


## <a name="next-steps"></a>Дальнейшие действия
> [!div class="nextstepaction"]
> [Преобразование за брандмауэрами IP-адресов при вызове API перевода](reference/v3-0-translate.md)
