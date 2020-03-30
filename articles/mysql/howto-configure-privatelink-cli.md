---
title: Частная ссылка - Azure CLI - База данных Azure для MyS'L
description: Узнайте, как настроить приватную ссылку для базы данных Azure для MyS'L из Azure CLI
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 01/09/2020
ms.openlocfilehash: f83f52f1c1800803c5e1d47f1931f7b13b2c11de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79368015"
---
# <a name="create-and-manage-private-link-for-azure-database-for-mysql-using-cli"></a>Создание и управление частной ссылкой для базы данных Azure для MyS'L с помощью CLI

Частная конечная точка — ключевой компонент для построения частной ссылки в Azure. Это позволяет ресурсам Azure, таким как виртуальные машины (VM), обмениваться данными в частном порядке с ресурсами частной ссылки. В этой статье вы узнаете, как использовать Azure CLI для создания VM в виртуальной сети Azure и базу данных Azure для сервера MyS'L с частной конечной точкой Azure.

> [!NOTE]
> Эта функция доступна во всех регионах Azure, где база данных Azure для MyS'L поддерживает уровни ценообразования общего назначения и памяти.

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Если вы решили установить и использовать Azure CLI локально, для выполнения инструкций из этого руководства вам потребуется использовать Azure CLI 2.0.28 или более поздней версии. Выполните команду `az --version`, чтобы узнать установленную версию. Сведения об установке или обновлении Azure CLI см. в [этой статье](/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Перед созданием любого ресурса необходимо создать группу ресурсов, которая будет содержать эту виртуальную сеть. Создайте группу ресурсов с помощью команды [az group create](/cli/azure/group). Этот пример создает группу ресурсов под названием *myResourceGroup* в *западноевропейских* местах:

```azurecli-interactive
az group create --name myResourceGroup --location westeurope
```

## <a name="create-a-virtual-network"></a>Создайте виртуальную сеть
Создайте виртуальную сеть с помощью команды [az network vnet create](/cli/azure/network/vnet). В этом примере создается виртуальная сеть по умолчанию с именем *myVirtualNetwork* с подсетью *mySubnet*.

```azurecli-interactive
az network vnet create \
 --name myVirtualNetwork \
 --resource-group myResourceGroup \
 --subnet-name mySubnet
```

## <a name="disable-subnet-private-endpoint-policies"></a>Отключение политик подсети частной конечной точки 
Поскольку Azure развертывает ресурсы в подсеть виртуальной сети, чтобы отключить политики сети частной конечной точки, вам следует создать или обновить подсеть. Обновите конфигурацию подсети *mySubnet* с помощью команды [az network vnet subnet update](https://docs.microsoft.com/cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-update).

```azurecli-interactive
az network vnet subnet update \
 --name mySubnet \
 --resource-group myResourceGroup \
 --vnet-name myVirtualNetwork \
 --disable-private-endpoint-network-policies true
```
## <a name="create-the-vm"></a>Создание виртуальной машины 
Создайте виртуальную машину с помощью команды az vm create. При появлении запроса укажите пароль в качестве учетных данных для входа на виртуальную машину. В этом примере создается виртуальная машина с именем *myVM*. 
```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm \
  --image Win2019Datacenter
```
Запишите общедоступный IP-адрес виртуальной машины. Этот адрес используется на следующем шаге, чтобы подключиться к виртуальной машине из Интернета.

## <a name="create-an-azure-database-for-mysql-server"></a>Создайте сервер базы данных Azure для MySQL. 
Создайте базу данных Azure для MyS'L с помощью сервера az mysql, создающего команду. Помните, что имя вашего сервера MyS'L должно быть уникальным в Azure, поэтому замените значение заполнителя в скобках на ваше собственное уникальное значение: 

```azurecli-interactive
# Create a logical server in the resource group 
az mysql server create \
--name mydemoserver \
--resource-group myResourcegroup \
--location westeurope \
--admin-user mylogin \
--admin-password <server_admin_password> \
--sku-name GP_Gen5_2
```

Обратите внимание, что идентификатор сервера MyS'L похож на ```/subscriptions/subscriptionId/resourceGroups/myResourceGroup/providers/Microsoft.DBforMySQL/servers/servername.``` идентификатор сервера MyS'L на следующем этапе. 

## <a name="create-the-private-endpoint"></a>Создание частной конечной точки 
Создайте в виртуальной сети частную конечную точку для сервера MyS'L: 
```azurecli-interactive
az network private-endpoint create \  
    --name myPrivateEndpoint \  
    --resource-group myResourceGroup \  
    --vnet-name myVirtualNetwork  \  
    --subnet mySubnet \  
    --private-connection-resource-id "<MySQL Server ID>" \  
    --group-ids mysqlServer \  
    --connection-name myConnection  
 ```

## <a name="configure-the-private-dns-zone"></a>Настройка частной зоны DNS 
Создайте частную DNS-зону для домена сервера MyS'L и создайте связующее звено с виртуальной сетью. 
```azurecli-interactive
az network private-dns zone create --resource-group myResourceGroup \ 
   --name  "privatelink.mysql.database.azure.com" 
az network private-dns link vnet create --resource-group myResourceGroup \ 
   --zone-name  "privatelink.mysql.database.azure.com"\ 
   --name MyDNSLink \ 
   --virtual-network myVirtualNetwork \ 
   --registration-enabled false 

#Query for the network interface ID  
networkInterfaceId=$(az network private-endpoint show --name myPrivateEndpoint --resource-group myResourceGroup --query 'networkInterfaces[0].id' -o tsv)
 
 
az resource show --ids $networkInterfaceId --api-version 2019-04-01 -o json 
# Copy the content for privateIPAddress and FQDN matching the Azure database for MySQL name 
 
 
#Create DNS records 
az network private-dns record-set a create --name myserver --zone-name privatelink.mysql.database.azure.com --resource-group myResourceGroup  
az network private-dns record-set a add-record --record-set-name myserver --zone-name privatelink.mysql.database.azure.com --resource-group myResourceGroup -a <Private IP Address>
```

> [!NOTE] 
> Настройка F'DN в настройках DNS клиента не решается с закрытым IP настроенным. Вам придется настроить зону DNS для настроенного F-DN, как показано [здесь.](../dns/dns-operations-recordsets-portal.md)

## <a name="connect-to-a-vm-from-the-internet"></a>Подключение к виртуальной машине из Интернета

Подключитесь к виртуальной машине *myVm* из Интернета, выполнив следующие действия.

1. На портале в строке поиска введите *myVm*.

1. Нажмите кнопку **Подключиться**. После нажатия кнопки **Подключиться** откроется окно **Connect to virtual machine** (Подключение к виртуальной машине).

1. Выберите **Скачать RDP файл**. Azure создаст и скачает на ваш компьютер файл протокола удаленного рабочего стола (*RDP*).

1. Откройте файл *downloaded.rdp*.

    1. При появлении запроса выберите **Подключиться**.

    1. Введите имя пользователя и пароль, указанные при создании виртуальной машины.

        > [!NOTE]
        > Возможно, вам придется выбрать **Дополнительные варианты** > **Используйте другую учетную запись,** чтобы указать учетные данные, которые вы ввели при создании VM.

1. Нажмите кнопку **ОК**.

1. При входе в систему может появиться предупреждение о сертификате. В таком случае выберите **Да** или **Продолжить**.

1. Когда появится рабочий стол виртуальной машины, сверните его, чтобы вернуться на локальный рабочий стол.  

## <a name="access-the-mysql-server-privately-from-the-vm"></a>Доступ к серверу MyS'L в частном порядке с VM

1. На удаленном рабочем столе  *myVm* откройте PowerShell.

2. Введите  `nslookup mydemomysqlserver.privatelink.mysql.database.azure.com`. 

    Должно появиться сообщение следующего вида:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    mydemomysqlserver.privatelink.mysql.database.azure.com
    Address:  10.1.3.4
    ```

3. Проверьте частное соединение ссылки на сервер MyS'L с помощью любого доступного клиента. В приведенном ниже примере я использовал [MyS'L Workbench](https://dev.mysql.com/doc/workbench/en/wb-installing-windows.html) для проведения операции.


4. В **Новом подключении**введите или выберите эту информацию:

    | Параметр | Значение |
    | ------- | ----- |
    | Имя подключения| Выберите имя соединения по вашему выбору.|
    | Имя узла | Выберите *mydemoserver.privatelink.mysql.database.azure.com* |
    | Имя пользователя | Введите *username@servername* имя пользователя, как это предусмотрено во время создания сервера MyS'L. |
    | Пароль | Введите пароль, предоставленный при создании сервера MyS'L. |
    ||

5. Выберите Подключиться.

6. Просмотр баз данных из левого меню.

7. (По желанию) Создание или запрос информации из базы данных MyS'L.

8. Закройте удаленное подключение к рабочему столу на myVm.

## <a name="clean-up-resources"></a>Очистка ресурсов 
Чтобы удалить ненужную группу ресурсов и все содержащиеся в ней ресурсы, выполните команду "az group delete". 

```azurecli-interactive
az group delete --name myResourceGroup --yes 
```

## <a name="next-steps"></a>Дальнейшие действия
- Узнайте больше о [том, что такое частная конечная точка Azure](https://docs.microsoft.com/azure/private-link/private-endpoint-overview)
