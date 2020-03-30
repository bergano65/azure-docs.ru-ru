---
title: Ограничьте эксфильтрацию данных для хранения Azure - Azure CLI
description: В этой статье вы узнаете, как ограничить и ограничить эксфильтрацию виртуальных сетевых данных ресурсами хранения azure с помощью политик идентимативных сетевых сетевых сетевых сетевых сетевых сетей с использованием Azure CLI.
services: virtual-network
documentationcenter: virtual-network
author: rdhillon
manager: ''
editor: ''
tags: azure-resource-manager
Customer intent: I want only specific Azure Storage account to be allowed access from a virtual network subnet.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: rdhillon
ms.custom: ''
ms.openlocfilehash: e01af052a936403162115965f2dc5b3ad46dd9cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78271189"
---
# <a name="manage-data-exfiltration-to-azure-storage-accounts-with-virtual-network-service-endpoint-policies-using-the-azure-cli"></a>Управление эксфильтрацией данных для учетных записей хранения Azure с помощью политик иного направления виртуальной сетевой системы с помощью Azure CLI

Политики виртуальной точки зрения сетевого обслуживания позволяют применять элементы управления доступом к учетным записям хранения Azure из виртуальной сети над конечными точками обслуживания. Это ключ к обеспечению безопасности рабочих нагрузок, управлению тем, какие учетные записи хранилища разрешены и где разрешена эксфильтрация данных.
Вы узнаете, как выполнять следующие задачи:

* Создайте виртуальную сеть и добавьте подсеть.
* Включить конечную точку обслуживания для хранения Azure.
* Создайте две учетные записи хранилища Azure и позвольте сети получить к нему доступ из созданной выше подсети.
* Создайте политику конечной точки службы, чтобы разрешить доступ только к одной из учетных записей хранилища.
* Развертывание виртуальной машины (VM) в подсеть.
* Подтвердите доступ к разрешенной учетной записи хранилища из подсети.
* Подтвердить доступ к неразрешенной учетной записи хранилища из подсети.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Если вы решили установить и использовать CLI локально, для выполнения инструкций из этого руководства вам потребуется Azure CLI 2.0.28 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам нужно установить или обновить, [см.]( /cli/azure/install-azure-cli) 

## <a name="create-a-virtual-network"></a>Создание виртуальной сети

Перед созданием виртуальной сети необходимо создать для нее группу ресурсов и другие компоненты, указанные в этой статье. Создайте группу ресурсов с помощью команды [az group create](/cli/azure/group). В следующем примере создается группа ресурсов с именем *myResourceGroup* в расположении *eastus*.

```azurecli-interactive
az group create \
  --name myResourceGroup \
  --location eastus
```

Создайте виртуальную сеть с одной подсетью при помощи команды [az network vnet create](/cli/azure/network/vnet).

```azurecli-interactive
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --address-prefix 10.0.0.0/16 \
  --subnet-name Private \
  --subnet-prefix 10.0.0.0/24
```

## <a name="enable-a-service-endpoint"></a>Включение конечной точки службы 

В этом примере для подсети *Private*создается конечная точка службы *для Microsoft.Storage:* 

```azurecli-interactive
az network vnet subnet create \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name Private \
  --address-prefix 10.0.0.0/24 \
  --service-endpoints Microsoft.Storage
```

## <a name="restrict-network-access-for-a-subnet"></a>Ограничение сетевого доступа для подсети

Создайте группу безопасности сети с помощью команды [az network nsg create](/cli/azure/network/nsg). В следующем примере создается группа безопасности сети *myNsgPrivate*.

```azurecli-interactive
az network nsg create \
  --resource-group myResourceGroup \
  --name myNsgPrivate
```

Чтобы привязать подсеть *Private* к этой группе безопасности сети, выполните команду [az network vnet subnet update](/cli/azure/network/vnet/subnet). В следующем примере подсеть *Private* привязывается к группе безопасности сети *myNsgPrivate*.

```azurecli-interactive
az network vnet subnet update \
  --vnet-name myVirtualNetwork \
  --name Private \
  --resource-group myResourceGroup \
  --network-security-group myNsgPrivate
```

Создайте правила безопасности командой [az network nsg rule create](/cli/azure/network/nsg/rule). Приведенное ниже правило разрешает исходящий доступ к общедоступным IP-адресам, назначенным службе хранилища Azure. 

```azurecli-interactive
az network nsg rule create \
  --resource-group myResourceGroup \
  --nsg-name myNsgPrivate \
  --name Allow-Storage-All \
  --access Allow \
  --protocol "*" \
  --direction Outbound \
  --priority 100 \
  --source-address-prefix "VirtualNetwork" \
  --source-port-range "*" \
  --destination-address-prefix "Storage" \
  --destination-port-range "*"
```

Каждая группа сетевой безопасности содержит несколько [правил безопасности по умолчанию.](security-overview.md#default-security-rules) Следующее правило перекрывает правило безопасности по умолчанию, которое позволяет доступ ко всем общедоступным IP-адресам. Опция `destination-address-prefix "Internet"` лишает исходящий доступ ко всем общедоступным IP-адресам. Предыдущее правило переопределяет это правило ввиду более высокого приоритета. Оно предоставляет доступ к общедоступным IP-адресам службы хранилища Azure.

```azurecli-interactive
az network nsg rule create \
  --resource-group myResourceGroup \
  --nsg-name myNsgPrivate \
  --name Deny-Internet-All \
  --access Deny \
  --protocol "*" \
  --direction Outbound \
  --priority 110 \
  --source-address-prefix "VirtualNetwork" \
  --source-port-range "*" \
  --destination-address-prefix "Internet" \
  --destination-port-range "*"
```

Следующее правило позволяет SSH трафик входящих в подсеть из любого места. Это правило переопределяет правило безопасности по умолчанию, запрещающее весь входящий трафик из Интернета. SSH допускается к подсети, так что подключение может быть проверено на более позднем этапе.

```azurecli-interactive
az network nsg rule create \
  --resource-group myResourceGroup \
  --nsg-name myNsgPrivate \
  --name Allow-SSH-All \
  --access Allow \
  --protocol Tcp \
  --direction Inbound \
  --priority 120 \
  --source-address-prefix "*" \
  --source-port-range "*" \
  --destination-address-prefix "VirtualNetwork" \
  --destination-port-range "22"
```

## <a name="restrict-network-access-to-azure-storage-accounts"></a>Ограничьте доступ к учетным записям хранения azure

В этом разделе перечислены шаги по ограничению доступа к сети для учетной записи хранилища Azure из данной подсети в виртуальной сети через конечную точку обслуживания.

### <a name="create-a-storage-account"></a>Создание учетной записи хранения

Создайте две учетные записи хранения Azure с [помощью учетной записи хранилища az.](/cli/azure/storage/account)

```azurecli-interactive
storageAcctName1="allowedstorageacc"

az storage account create \
  --name $storageAcctName1 \
  --resource-group myResourceGroup \
  --sku Standard_LRS \
  --kind StorageV2

storageAcctName2="notallowedstorageacc"

az storage account create \
  --name $storageAcctName2 \
  --resource-group myResourceGroup \
  --sku Standard_LRS \
  --kind StorageV2
```

После создания учетных записей хранилища извлеките строку соединения для учетных записей хранилища в переменную с помощью учетной записи [хранилища, шоу-соединения-строки.](/cli/azure/storage/account) Строка подключения используется для создания файлового ресурса на более позднем этапе.

```azurecli-interactive
saConnectionString1=$(az storage account show-connection-string \
  --name $storageAcctName1 \
  --resource-group myResourceGroup \
  --query 'connectionString' \
  --out tsv)

saConnectionString2=$(az storage account show-connection-string \
  --name $storageAcctName2 \
  --resource-group myResourceGroup \
  --query 'connectionString' \
  --out tsv)
```

<a name="account-key"></a>Просмотрите содержимое переменной и запишите значение параметра **AccountKey** из полученных выходных данных, так как он будет использоваться позже.

```azurecli-interactive
echo $saConnectionString1

echo $saConnectionString2
```

### <a name="create-a-file-share-in-the-storage-account"></a>Создание файлового ресурса в учетной записи хранения

Создайте файловый ресурс в учетной записи хранения командой [az storage share create](/cli/azure/storage/share). Позже этот файловый ресурс будет подключен для подтверждения сетевого доступа к нему.

```azurecli-interactive
az storage share create \
  --name my-file-share \
  --quota 2048 \
  --connection-string $saConnectionString1 > /dev/null

az storage share create \
  --name my-file-share \
  --quota 2048 \
  --connection-string $saConnectionString2 > /dev/null
```

### <a name="deny-all-network-access-to-the-storage-account"></a>Запретить доступ сети к учетной записи хранилища

По умолчанию учетные записи хранения принимают сетевые подключения клиентов в любой сети. Чтобы ограничить доступ выбранными сетями, измените действие по умолчанию на *Запретить*, выполнив команду [az storage account update](/cli/azure/storage/account). После запрещения сетевого доступа учетная запись хранения не будет доступна из любой сети.

```azurecli-interactive
az storage account update \
  --name $storageAcctName1 \
  --resource-group myResourceGroup \
  --default-action Deny

az storage account update \
  --name $storageAcctName2 \
  --resource-group myResourceGroup \
  --default-action Deny
```

### <a name="enable-network-access-from-virtual-network-subnet"></a>Включить доступ к сети из виртуальной сетевой подсети

Разрешите сетевой доступ к учетной записи хранения из подсети *Private*, выполнив команду [az storage account network-rule add](/cli/azure/storage/account/network-rule).

```azurecli-interactive
az storage account network-rule add \
  --resource-group myResourceGroup \
  --account-name $storageAcctName1 \
  --vnet-name myVirtualNetwork \
  --subnet Private

az storage account network-rule add \
  --resource-group myResourceGroup \
  --account-name $storageAcctName2 \
  --vnet-name myVirtualNetwork \
  --subnet Private
```

## <a name="apply-policy-to-allow-access-to-valid-storage-account"></a>Применить политику, чтобы разрешить доступ к действительной учетной записи хранилища

Политики конечных точек службы Azure доступны только для хранения Azure. Таким образом, мы будем включать в себя конечную точку службы *для Microsoft.Storage* в этой подсети для этой установки примера.

Политики конечных точек обслуживания применяются к конечным точкам службы. Начнем с создания политики конечных точек обслуживания. Затем мы создадим определения политики в этой политике для учетных записей Azure Storage, которые будут внесены в белый список для этой подсети

Создание политики конечной точки службы.

```azurecli-interactive
az network service-endpoint policy create \
  --resource-group myResourceGroup \
  --name mysepolicy \
  --location eastus
```

Сохранить ресурс URI для разрешенной учетной записи хранилища в переменной. Перед выполнением команды ниже замените * \<>* подписки на подписку на фактический значение идентификатора подписки.

```azurecli-interactive
$serviceResourceId="/subscriptions/<your-subscription-id>/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/allowedstorageacc"
```

Создайте & добавить определение политики для предоставления вышеуказанной учетной записи хранилища Azure в конечную точку службы

```azurecli-interactive
az network service-endpoint policy-definition create \
  --resource-group myResourceGroup \
  --policy-name mysepolicy \
  --name mypolicydefinition \
  --service "Microsoft.Storage" \
  --service-resources $serviceResourceId
```

И обновить виртуальную сетевую подсеть, чтобы связать с ней политику конечных точек обслуживания, созданную на предыдущем этапе

```azurecli-interactive
az network vnet subnet update \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name Private \
  --service-endpoints Microsoft.Storage \
  --service-endpoint-policy mysepolicy
```

## <a name="validate-access-restriction-to-azure-storage-accounts"></a>Проверка ограничения доступа к учетным записям хранения Azure

### <a name="create-the-virtual-machine"></a>Создание виртуальной машины

Чтобы протестировать доступ сети к учетной записи хранилища, разместите VM в подсети.

Создайте VM в *частной* подсети с [az vm create.](/cli/azure/vm) Также команда создает ключи SSH, если они не существуют в расположении ключей по умолчанию. Чтобы использовать определенный набор ключей, используйте параметр `--ssh-key-value`.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVmPrivate \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet Private \
  --generate-ssh-keys
```

Создание виртуальной машины занимает несколько минут. После создания запишите значение **publicIpAddress** из полученных выходных данных. На следующем шаге этот адрес потребуется для доступа к виртуальной машине из Интернета.

### <a name="confirm-access-to-storage-account"></a>Подтверждение прав доступа к учетной записи хранения

Подключитесь к виртуальной машине *myVmPrivate* по протоколу SSH. Замените * \<publicIpAddress>* общедоступным IP-адресом *вашего myVmPrivate* VM.

```bash 
ssh <publicIpAddress>
```

Создайте папку для точки подключения.

```bash
sudo mkdir /mnt/MyAzureFileShare1
```

Подключите файловый ресурс Azure к созданному каталогу. Перед выполнением команды ниже замените * \<>хранилища-ключ* с значением *AccountKey* с **$saConnectionString1**.

```bash
sudo mount --types cifs //allowedstorageacc.file.core.windows.net/my-file-share /mnt/MyAzureFileShare1 --options vers=3.0,username=allowedstorageacc,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino
```

Отобразится строка запроса `user@myVmPrivate:~$`. Файловый ресурс Azure успешно подключен к папке */mnt/MyAzureFileShare*.

### <a name="confirm-access-is-denied-to-storage-account"></a>Подтверждение запрета доступа к учетной записи хранения

Из того же VM *myVmPrivate*, создать каталог для точки крепления:

```bash
sudo mkdir /mnt/MyAzureFileShare2
```

Попытка смонтировать долю файла Azure из учетной записи хранения, не в *сводной,* в созданный каталог. В этой статье предполагается, что вы развернули последнюю версию Ubuntu. При использовании более ранних версий Ubuntu обратитесь к разделу [Использование файлов Azure в Linux](../storage/files/storage-how-to-use-files-linux.md?toc=%2fazure%2fvirtual-network%2ftoc.json), чтобы получить дополнительные инструкции по подключению файловых ресурсов. 

Перед выполнением команды ниже замените * \<>хранения ключей* с значением *AccountKey* от **$saConnectionString2**.

```bash
sudo mount --types cifs //notallowedstorageacc.file.core.windows.net/my-file-share /mnt/MyAzureFileShare2 --options vers=3.0,username=notallowedstorageacc,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino
```

Доступ запрещен, и вы `mount error(13): Permission denied` получаете ошибку, потому что эта учетная запись хранилища не находится в списке политики конечной точки службы, которую мы применили к подсети. 

Закройте сеанс SSH с виртуальной машиной *myVmPublic*.

## <a name="clean-up-resources"></a>Очистка ресурсов

Вы можете удалить ненужную группу ресурсов и все содержащиеся в ней ресурсы, выполнив команду [az group delete](/cli/azure).

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Дальнейшие действия

В этой статье вы применили политику конечных точек службы в отношении точки фактической сетевой службы Azure к хранилищем Azure. Вы создали учетные записи хранилища Azure и ограниченный доступ к сети только к определенным учетным записям хранения (и, таким образом, отрицали другие) из виртуальной сетевой подсети. Чтобы узнать больше о политиках [Service endpoints policies overview](virtual-network-service-endpoint-policies-overview.md)конечных точек службы, см.
