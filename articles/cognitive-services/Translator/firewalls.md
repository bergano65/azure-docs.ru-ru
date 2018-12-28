---
title: Преобразование за брандмауэрами с помощью API перевода текстов
titlesuffix: Azure Cognitive Services
description: Преобразование за брандмауэрами с помощью API перевода текстов.
services: cognitive-services
author: Jann-Skotdal
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: conceptual
ms.date: 11/20/2018
ms.author: v-jansko
ms.openlocfilehash: bf1a63d996a1ed2021654c96576eb9dac2cff8e6
ms.sourcegitcommit: cd0a1514bb5300d69c626ef9984049e9d62c7237
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/30/2018
ms.locfileid: "52683335"
---
# <a name="how-to-translate-behind-ip-firewalls-with-the-translator-text-api"></a>Преобразование за брандмауэрами с помощью API перевода текстов

API перевода текстов может выполнять преобразование за брандмауэрами, используя фильтрацию по доменным именам или IP-адресам. Фильтрация по доменным именам является предпочтительной. Мы **не рекомендуем** использовать Microsoft Translator за пределами брандмауэра для отфильтрованных IP-адресов. Это конфигурация, скорее всего, изменится в будущем без предварительного уведомления. 

## <a name="translator-ip-addresses"></a>IP-адреса для преобразования
IP-адреса для api.cognitive.microsofttranslator.com (API перевода текстов Microsoft Translator) по состоянию на 20 ноября 2018 г.:

* **Азиатско-Тихоокеанский регион**: 40.90.139.163, 104.44.89.44
* **Европа** 40.90.138.4, 40.90.141.99
* **Северная Америка** 40.90.139.36, 40.90.139.2


## <a name="next-steps"></a>Дополнительная информация
> [!div class="nextstepaction"]
> [Преобразование за брандмауэрами IP-адресов при вызове API перевода](reference/v3-0-translate.md)