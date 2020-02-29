---
title: Разрешенный ЦС для включения пользовательского HTTPS на Azure CDN
description: Если вы используете свой собственный сертификат для включения HTTPS в личном домене, вам нужен разрешенный центр сертификации (CA) для его создания.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2018
ms.author: magattus
ms.custom: mvc
ms.openlocfilehash: 7b71611d43bc2d4de4c3e609462906c44fba0443
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/28/2020
ms.locfileid: "77919980"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-cdn"></a>Разрешенные центры сертификации для включения настраиваемого HTTPS в Azure CDN

При [включении функции HTTPS с помощью собственного сертификата](cdn-custom-ssl.md?tabs=option-2-enable-https-with-your-own-certificate#ssl-certificates) для пользовательского домена сети доставки содержимого Azure (CDN) необходимо соблюдать требования к сертификатам. **Azure CDN стандарт из профиля Майкрософт** требует сертификат от одного из утвержденных центров сертификации (CA) в следующем списке. Если используется сертификат из неутвержденного ЦС или самозаверяющий сертификат, запрос отклоняется. **Azure CDN Standard от Verizon** и **Azure CDN Premium из профилей Verizon** принимают любой действительный сертификат от любого допустимого ЦС.

> [!NOTE]
> Возможность использования собственного сертификата для включения функции HTTPS пользовательского домена *недоступна* для **Azure CDN Standard из профилей Akamai** . 
>

[!INCLUDE [cdn-front-door-allowed-ca](../../includes/cdn-front-door-allowed-ca.md)]

