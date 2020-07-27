---
title: Ограничение доступа на импорт и экспорт управляемых дисков с помощью Приватного канала (предварительная версия) — Azure CLI
description: Включите Приватный канал (предварительная версия) для управляемых дисков с помощью Azure CLI. Так вы сможете безопасно экспортировать и импортировать диски в пределах вашей виртуальной сети.
author: roygara
ms.service: virtual-machines
ms.topic: overview
ms.date: 07/15/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 9184dc78f0f9f8d7997e0bb64bc4521e19364cfe
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86535589"
---
# <a name="azure-cli---restrict-importexport-access-for-managed-disks-with-private-links-preview"></a>Ограничение доступа на импорт и экспорт управляемых дисков с помощью Приватного канала (предварительная версия) — Azure CLI

Вы можете использовать [частные конечные точки](../../private-link/private-endpoint-overview.md) (предварительная версия), чтобы ограничить доступ на экспорт и импорт управляемых дисков и получить защищенный доступ к данным через [Приватный канал](../../private-link/private-link-overview.md) из клиентов в виртуальной сети Azure. Частная конечная точка использует для службы управляемых дисков IP-адрес из адресного пространства виртуальной сети. Сетевой трафик между клиентами в виртуальной сети и управляемыми дисками проходит через виртуальную сеть и приватный канал в магистральной сети Майкрософт, что позволяет избежать рисков, связанных с использованием общедоступного Интернета. 

Чтобы использовать Приватные каналы для экспорта и импорта управляемых дисков, сначала нужно создать ресурс доступа к диску и связать его с виртуальной сетью в той же подписке, создав частную конечную точку. Затем свяжите диск или моментальный снимок с экземпляром ресурса доступа к диску. Наконец, задайте для диска или моментального снимка свойство NetworkAccessPolicy со значением `AllowPrivate`. Это ограничит доступ к виртуальной сети. 

Можно задать для свойства NetworkAccessPolicy значение `DenyAll`, чтобы полностью запретить экспорт данных диска или моментального снимка. Для свойства NetworkAccessPolicy по умолчанию используется значение `AllowAll`.

## <a name="limitations"></a>Ограничения

[!INCLUDE [virtual-machines-disks-private-links-limitations](../../../includes/virtual-machines-disks-private-links-limitations.md)]

## <a name="regional-availability"></a>Доступность по регионам

[!INCLUDE [virtual-machines-disks-private-links-regions](../../../includes/virtual-machines-disks-private-links-regions.md)]

## <a name="prerequisites"></a>Предварительные требования

Чтобы использовать частные конечные точки для экспорта и импорта управляемых дисков, в подписке должна быть включена эта возможность. Для этого отправьте электронное письмо на адрес mdprivatelinks@microsoft.com и укажите идентификаторы нужных подписок.

## <a name="log-in-into-your-subscription-and-set-your-variables"></a>Вход в подписку и настройка переменных

```azurecli-interactive

subscriptionId=yourSubscriptionId
resourceGroupName=yourResourceGroupName
region=CentralUSEUAP
diskAccessName=yourDiskAccessForPrivateLinks
vnetName=yourVNETForPrivateLinks
subnetName=yourSubnetForPrivateLinks
privateEndPointName=yourPrivateLinkForSecureMDExportImport
privateEndPointConnectionName=yourPrivateLinkConnection

#The name of an existing disk which is the source of the snapshot
sourceDiskName=yourSourceDiskForSnapshot

#The name of the new snapshot which will be secured via Private Links
snapshotNameSecuredWithPL=yourSnapshotNameSecuredWithPL

az login

az account set --subscription $subscriptionId

```

## <a name="create-a-disk-access-using-azure-cli"></a>Создание доступа к диску с помощью Azure CLI
```azurecli-interactive
az group deployment create -g $resourceGroupName \
--template-uri "https://raw.githubusercontent.com/ramankumarlive/manageddisksprivatelinks/master/CreateDiskAccess.json" \
--parameters "region=$region" "diskAccessName=$diskAccessName"

diskAccessId=$(az resource show -n $diskAccessName -g $resourceGroupName --namespace Microsoft.Compute --resource-type diskAccesses --query [id] -o tsv)
```

## <a name="create-a-virtual-network"></a>Создайте виртуальную сеть

Для частных конечных точек не поддерживаются сетевые политики, например группы безопасности сети (NSG). Чтобы развернуть частную конечную точку в подсети, для этой подсети необходимо явным образом указать параметр отключения. 

```azurecli-interactive
az network vnet create --resource-group $resourceGroupName \
    --name $vnetName \
    --subnet-name $subnetName
```
## <a name="disable-subnet-private-endpoint-policies"></a>Отключение политик подсети частной конечной точки

Так как Azure развертывает ресурсы в подсети виртуальной сети, вам нужно в параметрах подсети отключить политики частной конечной точки. 

```azurecli-interactive
az network vnet subnet update --resource-group $resourceGroupName \
    --name $subnetName  \
    --vnet-name $vnetName \
    --disable-private-endpoint-network-policies true
```
## <a name="create-a-private-endpoint-for-the-disk-access-object"></a>Создание частной конечной точки для объекта доступа к диску

```azurecli-interactive
az network private-endpoint create --resource-group $resourceGroupName \
    --name $privateEndPointName \
    --vnet-name $vnetName  \
    --subnet $subnetName \
    --private-connection-resource-id $diskAccessId \
    --group-ids disks \
    --connection-name $privateEndPointConnectionName
```

## <a name="configure-the-private-dns-zone"></a>Настройка частной зоны DNS

Создайте Частную зону DNS для домена большого двоичного объекта хранилища, установите связь с виртуальной сетью и создайте группу зоны DNS, чтобы связать частную конечную точку с Частной зоной DNS. 

```azurecli-interactive
az network private-dns zone create --resource-group $resourceGroupName \ 
    --name "privatelink.blob.core.windows.net"

az network private-dns link vnet create --resource-group $resourceGroupName \
    --zone-name "privatelink.blob.core.windows.net" \
    --name yourDNSLink \
    --virtual-network $vnetName \
    --registration-enabled false 

az network private-endpoint dns-zone-group create \
   --resource-group $resourceGroupName \
   --endpoint-name $privateEndPointName \
   --name yourZoneGroup \
   --private-dns-zone "privatelink.blob.core.windows.net" \
   --zone-name disks
```
## <a name="create-a-snapshot-of-a-disk-protected-with-private-links"></a>Создание моментального снимка диска, защищенного с помощью Приватных каналов
   ```cli
   diskId=$(az disk show -n $sourceDiskName -g $resourceGroupName --query [id] -o tsv)
   
   az group deployment create -g $resourceGroupName \
      --template-uri "https://raw.githubusercontent.com/ramankumarlive/manageddisksprivatelinks/master/CreateSnapshotWithExportViaPrivateLink.json" \
      --parameters "snapshotNameSecuredWithPL=$snapshotNameSecuredWithPL" \
      "sourceResourceId=$diskId" \
      "diskAccessId=$diskAccessId" \
      "region=$region" \
      "networkAccessPolicy=AllowPrivate" 
```

## <a name="next-steps"></a>Дальнейшие действия

- [Вопросы и ответы о Приватных каналах](faq-for-disks.md#private-links-for-securely-exporting-and-importing-managed-disks)
- [Экспорт или копирование управляемых моментальных снимков в виде VHD в учетную запись хранения в другом регионе с помощью PowerShell](../scripts/virtual-machines-windows-powershell-sample-copy-snapshot-to-storage-account.md)
