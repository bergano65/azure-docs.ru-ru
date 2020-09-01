---
title: Настройка TLS 1,2 на клиентах Windows, обращающихся к Azure Stack пограничному устройству GPU
description: Описание настройки TLS 1,2 на клиентах Windows, обращающихся к Azure Stack пограничному устройству GPU.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 08/28/2020
ms.author: alkohli
ms.openlocfilehash: fdd88a902e812c89270a175aef4bf0de197c6e9b
ms.sourcegitcommit: d7352c07708180a9293e8a0e7020b9dd3dd153ce
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/30/2020
ms.locfileid: "89146136"
---
# <a name="configure-tls-12-on-windows-clients-accessing-azure-stack-edge-device"></a>Настройка TLS 1,2 на клиентах Windows, обращающихся к Azure Stack пограничному устройству

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

Если вы используете клиент Windows для доступа к устройству Azure Stack пограничным, вам потребуется настроить TLS 1,2 на клиенте. В этой статье содержатся ресурсы и рекомендации по настройке TLS 1,2 в клиенте Windows. 

Приведенные здесь рекомендации основаны на тестировании, выполняемом на клиенте под управлением Windows Server 2016.

## <a name="configure-tls-12-for-current-powershell-session"></a>Настройка TLS 1,2 для текущего сеанса PowerShell

Выполните следующие действия, чтобы настроить TLS 1,2 на клиенте.

1. Запустите оболочку PowerShell от имени администратора.
2. Чтобы задать TLS 1,2 для текущего сеанса PowerShell, введите:
  
    ```azurepowershell
    $TLS12Protocol = [System.Net.SecurityProtocolType] 'Ssl3 , Tls12'
    [System.Net.ServicePointManager]::SecurityProtocol = $TLS12Protocol
    ```
## <a name="configure-tls-12-on-client"></a>Настройка TLS 1,2 на клиенте

Если вы хотите установить TLS 1,2 для своей среды, следуйте указаниям в следующих документах:

- [Общие сведения о включении TLS 1,2](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings#tls-12)
- [Включение TLS 1.2 на клиентах](https://docs.microsoft.com/configmgr/core/plan-design/security/enable-tls-1-2-client)
- [Включение TLS 1.2 на серверах сайта и удаленных системах сайта](https://docs.microsoft.com/configmgr/core/plan-design/security/enable-tls-1-2-server)
- [Протоколы в TLS/SSL (поставщик общих служб Schannel)](https://docs.microsoft.com/windows-server/security/tls/manage-tls#configuring-tls-ecc-curve-order)
- Комплекты [шифров](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings#tls-12). в частности, [для настройки порядка](https://docs.microsoft.com/windows-server/security/tls/manage-tls#configuring-tls-cipher-suite-order) комплектов шифров TLS убедитесь в том, что вы перечислите текущие комплекты шифров и не могли бы быть в следующем списке:

    - TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
    - TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
    - TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
    - TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384

    Эти наборы шифров также можно добавить, напрямую изменив параметры реестра.

    ```azurepowershell
    New-ItemProperty -Path "$HklmSoftwarePath\Policies\Microsoft\Cryptography\Configuration\SSL\00010002" -Name "Functions"  -PropertyType String -Value ("TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384, TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384, TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384,TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384")
    ```

- Как задать эллиптические кривые

    Убедитесь, что вы перечислите текущие кривые эллиптических кривых и в начале всех недостающих из следующего списка:

    - P-256 
    - P-384

    Можно также добавить эти эллиптические кривые, напрямую изменив параметры реестра.
    
    ```azurepowershell
    New-ItemProperty -Path "$HklmSoftwarePath\Policies\Microsoft\Cryptography\Configuration\SSL\00010002" -Name "EccCurves" -PropertyType MultiString -Value @("NistP256", "NistP384")
    ```
    
    - [Установите минимальный размер обмена ключами RSA равным 2048](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings#keyexchangealgorithm---client-rsa-key-sizes).



## <a name="next-steps"></a>Дальнейшие действия

[Подключение к Azure Resource Manager](azure-stack-edge-j-series-connect-resource-manager.md)