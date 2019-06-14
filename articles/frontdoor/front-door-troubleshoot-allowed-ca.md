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
ms.openlocfilehash: 5cf94079dcd68887d9725ffbe9124f9b6c897dd0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60736162"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-front-door-service"></a>Разрешенные центры сертификации для включения настраиваемого протокола HTTPS в Azure Front Door Service

Если [включена функция HTTPS с использованием своего сертификата](front-door-custom-domain-https.md?tabs=option-2-enable-https-with-your-own-certificate), то для личного домена Azure Front Door Service нужно создать SSL-сертификат с помощью разрешенного центра сертификации (ЦС). В противном случае при использовании недопустимого ЦС или самозаверяющего сертификата ваш запрос будет отклонен.

При создании собственного сертификата разрешены следующие ЦС:

- AddTrust External CA Root
- Корневой ЦС AlphaSSL
- ЦС 01 AME Infra
- ЦС 02 AME Infra
- Ameroot
- AP Root CA
- AP Root Certificate Authority 2013
- AP Root Certificate Authority 2014
- APCA-DM3P
- Autopilot Root CA
- Baltimore CyberTrust Root
- Class 3 Public Primary Certification Authority
- COMODO RSA Certification Authority
- COMODO RSA Domain Validation Secure Server CA
- D-TRUST Root Class 3 CA 2 2009
- DigiCert Cloud Services CA-1
- DigiCert Global Root CA
- DigiCert High Assurance CA-3
- DigiCert High Assurance EV Root CA
- ЦС расширенной проверки сервера DigiCert SHA2
- DigiCert SHA2 High Assurance Server CA
- DigiCert SHA2 Secure Server CA
- Корневой ЦС X3 DST
- Корневой ЦС 2 класса 3 D-trust 2009
- ЦС Encryption Everywhere DV TLS
- Корневой ЦС Entrust
- Корневой ЦС Entrust — G2
- ЦС Entrust.net (2048)
- Глобальный ЦС GeoTrust
- Основной ЦС GeoTrust
- Основной ЦС GeoTrust — G2
- ЦС Geotrust RSA 2018
- GlobalSign;
- GlobalSign Extended Validation CA - SHA256 - G2
- GlobalSign Organization Validation CA - G2
- GlobalSign Root CA
- Корневой ЦС Go Daddy — G2
- Защищенный ЦС Go Daddy — G2
- ЦС RSA RapidSSL 2018
- Root Agency
- Symantec Class 3 EV SSL CA - G3
- Symantec Class 3 Secure Server CA - G4
- Symantec Enterprise Mobile Root for Microsoft
- Основной корневой ЦС Thawte
- Основной корневой ЦС Thawte — G2
- Основной корневой ЦС Thawte — G3
- ЦС Thawte RSA 2018
- Thawte Timestamping CA
- ЦС TrustAsia TLS RSA
- VeriSign Class 3 Extended Validation SSL CA
- VeriSign Class 3 Extended Validation SSL SGC CA
- VeriSign Class 3 Public Primary Certification Authority - G5
- VeriSign International Server CA - Class 3
- VeriSign Time Stamping Service Root
- VeriSign Universal Root Certification Authority
