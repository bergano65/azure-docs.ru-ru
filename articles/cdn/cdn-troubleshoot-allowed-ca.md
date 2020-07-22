---
title: Разрешенный ЦС для включения пользовательского HTTPS на Azure CDN
description: Если вы используете свой собственный сертификат для включения HTTPS в личном домене, вам нужен разрешенный центр сертификации (CA) для его создания.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 10/18/2018
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: 29b6cb25e021e86ce6663b4db5c89217aaf70a37
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84887407"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-cdn"></a>Разрешенные центры сертификации для включения настраиваемого HTTPS в Azure CDN

При [включении функции HTTPS с помощью собственного сертификата](cdn-custom-ssl.md?tabs=option-2-enable-https-with-your-own-certificate#tlsssl-certificates) для пользовательского домена сети доставки содержимого Azure (CDN) необходимо соблюдать требования к сертификатам. **Azure CDN стандарт из профиля Майкрософт** требует сертификат от одного из утвержденных центров сертификации (CA) в следующем списке. Если используется сертификат из неутвержденного ЦС или самозаверяющий сертификат, запрос отклоняется. **Azure CDN Standard от Verizon** и **Azure CDN Premium из профилей Verizon** принимают любой действительный сертификат от любого допустимого ЦС.

> [!NOTE]
> Возможность использования собственного сертификата для включения функции HTTPS пользовательского домена *недоступна* для **Azure CDN Standard из профилей Akamai** . 
>

[!INCLUDE [cdn-front-door-allowed-ca](../../includes/cdn-front-door-allowed-ca.md)]

