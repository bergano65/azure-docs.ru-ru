---
title: Разрешенный ЦС для включения пользовательского HTTPS в передней дверце Azure
description: Если вы используете собственный сертификат, чтобы включить протокол HTTPS в пользовательском домене передней дверцы Azure, для его создания необходимо использовать разрешенный центр сертификации (ЦС).
services: frontdoor
documentationcenter: ''
author: duongau
ms.assetid: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/30/2020
ms.author: duau
ms.openlocfilehash: 20c5d611272ee2159ce8ddcc2865797a225a7ebb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91613685"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-front-door"></a>Разрешенные центры сертификации для включения пользовательского HTTPS в передней дверце Azure

При [включении функции HTTPS с помощью собственного сертификата](front-door-custom-domain-https.md?tabs=option-2-enable-https-with-your-own-certificate) для пользовательского домена передней дверцы Azure. Для создания сертификата TLS/SSL требуется разрешенный центр сертификации (ЦС). В противном случае при использовании недопустимого ЦС или самозаверяющего сертификата ваш запрос будет отклонен.

[!INCLUDE [cdn-front-door-allowed-ca](../../includes/cdn-front-door-allowed-ca.md)]
