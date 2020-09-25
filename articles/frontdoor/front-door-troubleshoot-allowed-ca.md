---
title: Разрешенный ЦС для включения пользовательского HTTPS в передней дверце Azure
description: Если вы используете собственный сертификат для включения HTTPS в домене 0custom для передней дверцы Azure, для его создания необходимо использовать разрешенный центр сертификации (ЦС).
services: frontdoor
documentationcenter: ''
author: duongau
ms.assetid: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/23/2018
ms.author: duau
ms.openlocfilehash: 973df2505eefc2a46aa105b874f32b61fe6e8b36
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91269813"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-front-door"></a>Разрешенные центры сертификации для включения пользовательского HTTPS в передней дверце Azure

Если вы [включили функцию HTTPS с помощью собственного сертификата](front-door-custom-domain-https.md?tabs=option-2-enable-https-with-your-own-certificate), то для пользовательского домена "Передняя дверца Azure" необходимо использовать разрешенный центр сертификации (ЦС) для создания сертификата TLS/SSL. В противном случае при использовании недопустимого ЦС или самозаверяющего сертификата ваш запрос будет отклонен.

[!INCLUDE [cdn-front-door-allowed-ca](../../includes/cdn-front-door-allowed-ca.md)]
