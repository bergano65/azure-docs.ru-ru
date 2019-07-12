---
title: Разрешенные центры сертификации для включения настраиваемого HTTPS в Azure CDN | Документация Майкрософт
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
ms.openlocfilehash: 754941163ddce9512870f0b76a96207472e5b2aa
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/05/2019
ms.locfileid: "67593364"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-cdn"></a>Разрешенные центры сертификации для включения настраиваемого HTTPS в Azure CDN

Для личного домена сети доставки содержимого Azure (CDN) в конечной точке **Azure CDN уровня "Стандартный" от Майкрософт**, когда вы [включите функцию HTTPS, используя свой собственный сертификат](cdn-custom-ssl.md?tabs=option-2-enable-https-with-your-own-certificate#ssl-certificates), нужно создать SSL-сертификат с помощью разрешенного центра сертификации (ЦС). В противном случае при использовании недопустимого ЦС или самозаверяющего сертификата ваш запрос будет отклонен.

> [!NOTE]
> Возможность использования собственного сертификата для включения пользовательского HTTPS доступна только с профилем **Azure CDN уровня "Стандартный" от Майкрософт**. 
>

[!INCLUDE [cdn-front-door-allowed-ca](../../includes/cdn-front-door-allowed-ca.md)]

