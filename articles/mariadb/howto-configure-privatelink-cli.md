---
title: Частная ссылка — Azure CLI — база данных Azure для MariaDB
description: Узнайте, как настроить частную ссылку на базу данных Azure для MariaDB из Azure CLI
author: kummanish
ms.author: manishku
ms.service: mariadb
ms.topic: how-to
ms.date: 01/09/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: a8f94c34281adbe274ad70425850e2ade4dc94ea
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/06/2020
ms.locfileid: "87833238"
---
# <a name="create-and-manage-private-link-for-azure-database-for-mariadb-using-cli"></a>Создание и управление частной ссылкой для базы данных Azure для MariaDB с помощью интерфейса командной строки

Частная конечная точка — ключевой компонент для построения частной ссылки в Azure. Это позволяет ресурсам Azure, таким как виртуальные машины (VM), обмениваться данными в частном порядке с ресурсами частной ссылки. В этой статье вы узнаете, как использовать Azure CLI для создания виртуальной машины в виртуальной сети Azure и сервера базы данных Azure для MariaDB с частной конечной точкой Azure.

> [!NOTE]
> Функция "Частная связь" доступна только для серверов базы данных Azure для MariaDB в общего назначения или ценовых категориях, оптимизированных для памяти. Убедитесь, что сервер базы данных находится в одной из этих ценовых категорий.

## <a name="prerequisites"></a>Предварительные требования

Прежде чем приступить к выполнению этого руководства, необходимы следующие компоненты:

- [Сервер базы данных Azure для MariaDB](quickstart-create-mariadb-server-database-using-azure-cli.md).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Если вы решили установить и использовать Azure CLI локально, для выполнения инструкций из этого руководства вам потребуется использовать Azure CLI 2.0.28 или более поздней версии. Выполните команду `az --version`, чтобы узнать установленную версию. Сведения об установке или обновлении Azure CLI см. в [этой статье](/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Перед созданием любого ресурса необходимо создать группу ресурсов, которая будет содержать эту виртуальную сеть. Создайте группу ресурсов с помощью команды [az group create](/cli/azure/group). В этом примере создается группа ресурсов с именем *myResourceGroup* в расположении *westeurope* :

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
Azure развертывает ресурсы в подсеть в виртуальной сети, поэтому необходимо создать или обновить подсеть, чтобы отключить [политики сети](../private-link/disable-private-endpoint-network-policy.md)частной конечной точки. Обновите конфигурацию подсети *mySubnet* с помощью команды [az network vnet subnet update](https://docs.microsoft.com/cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-update).

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

## <a name="create-an-azure-database-for-mariadb-server"></a>Создание сервера Базы данных Azure для MariaDB 
Создайте базу данных Azure для MariaDB с помощью команды AZ MariaDB Server Create. Помните, что имя сервера MariaDB должно быть уникальным в пределах Azure, поэтому замените значение заполнителя в квадратных скобках своим уникальным значением: 

```azurecli-interactive
# Create a server in the resource group 
az mariadb server create \
--name mydemoserver \
--resource-group myResourcegroup \
--location westeurope \
--admin-user mylogin \
--admin-password <server_admin_password> \
--sku-name GP_Gen5_2
```

> [!NOTE]
> Иногда база данных Azure для MariaDB и подсеть виртуальной сети относятся к разным подпискам. В этих случаях необходимо обеспечить следующую конфигурацию:
> - Убедитесь, что в обеих подписках зарегистрирован поставщик ресурсов **Microsoft. дбформариадб** . Дополнительные сведения см. в разделе [resource-manager-registration][resource-manager-portal]

## <a name="create-the-private-endpoint"></a>Создание частной конечной точки 
Создайте частную конечную точку для сервера MariaDB в виртуальной сети. 

```azurecli-interactive
az network private-endpoint create \  
    --name myPrivateEndpoint \  
    --resource-group myResourceGroup \  
    --vnet-name myVirtualNetwork  \  
    --subnet mySubnet \  
    --private-connection-resource-id $(az resource show -g myResourcegroup -n mydemoserver --resource-type "Microsoft.DBforMariaDB/servers" --query "id") \    
    --group-id mariadbServer \  
    --connection-name myConnection  
 ```


## <a name="configure-the-private-dns-zone"></a>Настройка частной зоны DNS 
Создайте Частная зона DNS зону для домена сервера Маридб и создайте связь связи с виртуальной сетью. 
```azurecli-interactive
az network private-dns zone create --resource-group myResourceGroup \ 
   --name  "privatelink.mariadb.database.azure.com" 
az network private-dns link vnet create --resource-group myResourceGroup \ 
   --zone-name  "privatelink.mariadb.database.azure.com"\ 
   --name MyDNSLink \ 
   --virtual-network myVirtualNetwork \ 
   --registration-enabled false 

#Query for the network interface ID  
networkInterfaceId=$(az network private-endpoint show --name myPrivateEndpoint --resource-group myResourceGroup --query 'networkInterfaces[0].id' -o tsv)
 
 
az resource show --ids $networkInterfaceId --api-version 2019-04-01 -o json 
# Copy the content for privateIPAddress and FQDN matching the Azure database for MariaDB name 
 
 
#Create DNS records 
az network private-dns record-set a create --name mydemoserver --zone-name privatelink.mariadb.database.azure.com --resource-group myResourceGroup  
az network private-dns record-set a add-record --record-set-name mydemoserver --zone-name privatelink.mariadb.database.windows.net --resource-group myResourceGroup -a <Private IP Address>
```

> [!NOTE] 
> Полное доменное имя в параметре DNS клиента не разрешается в настроенный частный IP-адрес. Вам потребуется настроить зону DNS для настроенного FQDN, как показано [ниже](../dns/dns-operations-recordsets-portal.md).

## <a name="connect-to-a-vm-from-the-internet"></a>Подключение к виртуальной машине из Интернета

Подключитесь к виртуальной машине *myVm* из Интернета, выполнив следующие действия.

1. На портале в строке поиска введите *myVm*.

1. Нажмите кнопку **Подключиться**. После нажатия кнопки **Подключиться** откроется окно **Connect to virtual machine** (Подключение к виртуальной машине).

1. Щелкните **Скачать RDP-файл**. Azure создаст и скачает на ваш компьютер файл протокола удаленного рабочего стола (*RDP*).

1. Откройте файл *downloaded.rdp*.

    1. При появлении запроса выберите **Подключиться**.

    1. Введите имя пользователя и пароль, указанные при создании виртуальной машины.

        > [!NOTE]
        > Возможно, потребуется выбрать **More choices** > **Use a different account** (Дополнительные варианты > Использовать другую учетную запись), чтобы указать учетные данные, введенные при создании виртуальной машины.

1. Щелкните **ОК**.

1. При входе в систему может появиться предупреждение о сертификате. В таком случае выберите **Да** или **Продолжить**.

1. Когда появится рабочий стол виртуальной машины, сверните его, чтобы вернуться на локальный рабочий стол.  

## <a name="access-the-mariadb-server-privately-from-the-vm"></a>Доступ к серверу MariaDB в частном порядке с виртуальной машины

1. На удаленном рабочем столе  *myVm* откройте PowerShell.

2. Введите  `nslookup mydemoserver.privatelink.mariadb.database.azure.com`. 

    Должно появиться сообщение следующего вида:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    mydemoserver.privatelink.mariadb.database.azure.com
    Address:  10.1.3.4
    ```

3. Проверьте подключение к частной ссылке для сервера MariaDB, используя любой доступный клиент. В примере ниже я использовал [MySQL Workbench](https://dev.mysql.com/doc/workbench/en/wb-installing-windows.html) для выполнения этой операции.

4. В окне **новое подключение**введите или выберите следующие сведения:

    | Параметр | Значение |
    | ------- | ----- |
    | Имя подключения| Выберите нужное имя подключения.|
    | Имя узла | Выбор *mydemoserver.privatelink.MariaDB.Database.Azure.com* |
    | Имя пользователя | Введите имя пользователя, *username@servername* которое предоставляется во время создания сервера MariaDB. |
    | Пароль | Введите пароль, указанный при создании сервера MariaDB. |
    ||

5. Выберите **проверить подключение** или **ОК**.

6. При необходимости Просмотр баз данных из левого меню и создание или запрос информации из базы данных MariaDB

8. Закройте подключение к удаленному рабочему столу myVm.

## <a name="clean-up-resources"></a>Очистка ресурсов 
Чтобы удалить ненужную группу ресурсов и все содержащиеся в ней ресурсы, выполните команду "az group delete". 

```azurecli-interactive
az group delete --name myResourceGroup --yes 
```

## <a name="next-steps"></a>Дальнейшие действия
Подробнее о том [, что такое частная конечная точка Azure](https://docs.microsoft.com/azure/private-link/private-endpoint-overview)

<!-- Link references, to text, Within this same GitHub repo. -->
[resource-manager-portal]: ../azure-resource-manager/management/resource-providers-and-types.md
