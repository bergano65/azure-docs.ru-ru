---
title: Разрешенный ЦС для включения пользовательского HTTPS в передней дверце Azure
description: Если вы используете свой собственный сертификат для включения HTTPS в личном домене, вам нужен разрешенный центр сертификации (CA) для его создания.
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.assetid: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/23/2018
ms.author: sharadag
ms.openlocfilehash: 611f5730afed4c3a84b81d6acfd33b633c532bbc
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "80874676"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-front-door"></a>Разрешенные центры сертификации для включения пользовательского HTTPS в передней дверце Azure

Если вы [включили функцию HTTPS с помощью собственного сертификата](front-door-custom-domain-https.md?tabs=option-2-enable-https-with-your-own-certificate), то для пользовательского домена "Передняя дверца Azure" необходимо использовать разрешенный центр сертификации (ЦС) для создания сертификата TLS/SSL. В противном случае при использовании недопустимого ЦС или самозаверяющего сертификата ваш запрос будет отклонен.

[!INCLUDE [cdn-front-door-allowed-ca](../../includes/cdn-front-door-allowed-ca.md)]
