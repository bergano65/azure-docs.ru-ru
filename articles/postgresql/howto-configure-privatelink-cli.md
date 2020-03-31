---
title: Частная ссылка - Azure CLI - База данных Azure для PostgreS'L - Единый сервер
description: Узнайте, как настроить частную ссылку для базы данных Azure для почтового сервера PostgreS-L- Одинсервер от Azure CLI
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/09/2020
ms.openlocfilehash: d4288b901a0e6e132e32f8391d108e79861fc331
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371041"
---
# <a name="create-and-manage-private-link-for-azure-database-for-postgresql---single-server-using-cli"></a>Создание и управление частной ссылкой для базы данных Azure для PostgreS-L - Единый сервер с помощью CLI

Частная конечная точка — ключевой компонент для построения частной ссылки в Azure. Это позволяет ресурсам Azure, таким как виртуальные машины (VM), обмениваться данными в частном порядке с ресурсами частной ссылки. В этой статье вы узнаете, как использовать Azure CLI для создания VM в виртуальной сети Azure и базу данных Azure для одного сервера PostgreS'L с частной конечной точкой Azure.

> [!NOTE]
> Эта функция доступна во всех регионах Azure, где база данных Azure для PostgreS-L - Единый сервер поддерживает уровни ценообразования общего назначения и памяти.

## <a name="prerequisites"></a>Предварительные требования

Прежде чем приступить к выполнению этого руководства, необходимы следующие компоненты:

- [сервер и база данных Azure для PostgreSQL](quickstart-create-server-database-azure-cli.md);

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

## <a name="create-an-azure-database-for-postgresql---single-server"></a>Создание базы данных Azure для PostgreS'L - Единый сервер 
Создайте базу данных Azure для PostgreS'L с сервером az postgres, создавая команду. Помните, что имя вашего сервера PostgreS'L должно быть уникальным в Azure, поэтому замените значение заполнителя в скобках на ваше собственное уникальное значение: 

```azurecli-interactive
# Create a logical server in the resource group 
az postgres server create \
--name mydemoserver \
--resource-group myresourcegroup \
--location westeurope \
--admin-user mylogin \
--admin-password <server_admin_password> \
--sku-name GP_Gen5_2
```

Обратите внимание, что идентификатор сервера PostgreS'L похож ```/subscriptions/subscriptionId/resourceGroups/myResourceGroup/providers/Microsoft.DBforPostgreSQL/servers/servername.``` на идентификатор сервера PostgreS'L на следующем этапе. 

## <a name="create-the-private-endpoint"></a>Создание частной конечной точки 
Создайте частную конечную точку для сервера PostgreS'L в вашей виртуальной сети: 
```azurecli-interactive
az network private-endpoint create \  
    --name myPrivateEndpoint \  
    --resource-group myResourceGroup \  
    --vnet-name myVirtualNetwork  \  
    --subnet mySubnet \  
    --private-connection-resource-id "<PostgreSQL Server ID>" \  
    --group-ids postgresqlServer \  
    --connection-name myConnection  
 ```

## <a name="configure-the-private-dns-zone"></a>Настройка частной зоны DNS 
Создайте частную DNS-зону для домена сервера PostgreS'L и создайте связующее звено с виртуальной сетью. 
```azurecli-interactive
az network private-dns zone create --resource-group myResourceGroup \ 
   --name  "privatelink.postgres.database.azure.com" 
az network private-dns link vnet create --resource-group myResourceGroup \ 
   --zone-name  "privatelink.postgres.database.azure.com"\ 
   --name MyDNSLink \ 
   --virtual-network myVirtualNetwork \ 
   --registration-enabled false 

#Query for the network interface ID  
networkInterfaceId=$(az network private-endpoint show --name myPrivateEndpoint --resource-group myResourceGroup --query 'networkInterfaces[0].id' -o tsv)
 
 
az resource show --ids $networkInterfaceId --api-version 2019-04-01 -o json 
# Copy the content for privateIPAddress and FQDN matching the Azure database for PostgreSQL name 
 
 
#Create DNS records 
az network private-dns record-set a create --name myserver --zone-name privatelink.postgres.database.azure.com --resource-group myResourceGroup  
az network private-dns record-set a add-record --record-set-name myserver --zone-name privatelink.postgres.database.windows.net --resource-group myResourceGroup -a <Private IP Address>
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

## <a name="access-the-postgresql-server-privately-from-the-vm"></a>Доступ к серверу PostgreS'L в частном порядке с VM

1. На удаленном рабочем столе  *myVm* откройте PowerShell.

2. Введите  `nslookup mydemopostgresserver.privatelink.postgres.database.azure.com`. 

    Должно появиться сообщение следующего вида:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    mydemopostgresserver.privatelink.postgres.database.azure.com
    Address:  10.1.3.4
    ```

3. Проверьте частное соединение ссылки для сервера PostgreS'L с помощью любого доступного клиента. В приведенном ниже примере я использовал [студию Данных Azure](https://docs.microsoft.com/sql/azure-data-studio/download?view=sql-server-ver15) для проведения операции.

4. В **Новом подключении**введите или выберите эту информацию:

    | Параметр | Значение |
    | ------- | ----- |
    | Тип сервера| Выберите **PostgreS'L**.|
    | Имя сервера| Выберите *mydemopostgresserver.privatelink.postgres.database.azure.com* |
    | Имя пользователя | Введите username@servername имя пользователя, как это предусмотрено во время создания сервера PostgreS'L. |
    |Пароль |Введите пароль, предоставленный при создании сервера PostgreS'L. |
    |SSL|Выберите **Требуемое**время .|
    ||

5. Выберите Подключиться.

6. Просмотр баз данных из левого меню.

7. (По желанию) Создавайте или задавив запрос с сервера postgreS'L.

8. Закройте удаленное подключение к рабочему столу на myVm.

## <a name="clean-up-resources"></a>Очистка ресурсов 
Чтобы удалить ненужную группу ресурсов и все содержащиеся в ней ресурсы, выполните команду "az group delete". 

```azurecli-interactive
az group delete --name myResourceGroup --yes 
```

## <a name="next-steps"></a>Дальнейшие действия
- Узнайте больше о [том, что такое частная конечная точка Azure](https://docs.microsoft.com/azure/private-link/private-endpoint-overview)
