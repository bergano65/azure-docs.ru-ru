---
title: Разрешенные центры сертификации для включения настраиваемого протокола HTTPS в Azure Front Door Service | Документация Майкрософт
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
ms.openlocfilehash: e2bab9f9e1ae099952b34e66f7250e7ecbc8e689
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/22/2019
ms.locfileid: "67330755"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-front-door-service"></a>Разрешенные центры сертификации для включения настраиваемого протокола HTTPS в Azure Front Door Service

Если [включена функция HTTPS с использованием своего сертификата](front-door-custom-domain-https.md?tabs=option-2-enable-https-with-your-own-certificate), то для личного домена Azure Front Door Service нужно создать SSL-сертификат с помощью разрешенного центра сертификации (ЦС). В противном случае при использовании недопустимого ЦС или самозаверяющего сертификата ваш запрос будет отклонен.

[!INCLUDE [cdn-front-door-allowed-ca](../../includes/cdn-front-door-allowed-ca.md)]
