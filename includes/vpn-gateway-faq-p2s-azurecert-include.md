---
title: Включить имя файла
description: включить файл
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 08/14/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: f322803d3484b4ec2d5449e19d67d75b35d6d92f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "75751985"
---
[!INCLUDE [P2S FAQ All](vpn-gateway-faq-p2s-all-include.md)]

### <a name="what-should-i-do-if-im-getting-a-certificate-mismatch-when-connecting-using-certificate-authentication"></a>Что делать, если при подключении с использованием проверки подлинности сертификата возникло несоответствие сертификата?

Снимите флажок **"проверять удостоверение сервера путем проверки сертификата"** или **Добавьте полное доменное имя сервера вместе с сертификатом** при создании профиля вручную. Это можно сделать, запустив **Rasphone** из командной строки и выбрав профиль из раскрывающегося списка.

Обход проверки удостоверения сервера не рекомендуется в целом, но при использовании проверки подлинности сертификата Azure для проверки сервера в протоколе туннелирования VPN (IKEv2/SSTP) и протокола EAP используется один и тот же сертификат. Так как сертификат сервера и полное доменное имя уже проверены протоколом туннелирования VPN, он становится избыточным для повторной проверки в EAP.

![точка-сеть](./media/vpn-gateway-faq-p2s-all-include/servercert.png "Сертификат сервера")

### <a name="can-i-use-my-own-internal-pki-root-ca-to-generate-certificates-for-point-to-site-connectivity"></a>Можно ли использовать собственный внутренний корневой ЦС PKI для создания сертификатов для подключения "точка — сеть"?

Да. Раньше поддерживались только самозаверяющие корневые сертификаты. Вы по-прежнему можете загружать до 20 корневых сертификатов.

### <a name="can-i-use-certificates-from-azure-key-vault"></a>Можно ли использовать сертификаты из Azure Key Vault?

Нет.

### <a name="what-tools-can-i-use-to-create-certificates"></a>Какие инструменты можно использовать для создания сертификатов?

Можно использовать решение Enterprise PKI (внутренняя инфраструктура открытых ключей), Azure PowerShell, MakeCert и OpenSSL.

### <a name="are-there-instructions-for-certificate-settings-and-parameters"></a><a name="certsettings"></a>Есть инструкции по выбору параметров сертификата?

* **Внутренняя инфраструктура открытых ключей и решение Enterprise PKI:** см. инструкции по [созданию сертификатов](../articles/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#generatecert).

* **Azure PowerShell:** см. инструкции по работе с [Azure PowerShell](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md).

* **MakeCert:** см. инструкции по работе с [MakeCert](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-makecert.md).

* **OpenSSL** 

    * При экспорте сертификатов преобразуйте корневой сертификат в кодировку Base64.

    * Для сертификата клиента:

      * При создании закрытого ключа укажите длину 4096.
      * При создании сертификата для параметра *-extensions* укажите значение *usr_cert*.
