---
title: Создание общего ресурса NFS — файлы Azure
description: Узнайте, как создать файловый ресурс Azure, который можно подключить с помощью протокола сетевой файловой системы.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 09/15/2020
ms.author: rogarana
ms.subservice: files
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: bf75537c0baf029bc3fc63e320f6290a1f41a524
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/27/2020
ms.locfileid: "92738841"
---
# <a name="how-to-create-an-nfs-share"></a>Создание общей папки NFS

Файловые ресурсы Azure — это полностью управляемые общие файловые ресурсы, которые находятся в облаке. Доступ к ним можно получить с помощью протокола SMB или протокола сетевой файловой системы (NFS). В этой статье рассматривается создание общей папки, использующей протокол NFS. Дополнительные сведения об этих протоколах см. в статье [протоколы файловых ресурсов Azure](storage-files-compare-protocols.md).

## <a name="limitations"></a>Ограничения

[!INCLUDE [files-nfs-limitations](../../../includes/files-nfs-limitations.md)]

### <a name="regional-availability"></a>Доступность по регионам

[!INCLUDE [files-nfs-regional-availability](../../../includes/files-nfs-regional-availability.md)]

## <a name="prerequisites"></a>Предварительные требования

- Создайте [учетную запись филестораже](storage-how-to-create-premium-fileshare.md).

    > [!IMPORTANT]
    > Доступ к общим папкам NFS можно получить только из доверенных сетей. Подключения к общему ресурсу NFS должны исходить из одного из следующих источников:

    - Либо [Создайте закрытую конечную точку](storage-files-networking-endpoints.md#create-a-private-endpoint) (рекомендуется), либо [Ограничьте доступ к общедоступной конечной точке](storage-files-networking-endpoints.md#restrict-public-endpoint-access).
    - [Настройте VPN-подключение типа "точка — сеть" (P2S) в Linux для использования с файлами Azure](storage-files-configure-p2s-vpn-linux.md).
    - [Настройте VPN типа "сеть — сеть" для использования с файлами Azure](storage-files-configure-s2s-vpn.md).
    - Настройте [ExpressRoute](../../expressroute/expressroute-introduction.md).
- Если вы хотите использовать Azure CLI, [установите последнюю версию](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="register-the-nfs-41-protocol"></a>Регистрация протокола NFS 4,1

Если вы используете модуль Azure PowerShell или Azure CLI, зарегистрируйте функцию с помощью следующих команд:

### <a name="powershell"></a>PowerShell

```azurepowershell
Connect-AzAccount
$context = Get-AzSubscription -SubscriptionId <yourSubscriptionIDHere>
Set-AzContext $context
Register-AzProviderFeature -FeatureName AllowNfsFileShares -ProviderNamespace Microsoft.Storage
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```

### <a name="azure-cli"></a>Azure CLI

```azurecli
az login
az feature register --name AllowNfsFileShares \
                    --namespace Microsoft.Storage \
                    --subscription <yourSubscriptionIDHere>
az provider register --namespace Microsoft.Storage
```

## <a name="verify-that-the-feature-is-registered"></a>Убедитесь, что компонент зарегистрирован

Утверждение регистрации может занять до часа. Чтобы убедиться, что регистрация завершена, используйте следующие команды:

### <a name="powershell"></a>PowerShell

```azurepowershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage -FeatureName AllowNfsFileShares
```

### <a name="azure-cli"></a>Azure CLI

```azurecli
az feature show --name AllowNfsFileShares --namespace Microsoft.Storage --subscription <yourSubscriptionIDHere>
```

## <a name="create-an-nfs-share"></a>Создание общей папки NFS

# <a name="portal"></a>[Портал](#tab/azure-portal)

Теперь, когда вы создали учетную запись Филестораже и настроили сеть, можно создать файловый ресурс NFS. Процесс аналогичен созданию общего ресурса SMB. при создании общей папки выбирается **NFS** вместо **SMB** .

1. Перейдите к своей учетной записи хранения и выберите **Общие папки** .
1. Выберите **+ Общая папка** , чтобы создать новый файловый ресурс.
1. Назовите общую папку и выберите подготовленную емкость.
1. В качестве **протокола** выберите **NFS (Предварительная версия)** .
1. Для выбора **корневого Squash** сделайте выбор.

    - Root Squash (по умолчанию) — доступ для удаленного суперпользователя (root) сопоставляется с UID (65534) и GID (65534).
    - Без корневого Squash — удаленный суперпользователь (root) получает доступ от имени привилегированного пользователя.
    - Все Squash — все пользователи сопоставлены с UID (65534) и GID (65534).
    
1. Нажмите кнопку **создания** .

    :::image type="content" source="media/storage-files-how-to-create-mount-nfs-shares/create-nfs-file-share.png" alt-text="Снимок экрана: колонка создания файлового ресурса":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Убедитесь, что платформа .NET Framework установлена. См. раздел [Download .NET Framework](https://dotnet.microsoft.com/download/dotnet-framework).
 
1. Убедитесь, что установленная версия PowerShell `5.1` или более поздняя, с помощью следующей команды.    

   ```powershell
   echo $PSVersionTable.PSVersion.ToString() 
   ```
    
   Сведения об обновлении версии PowerShell см. в разделе [обновление существующих Windows PowerShell](https://docs.microsoft.com/powershell/scripting/install/installing-windows-powershell?view=powershell-6#upgrading-existing-windows-powershell) .
    
1. Установите последнюю версию модуля PowershellGet.

   ```powershell
   install-Module PowerShellGet –Repository PSGallery –Force  
   ```

1. Закройте, а затем снова откройте консоль PowerShell.

1. Установите модуль просмотра **AZ. Storage** предварительной версии **2.5.2-Preview** .

   ```powershell
   Install-Module Az.Storage -Repository PsGallery -RequiredVersion 2.5.2-preview -AllowClobber -AllowPrerelease -Force  
   ```

   Дополнительные сведения об установке модулей PowerShell см. [в статье Установка модуля Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.0.0) .
   
1. Чтобы создать файловый ресурс уровня "Премиум" с помощью модуля Azure PowerShell, используйте командлет [New-азрмсторажешаре](/powershell/module/az.storage/new-azrmstorageshare) .

> [!NOTE]
> Подготовленные размеры общих ресурсов задаются квотой общего доступа. Общие ресурсы выставляются по подготовленному размеру. Дополнительные сведения см. на [странице с расценками](https://azure.microsoft.com/pricing/details/storage/files/).

  ```powershell
  New-AzRmStorageShare `
   -ResourceGroupName $resourceGroupName `
   -StorageAccountName $storageAccountName `
   -Name myshare `
   -EnabledProtocol NFS `
   -RootSquash RootSquash `
   -Context $storageAcct.Context
  ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Чтобы создать общую папку уровня "Премиум" с помощью Azure CLI, используйте команду [AZ Storage Share Create](/cli/azure/storage/share-rm) .

> [!NOTE]
> Подготовленные размеры общих ресурсов задаются квотой общего доступа. Общие ресурсы выставляются по подготовленному размеру. Дополнительные сведения см. на [странице с расценками](https://azure.microsoft.com/pricing/details/storage/files/).

```azurecli-interactive
az storage share-rm create \
    --storage-account $STORAGEACCT \
    --enabled-protocol NFS \
    --root-squash RootSquash \
    --name "myshare" 
```
---

## <a name="next-steps"></a>Дальнейшие действия

Теперь, когда вы создали общий ресурс NFS, для его использования необходимо подключить его к клиенту Linux. Дополнительные сведения см. [в разделе Подключение общего ресурса NFS](storage-files-how-to-mount-nfs-shares.md).

Если возникнут проблемы, см. раздел [Устранение неполадок файловых ресурсов NFS Azure](storage-troubleshooting-files-nfs.md).
