---
title: Создание частной конечной точки Azure с помощью Azure CLI | Документация Майкрософт
description: Сведения о частной конечной точке Azure
services: private-link
author: KumudD
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: kumud
ms.openlocfilehash: 30994133b19c4f59ae9e8be26caffe14348638f6
ms.sourcegitcommit: 3fa4384af35c64f6674f40e0d4128e1274083487
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2019
ms.locfileid: "71219370"
---
# <a name="create-a-private-endpoint-using-azure-cli"></a>Создание частной конечной точки с помощью Azure CLI
Частная конечная точка — это фундаментальный Стандартный блок для частной ссылки в Azure. Она позволяет ресурсам Azure, таким как виртуальные машины, обмениваться данными с частными ресурсами. В этом кратком руководстве вы узнаете, как создать виртуальную машину в виртуальной сети, сервере базы данных SQL с частной конечной точкой, использующей Azure CLI. Затем вы можете получить доступ к виртуальной машине и получить безопасный доступ к ресурсу частной ссылки (в этом примере это частный сервер базы данных SQL Azure). 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Если вы решили установить и использовать Azure CLI локально, для выполнения инструкций из этого руководства вам потребуется использовать Azure CLI 2.0.28 или более поздней версии. Выполните команду `az --version`, чтобы узнать установленную версию. Сведения об установке или обновлении Azure CLI см. в [этой статье](/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Создать группу ресурсов

Прежде чем можно будет создать ресурс, необходимо создать группу ресурсов для размещения виртуальной сети. Создайте группу ресурсов с помощью команды [az group create](/cli/azure/group). В этом примере создается группа ресурсов с именем *myResourceGroup* в расположении *westcentralus* :

```azurecli-interactive
az group create --name myResourceGroup --location westcentralus
```

## <a name="create-a-virtual-network"></a>Создать виртуальную сеть
Создайте виртуальную сеть с помощью команды [AZ Network vnet Create](/cli/azure/network/vnet). В этом примере создается виртуальная сеть по умолчанию с именем *myVirtualNetwork* с одной подсетью с именем *mySubnet*:

```azurecli-interactive
az network vnet create \
 --name myVirtualNetwork \
 --resource-group myResourceGroup \
 --subnet-name mySubnet
```
## <a name="disable-subnet-private-endpoint-policies"></a>Отключить политики частной конечной точки подсети 
Azure развертывает ресурсы в подсеть в виртуальной сети, поэтому необходимо создать или обновить подсеть, чтобы отключить политики сети частной конечной точки. Обновите конфигурацию подсети с именем * mySubnet * *, выполнив [Обновление для подсети AZ Network vnet](https://docs.microsoft.com/cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-update):

```azurecli-interactive
az network vnet subnet update \
 --name mySubnet \
 --resource-group myResourceGroup \
 --vnet-name myVirtualNetwork \
 --disable-private-endpoint-network-policies true
```
## <a name="create-the-vm"></a>Создание виртуальной машины 
Создайте виртуальную машину с помощью команды AZ VM Create. При появлении запроса введите пароль, который будет использоваться в качестве учетных данных для входа для виртуальной машины. В этом примере создается виртуальная машина с именем *myVm*: 
```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm \
  --image Win2019Datacenter
```
 Запишите общедоступный IP-адрес виртуальной машины. Этот адрес используется на следующем шаге, чтобы подключиться к виртуальной машине из Интернета.

## <a name="create-a-sql-database-server"></a>Создание сервера базы данных SQL 
Создайте сервер базы данных SQL с помощью команды AZ SQL Server Create. Помните, что имя SQL Server должно быть уникальным в пределах Azure, поэтому замените значение заполнителя в квадратных скобках своим уникальным значением: 

```azurecli-interactive
# Create a logical server in the resource group 
az sql server create \ 
    --name "myserver"\ 
    --resource-group myResourceGroup \ 
    --location WestUS \ 
    --admin-user "sqladmin" \ 
    --admin-password "CHANGE_PASSWORD_1" 
 
# Create a database in the server with zone redundancy as false 
az sql db create \ 
    --resource-group myResourceGroup  \ 
    --server myserver \ 
    --name mySampleDatabase \ 
    --sample-name AdventureWorksLT \ 
    --edition GeneralPurpose \ 
    --family Gen4 \ 
    --capacity 1 
```

Обратите внимание, что идентификатор SQL Server ```/subscriptions/subscriptionId/resourceGroups/myResourceGroup/providers/Microsoft.Sql/servers/myserver.``` аналогичен, в следующем шаге будет использоваться идентификатор SQL Server. 

## <a name="create-the-private-endpoint"></a>Создание частной конечной точки 
Создайте частную конечную точку для сервера базы данных SQL в виртуальной сети. 
```azurecli-interactive
az network private-endpoint create \  
    --name myPrivateEndpoint \  
    --resource-group myResourceGroup \  
    --vnet-name myVirtualNetwork  \  
    --subnet mySubnet \  
    --private-connection-resource-id "<SQL Server ID>" \  
    --group-ids sqlServer \  
    --connection-name myConnection  
 ```
## <a name="configure-the-private-dns-zone"></a>Настройка зоны Частная зона DNS 
Создайте зону Частная зона DNS для домена сервера базы данных SQL и создайте связь связи с виртуальной сетью. 
```azurecli-interactive
az network private-dns zone create --resource-group myResourceGroup \ 
   --name  "privatelink.database.windows.net" 
az network private-dns link vnet create --resource-group myResourceGroup \ 
   --zone-name  "privatelink.database.windows.net"\ 
   --name MyDNSLink \ 
   --virtual-network myVirtualNetwork \ 
   --registration-enabled false 

#Query for the network interface ID  
networkInterfaceId=$(az network private-endpoint show --name myPrivateEndpoint --resource-group myResourceGroup --query 'networkInterfaces[0].id' -o tsv)
 
 
az resource show --ids $networkInterfaceId --api-version 2019-04-01 -o json 
# Copy the content for privateIPAddress and FQDN matching the SQL server name 
 
 
#Create DNS records 
az network private-dns record-set a create --name myserver --zone-name privatelink.database.windows.net --resource-group myResourceGroup  
az network private-dns record-set a add-record --record-set-name myserver --zone-name privatelink.database.windows.net --resource-group myResourceGroup -a <Private IP Address>
```

## <a name="connect-to-a-vm-from-the-internet"></a>Подключение к виртуальной машине из Интернета

Подключитесь к виртуальной машине *myVm* из Интернета следующим образом:

1. На портале в строке поиска введите *myVm*.

1. Нажмите кнопку **Подключиться**. После нажатия кнопки **Подключиться** откроется окно **Connect to virtual machine** (Подключение к виртуальной машине).

1. Щелкните **Скачать RDP-файл**. Azure создаст и скачает на ваш компьютер файл протокола удаленного рабочего стола (*RDP*).

1. Откройте скачанный RDP-файл*.

    1. При появлении запроса выберите **Подключиться**.

    1. Введите имя пользователя и пароль, указанные при создании виртуальной машины.

        > [!NOTE]
        > Возможно, потребуется выбрать **More choices** > **Use a different account** (Дополнительные варианты > Использовать другую учетную запись), чтобы указать учетные данные, введенные при создании виртуальной машины.

1. Нажмите кнопку **ОК**.

1. При входе в систему может появиться предупреждение о сертификате. В таком случае выберите **Да** или **Продолжить**.

1. Когда появится рабочий стол виртуальной машины, сверните его, чтобы вернуться на локальный рабочий стол.  

## <a name="access-sql-database-server-privately-from-the-vm"></a>Доступ к серверу базы данных SQL в частном порядке с виртуальной машины

В этом разделе вы будете подключаться к серверу базы данных SQL из виртуальной машины с помощью частной конечной точки.

 1. На удаленном рабочем столе  *myVm* откройте PowerShell.
 2. Введите nslookup MyServer.Database.Windows.NET  , чтобы получить примерно следующее сообщение: 

```
      Server:  UnKnown 
      Address:  168.63.129.16 
      Non-authoritative answer: 
      Name:    myserver.privatelink.database.windows.net 
      Address:  10.0.0.5 
      Aliases:  myserver.database.windows.net 
```
 3. Установить SQL Server Management Studio 
 4. В окне Подключение к серверу введите или выберите следующие сведения: Тип сервера: Выберите ядро СУБД.
 Имя сервера: Выберите имя пользователя myserver.database.windows.net: Введите имя пользователя, указанное во время создания.
 Пароль: Введите пароль, указанный во время создания.
 Запомнить пароль: Выберите Да.
 
 5. Выберите **подключить**.
 6. Просмотр **баз данных** из левого меню.
 7. При необходимости Создание или запрос информации из *MyDatabase*
 8. Закройте подключение к удаленному рабочему столу *myVm*.

## <a name="clean-up-resources"></a>Очистка ресурсов 
Если она больше не нужна, можно удалить группу ресурсов и все ее ресурсы с помощью команды AZ Group DELETE. 

```azurecli-interactive
az group delete --name myResourceGroup --yes 
```

## <a name="next-steps"></a>Следующие шаги
- Дополнительные сведения о [частной ссылке Azure](private-link-overview.md)
 
