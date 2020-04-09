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
ms.openlocfilehash: 81d2209c4b76db685e5a8d2625c84469d5c3dc43
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985853"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-cdn"></a>Разрешенные центры сертификации для включения настраиваемого HTTPS в Azure CDN

При [включании функции HTTPS с помощью собственного сертификата](cdn-custom-ssl.md?tabs=option-2-enable-https-with-your-own-certificate#tlsssl-certificates) для пользовательского домена Azure Content Delivery Network (CDN) необходимо соответствовать определенным требованиям сертификата. **Стандарт Azure CDN от** профиля Microsoft требует сертификат от одного из утвержденных органов сертификата (CA) в следующем списке. Если сертификат из неутвержденного ЦС или если используется сертификат самоподписанных, запрос отклоняется. **Стандарт Azure CDN от Verizon** и **Azure CDN Premium из** профилей Verizon принимают любой действительный сертификат от любого действительного CA.

> [!NOTE]
> Возможность использования собственного сертификата для включения пользовательского домена HTTPS *недоступна* для **Azure CDN Standard из** профилей Akamai. 
>

[!INCLUDE [cdn-front-door-allowed-ca](../../includes/cdn-front-door-allowed-ca.md)]

