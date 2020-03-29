---
title: Разрешено CA для включения пользовательских HTTPS на Azure CDN
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77919980"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-cdn"></a>Разрешенные центры сертификации для включения настраиваемого HTTPS в Azure CDN

При [включании функции HTTPS с помощью собственного сертификата](cdn-custom-ssl.md?tabs=option-2-enable-https-with-your-own-certificate#ssl-certificates) для пользовательского домена Azure Content Delivery Network (CDN) необходимо соответствовать определенным требованиям сертификата. **Стандарт Azure CDN от** профиля Microsoft требует сертификат от одного из утвержденных органов сертификата (CA) в следующем списке. Если сертификат из неутвержденного ЦС или если используется сертификат самоподписанных, запрос отклоняется. **Стандарт Azure CDN от Verizon** и **Azure CDN Premium из** профилей Verizon принимают любой действительный сертификат от любого действительного CA.

> [!NOTE]
> Возможность использования собственного сертификата для включения пользовательского домена HTTPS *недоступна* для **Azure CDN Standard из** профилей Akamai. 
>

[!INCLUDE [cdn-front-door-allowed-ca](../../includes/cdn-front-door-allowed-ca.md)]

