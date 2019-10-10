---
title: 'Настройка Максек-ExpressRoute: Azure | Документация Майкрософт'
description: Эта статья поможет вам настроить Максек для защиты подключений между граничным маршрутизатором и граничным маршрутизатором Майкрософт.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: cherylmc
ms.openlocfilehash: eeaa709b88ca795d906fe3688301b4cd7d8c726e
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/10/2019
ms.locfileid: "72244132"
---
# <a name="configure-macsec-on-expressroute-direct-ports"></a>Настройка Максек для прямого порта ExpressRoute

Эта статья поможет вам настроить Максек для защиты подключений между граничным маршрутизатором и граничным маршрутизатором Майкрософт с помощью PowerShell.

## <a name="before-you-begin"></a>Перед началом работы

Прежде чем начать настройку, проверьте следующее:

* Вы понимаете [рабочие процессы прямой подготовки ExpressRoute](expressroute-erdirect-about.md).
* Вы создали [ресурс прямого порта ExpressRoute](expressroute-howto-erdirect.md).
* Если вы хотите запустить PowerShell локально, убедитесь, что на компьютере установлена последняя версия Azure PowerShell.

### <a name="working-with-azure-powershell"></a>Работа с Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

### <a name="sign-in-and-select-the-right-subscription"></a>Войдите и выберите подходящую подписку.

Чтобы начать настройку, войдите в учетную запись Azure и выберите подписку, которую хотите использовать.

   [!INCLUDE [sign in](../../includes/expressroute-cloud-shell-connect.md)]

## <a name="1-create-azure-key-vault-macsec-secrets-and-user-identity"></a>1. Создание Azure Key Vault, секретов Максек и удостоверения пользователя

1. Создайте Key Vault экземпляр для хранения секретов Максек в новой группе ресурсов.

    ```azurepowershell-interactive
    New-AzResourceGroup -Name "your_resource_group" -Location "resource_location"
    $keyVault = New-AzKeyVault -Name "your_key_vault_name" -ResourceGroupName "your_resource_group" -Location "resource_location" -EnableSoftDelete 
    ```

    Если у вас уже есть хранилище ключей или группа ресурсов, их можно использовать повторно. Однако очень важно включить [функцию **обратимого удаления** ](https://docs.microsoft.com/en-us/azure/key-vault/key-vault-ovw-soft-delete) в существующем хранилище ключей. Если обратимое удаление не включено, для его включения можно использовать следующие команды:

    ```azurepowershell-interactive
    ($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "your_existing_keyvault").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"
    Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
    ```
2. Создайте удостоверение пользователя.

    ```azurepowershell-interactive
    $identity = New-AzUserAssignedIdentity  -Name "identity_name" -Location "resource_location" -ResourceGroupName "your_resource_group"
    ```

    Если New-Азусерассигнедидентити не распознан как допустимый командлет PowerShell, установите следующий модуль (в режиме администратора) и повторно выполните указанную выше команду.

    ```azurepowershell-interactive
    Install-Module -Name Az.ManagedServiceIdentity
    ```
3. Создайте ключ связи подключения (как) и имя ключа связи подключения (ККН) и сохраните их в хранилище ключей.

    ```azurepowershell-interactive
    $CAK = ConvertTo-SecureString "your_key" -AsPlainText -Force
    $CKN = ConvertTo-SecureString "your_key_name" -AsPlainText -Force
    $MACsecCAKSecret = Set-AzKeyVaultSecret -VaultName "your_key_vault_name" -Name "CAK_name" -SecretValue $CAK
    $MACsecCKNSecret = Set-AzKeyVaultSecret -VaultName "your_key_vault_name" -Name "CKN_name" -SecretValue $CKN
    ```
4. Назначьте разрешение GET для удостоверения пользователя.

    ```azurepowershell-interactive
    Set-AzKeyVaultAccessPolicy -VaultName "your_key_vault_name" -PermissionsToSecrets get -ObjectId $identity.PrincipalId
    ```

   Теперь это удостоверение может получить секреты, например как и ККН, из хранилища ключей.
5. Настройте этот идентификатор пользователя для использования ExpressRoute.

    ```azurepowershell-interactive
    $erIdentity = New-AzExpressRoutePortIdentity -UserAssignedIdentityId $identity.Id
    ```
 
## <a name="2-configure-macsec-on-expressroute-direct-ports"></a>2. Настройка Максек для прямого порта ExpressRoute

### <a name="to-enable-macsec"></a>Включение Максек

Каждый прямой экземпляр ExpressRoute имеет два физических порта. Вы можете включить Максек на обоих портах одновременно или включить Максек на одном порту за раз. Поочередное выполнение этого порта (путем переключения трафика на активный порт при обслуживании другого порта) может снизить перерыв в работе, если служба ExpressRoute Direct уже находится в службе.

1. Задайте секреты Максек и шифр и свяжите удостоверение пользователя с портом, чтобы код управления ExpressRoute мог получить доступ к секретам Максек при необходимости.

    ```azurepowershell-interactive
    $erDirect = Get-AzExpressRoutePort -ResourceGroupName "your_resource_group" -Name "your_direct_port_name"
    $erDirect.Links[0]. MacSecConfig.CknSecretIdentifier = $MacSecCKNSecret.Id
    $erDirect.Links[0]. MacSecConfig.CakSecretIdentifier = $MacSecCAKSecret.Id
    $erDirect.Links[0]. MacSecConfig.Cipher = "gcm-aes-128"
    $erDirect.Links[1]. MacSecConfig.CknSecretIdentifier = $MacSecCKNSecret.Id
    $erDirect.Links[1]. MacSecConfig.CakSecretIdentifier = $MacSecCAKSecret.Id
    $erDirect.Links[1]. MacSecConfig.Cipher = "gcm-aes-128"
    $erDirect.identity = $erIdentity
    Set-AzExpressRoutePort -ExpressRoutePort $erDirect
    ```
2. Используемых Если порты находятся в состоянии администрирования, можно выполнить следующие команды, чтобы отобразить порты.

    ```azurepowershell-interactive
    $erDirect = Get-AzExpressRoutePort -ResourceGroupName "your_resource_group" -Name "your_direct_port_name"
    $erDirect.Links[0].AdminState = “Enabled”
    $erDirect.Links[1].AdminState = “Enabled”
    Set-AzExpressRoutePort -ExpressRoutePort $erDirect
    ```

    На этом этапе Максек включен на непосредственных портах ExpressRoute на стороне Майкрософт. Если вы еще не настроили его на пограничных устройствах, можно приступить к настройке с помощью одних и тех же секретов и шифров Максек.

### <a name="to-disable-macsec"></a>Отключение Максек

Если в экземпляре ExpressRoute Direct больше не требуется Максек, можно выполнить следующие команды, чтобы отключить ее.

```azurepowershell-interactive
$erDirect = Get-AzExpressRoutePort -ResourceGroupName "your_resource_group" -Name "your_direct_port_name"
$erDirect.Links[0]. MacSecConfig.CknSecretIdentifier = $null
$erDirect.Links[0]. MacSecConfig.CakSecretIdentifier = $null
$erDirect.Links[1]. MacSecConfig.CknSecretIdentifier = $null
$erDirect.Links[1]. MacSecConfig.CakSecretIdentifier = $null
$erDirect.identity = $null
Set-AzExpressRoutePort -ExpressRoutePort $erDirect
```

На этом этапе Максек отключен на непосредственных портах ExpressRoute на стороне Майкрософт.

### <a name="test-connectivity"></a>Проверка подключения
После настройки Максек (включая обновление ключа Максек) на портах с прямым подключением ExpressRoute [Проверьте](expressroute-troubleshooting-expressroute-overview.md) , работают ли сеансы BGP для каналов. Если у вас еще нет канала, создайте его сначала и настройте частный пиринг Azure или пиринг Майкрософт. Если Максек настроен неправильно, в том числе несовпадение ключей Максек, между сетевыми устройствами и сетевыми устройствами Майкрософт, разрешение ARP не будет отображаться на уровне 2 и на компьютере с установленным BGP на уровне 3. Если все настроено правильно, вы должны увидеть маршруты BGP, объявляемые правильно в обоих направлениях, и поток данных приложения в соответствии с ExpressRoute.

## <a name="next-steps"></a>Следующие шаги
1. [Создание канала ExpressRoute в ExpressRoute Direct](expressroute-howto-erdirect.md)
2. [Подключение виртуальной сети к каналу ExpressRoute](expressroute-howto-linkvnet-arm.md).
3. [Проверка подключения ExpressRoute](expressroute-troubleshooting-expressroute-overview.md).
