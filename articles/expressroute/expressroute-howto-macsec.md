---
title: 'Azure ExpressRoute: Налажнете MACsec'
description: Эта статья поможет настроить MACsec для обеспечения связи между маршрутизаторами и маршрутизаторами Microsoft.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: cherylmc
ms.openlocfilehash: 626302845dfb4b19deb921675601818b35ab8edb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74083547"
---
# <a name="configure-macsec-on-expressroute-direct-ports"></a>Направьте MACsec на прямые порты ExpressRoute

Эта статья поможет настроить MACsec для обеспечения связи между маршрутизаторами края и маршрутизаторами Microsoft с помощью PowerShell.

## <a name="before-you-begin"></a>Перед началом

Прежде чем начать настройку, проверьте следующее:

* Вы [понимаете, ExpressRoute Прямая подготовка рабочих процессов](expressroute-erdirect-about.md).
* Вы создали [ресурс порта ExpressRoute Direct.](expressroute-howto-erdirect.md)
* Если вы хотите запустить PowerShell локально, убедитесь, что последняя версия Azure PowerShell установлена на вашем компьютере.

### <a name="working-with-azure-powershell"></a>Работа с Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

### <a name="sign-in-and-select-the-right-subscription"></a>Вопийте и выберите правильную подписку

Чтобы запустить конфигурацию, вопийте в учетную запись Azure и выберите подписку, которую вы хотите использовать.

   [!INCLUDE [sign in](../../includes/expressroute-cloud-shell-connect.md)]

## <a name="1-create-azure-key-vault-macsec-secrets-and-user-identity"></a>1. Создайте Убежище ключей Azure, секреты MACsec и идентификацию пользователя

1. Создайте экземпляр Key Vault для хранения секретов MACsec в новой группе ресурсов.

    ```azurepowershell-interactive
    New-AzResourceGroup -Name "your_resource_group" -Location "resource_location"
    $keyVault = New-AzKeyVault -Name "your_key_vault_name" -ResourceGroupName "your_resource_group" -Location "resource_location" -EnableSoftDelete 
    ```

    Если у вас уже есть хранилище ключей или группа ресурсов, вы можете повторно использовать их. Тем не менее, очень важно включить [функцию **«мягкого удаления»** ](../key-vault/key-vault-ovw-soft-delete.md) в существующем хранилище ключей. Если мягкое удаление не включено, можно использовать следующие команды для включения:

    ```azurepowershell-interactive
    ($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "your_existing_keyvault").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"
    Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
    ```
2. Создание иждивественности пользователя.

    ```azurepowershell-interactive
    $identity = New-AzUserAssignedIdentity  -Name "identity_name" -Location "resource_location" -ResourceGroupName "your_resource_group"
    ```

    Если New-AzUserAssignedIdentity не признан действительным PowerShell cmdlet, установите следующий модуль (в режиме администратора) и перезапустите приведенную выше команду.

    ```azurepowershell-interactive
    Install-Module -Name Az.ManagedServiceIdentity
    ```
3. Создайте ключ ассоциации подключения (CAK) и ключевое имя ассоциации подключения (CKN) и храните их в хранилище ключей.

    ```azurepowershell-interactive
    $CAK = ConvertTo-SecureString "your_key" -AsPlainText -Force
    $CKN = ConvertTo-SecureString "your_key_name" -AsPlainText -Force
    $MACsecCAKSecret = Set-AzKeyVaultSecret -VaultName "your_key_vault_name" -Name "CAK_name" -SecretValue $CAK
    $MACsecCKNSecret = Set-AzKeyVaultSecret -VaultName "your_key_vault_name" -Name "CKN_name" -SecretValue $CKN
    ```
4. Назначайте разрешение GET на идентификацию пользователя.

    ```azurepowershell-interactive
    Set-AzKeyVaultAccessPolicy -VaultName "your_key_vault_name" -PermissionsToSecrets get -ObjectId $identity.PrincipalId
    ```

   Теперь эта личность может получить секреты, например CAK и CKN, из хранилища ключей.
5. Установите этот иждивественность пользователя для использования ExpressRoute.

    ```azurepowershell-interactive
    $erIdentity = New-AzExpressRoutePortIdentity -UserAssignedIdentityId $identity.Id
    ```
 
## <a name="2-configure-macsec-on-expressroute-direct-ports"></a>2. Направьте MACsec на прямые порты ExpressRoute

### <a name="to-enable-macsec"></a>Для включения В нее MACsec

Каждый экземпляр ExpressRoute Direct имеет два физических порта. Вы можете включить MACsec на обоих портах в то же время или включить MACsec на одном порту за один раз. Это один порт в то время (путем переключения трафика в активный порт при обслуживании другого порта) может помочь свести к минимуму перерыв, если ваш ExpressRoute Direct уже в эксплуатации.

1. Установите секреты и шифр MACsec и связывай идентификацию пользователя с портом, чтобы код управления ExpressRoute мог при необходимости получить доступ к секретам MACsec.

    ```azurepowershell-interactive
    $erDirect = Get-AzExpressRoutePort -ResourceGroupName "your_resource_group" -Name "your_direct_port_name"
    $erDirect.Links[0]. MacSecConfig.CknSecretIdentifier = $MacSecCKNSecret.Id
    $erDirect.Links[0]. MacSecConfig.CakSecretIdentifier = $MacSecCAKSecret.Id
    $erDirect.Links[0]. MacSecConfig.Cipher = "GcmAes256"
    $erDirect.Links[1]. MacSecConfig.CknSecretIdentifier = $MacSecCKNSecret.Id
    $erDirect.Links[1]. MacSecConfig.CakSecretIdentifier = $MacSecCAKSecret.Id
    $erDirect.Links[1]. MacSecConfig.Cipher = "GcmAes256"
    $erDirect.identity = $erIdentity
    Set-AzExpressRoutePort -ExpressRoutePort $erDirect
    ```
2. (Необязательно) Если порты находятся в состоянии административного вниз, вы можете запустить следующие команды для создания портов.

    ```azurepowershell-interactive
    $erDirect = Get-AzExpressRoutePort -ResourceGroupName "your_resource_group" -Name "your_direct_port_name"
    $erDirect.Links[0].AdminState = "Enabled"
    $erDirect.Links[1].AdminState = "Enabled"
    Set-AzExpressRoutePort -ExpressRoutePort $erDirect
    ```

    На данный момент, MACsec включен на ExpressRoute Прямые порты на стороне Microsoft. Если вы еще не настроили его на устройствах края, вы можете приступить к настройке их с теми же секретами и шифром MACsec.

### <a name="to-disable-macsec"></a>Чтобы отключить MACsec

Если MACsec больше не желается в экземпляре ExpressRoute Direct, вы можете запустить следующие команды, чтобы отключить его.

```azurepowershell-interactive
$erDirect = Get-AzExpressRoutePort -ResourceGroupName "your_resource_group" -Name "your_direct_port_name"
$erDirect.Links[0]. MacSecConfig.CknSecretIdentifier = $null
$erDirect.Links[0]. MacSecConfig.CakSecretIdentifier = $null
$erDirect.Links[1]. MacSecConfig.CknSecretIdentifier = $null
$erDirect.Links[1]. MacSecConfig.CakSecretIdentifier = $null
$erDirect.identity = $null
Set-AzExpressRoutePort -ExpressRoutePort $erDirect
```

На данный момент MACsec отключен в портах ExpressRoute Direct на стороне Microsoft.

### <a name="test-connectivity"></a>Проверка подключения
После настройки MACsec (включая обновление ключа MACsec) в портах ExpressRoute Direct [проверьте,](expressroute-troubleshooting-expressroute-overview.md) запущены ли и запущены сеансы BGP цепей. Если у вас пока нет схемы в портах, пожалуйста, создайте одну первую и навяжните Azure Private Peering или Microsoft Peering of the circuit. Если MACsec неправильно настроен, включая несоответствие ключа MACsec, между вашими сетевыми устройствами и сетевыми устройствами Майкрософт, вы не увидите разрешение ARP на уровне 2 и создание BGP на уровне 3. Если все настроено правильно, вы должны увидеть маршруты BGP, рекламируемые правильно в обоих направлениях, и поток данных приложения соответственно через ExpressRoute.

## <a name="next-steps"></a>Дальнейшие действия
1. [Создание схемы ExpressRoute на ЭкспрессРут Прямая](expressroute-howto-erdirect.md)
2. [Подключение виртуальной сети к каналу ExpressRoute](expressroute-howto-linkvnet-arm.md).
3. [Проверить подключение ExpressRoute](expressroute-troubleshooting-expressroute-overview.md)
