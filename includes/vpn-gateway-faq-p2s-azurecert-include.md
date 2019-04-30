---
title: включение файла
description: включение файла
services: vpn-gateway
author: WenJason
ms.service: vpn-gateway
ms.topic: include
origin.date: 03/21/2018
ms.date: 12/24/2018
ms.author: v-jay
ms.custom: include file
ms.openlocfilehash: e2e91dc91cf0fbe6827808785a4c3cc25b06542b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60320254"
---
[!INCLUDE [P2S FAQ All](vpn-gateway-faq-p2s-all-include.md)]

### <a name="can-i-use-my-own-internal-pki-root-ca-for-point-to-site-connectivity"></a>Можно ли использовать корневой ЦС собственной внутренней системы PKI для подключения "точка — сеть"?

Да. Раньше поддерживались только самозаверяющие корневые сертификаты. Вы по-прежнему можете загружать до 20 корневых сертификатов.

### <a name="what-tools-can-i-use-to-create-certificates"></a>Какие инструменты можно использовать для создания сертификатов?

Можно использовать решение Enterprise PKI (внутренняя инфраструктура открытых ключей), Azure PowerShell, MakeCert и OpenSSL.

### <a name="certsettings"></a>Есть инструкции по выбору параметров сертификата?

* **Внутренняя PKI и решение Enterprise PKI:** Описывается, как [сертификаты](../articles/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#generatecert).

* **Azure PowerShell**. См. в разделе [Azure PowerShell](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md) инструкции.

* **MakeCert:** См. в разделе [MakeCert](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-makecert.md) инструкции.

* **OpenSSL:** 

    * При экспорте сертификатов преобразуйте корневой сертификат в кодировку Base64.

    * Для сертификата клиента:

      * При создании закрытого ключа укажите длину 4096.
      * При создании сертификата для параметра *-extensions* укажите значение *usr_cert*.