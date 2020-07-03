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
ms.topic: article
ms.date: 10/18/2018
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: faf51dbb1f1c3c0346b1ae9104494538efcc2ee7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81259979"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-cdn"></a>Разрешенные центры сертификации для включения настраиваемого HTTPS в Azure CDN

При [включении функции HTTPS с помощью собственного сертификата](cdn-custom-ssl.md?tabs=option-2-enable-https-with-your-own-certificate#tlsssl-certificates) для пользовательского домена сети доставки содержимого Azure (CDN) необходимо соблюдать требования к сертификатам. **Azure CDN стандарт из профиля Майкрософт** требует сертификат от одного из утвержденных центров сертификации (CA) в следующем списке. Если используется сертификат из неутвержденного ЦС или самозаверяющий сертификат, запрос отклоняется. **Azure CDN Standard от Verizon** и **Azure CDN Premium из профилей Verizon** принимают любой действительный сертификат от любого допустимого ЦС.

> [!NOTE]
> Возможность использования собственного сертификата для включения функции HTTPS пользовательского домена *недоступна* для **Azure CDN Standard из профилей Akamai** . 
>

[!INCLUDE [cdn-front-door-allowed-ca](../../includes/cdn-front-door-allowed-ca.md)]

