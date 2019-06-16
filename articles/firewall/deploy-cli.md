---
title: Развертывание и настройка брандмауэра Azure, с помощью Azure CLI
description: В этой статье вы узнаете, как развернуть и настроить брандмауэр Azure с помощью Azure CLI.
services: firewall
author: vhorne
ms.service: firewall
ms.date: 06/11/2019
ms.author: victorh
ms.topic: article
ms.openlocfilehash: b40ac789fbc331e779e85462724e5c8a8e9bce47
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67083357"
---
# <a name="deploy-and-configure-azure-firewall-using-azure-cli"></a>Развертывание и настройка брандмауэра Azure, с помощью Azure CLI

Управление доступом исходящих сетевых подключений является важной частью общего плана безопасности сети. Например, вы можете ограничить доступ к веб-сайтам или исходящим IP-адресам и портам, которые могут быть доступными.

Вы можете управлять доступом к исходящей сети из подсети Azure только с помощью Брандмауэра Azure. Брандмауэр Azure позволяет настроить:

* Правила приложений, определяющие полные доменные имена (FQDN), к которым можно получить доступ из подсети.
* Правила сети, определяющие адрес источника, протокол, порт назначения и адрес назначения.

При маршрутизации трафика на брандмауэр, используемый в качестве шлюза по умолчанию, для подсети к трафику применяются настроенные правила брандмауэра.

В этой статье вы создадите упрощенный одной виртуальной сети с три подсети для более удобного развертывания. В рабочих развертываниях [звездообразной модели](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) рекомендуется. Брандмауэр является в собственную виртуальную сеть. Серверы рабочей нагрузки размещены в одноранговых виртуальных сетях в одном регионе с одной или несколькими подсетями.

* **AzureFirewallSubnet** — в этой подсети находится брандмауэр.
* **Workload-SN** — в этой подсети находится сервер рабочей нагрузки. Трафик этой подсети проходит через брандмауэр.
* **Jump-SN** — в этой подсети находится сервер перехода. Сервер перехода имеет общедоступный IP-адрес, к которому можно подключиться с помощью удаленного рабочего стола. Затем вы можете подключиться к серверу рабочей нагрузки (используя другой удаленный рабочий стол).

![Инфраструктура сети, используемая в руководстве](media/tutorial-firewall-rules-portal/Tutorial_network.png)

В этой статье раскрываются следующие темы:

> [!div class="checklist"]
> * настройка тестовой сетевой среды;
> * развертывание брандмауэра;
> * создание маршрута по умолчанию;
> * настройка правила приложения для предоставления доступа к www.google.com;
> * настройка сетевых правил для предоставления доступа к внешним DNS-серверам;
> * тестирование брандмауэра.

При желании можно выполнить этой процедуры с помощью [портала Azure](tutorial-firewall-deploy-portal.md) или [Azure PowerShell](deploy-ps.md).

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Технические условия

### <a name="azure-cli"></a>Инфраструктура CLI Azure

Если вы решили установить и использовать CLI локально, вам потребуется Azure CLI 2.0.4 или более поздней версии. Чтобы узнать версию, выполните команду **az --version**. Дополнительные сведения об установке или обновлении см. [здесь]( /cli/azure/install-azure-cli).

## <a name="set-up-the-network"></a>Настройка сети

Сначала создайте группу ресурсов, необходимых для развертывания брандмауэра. Затем создайте виртуальную сеть, подсети и тестовые серверы.

### <a name="create-a-resource-group"></a>Создание группы ресурсов

Группа ресурсов содержит все ресурсы для развертывания.

```azurecli-interactive
az group create --name Test-FW-RG --location eastus
```

### <a name="create-a-vnet"></a>Создание виртуальной сети

Эта виртуальная сеть имеет три подсети.

> [!NOTE]
> Минимальный размер подсети AzureFirewallSubnet равен /26.

```azurecli-interactive
az network vnet create \
  --name Test-FW-VN \
  --resource-group Test-FW-RG \
  --location eastus \
  --address-prefix 10.0.0.0/16 \
  --subnet-name AzureFirewallSubnet \
  --subnet-prefix 10.0.1.0/24
az network vnet subnet create \
  --name Workload-SN \
  --resource-group Test-FW-RG \
  --vnet-name Test-FW-VN   \
  --address-prefix 10.0.2.0/24
az network vnet subnet create \
  --name Jump-SN \
  --resource-group Test-FW-RG \
  --vnet-name Test-FW-VN   \
  --address-prefix 10.0.3.0/24
```

### <a name="create-virtual-machines"></a>Создание виртуальных машин

Теперь создайте виртуальные машины для перехода и рабочей нагрузки и поместите их в соответствующие подсети.
Когда появится запрос, введите пароль для виртуальной машины.

Создайте виртуальную машину Srv перехода.

```azurecli-interactive
az vm create \
    --resource-group Test-FW-RG \
    --name Srv-Jump \
    --location eastus \
    --image win2016datacenter \
    --vnet-name Test-FW-VN \
    --subnet Jump-SN \
    --admin-username azureadmin
az vm open-port --port 3389 --resource-group Test-FW-RG --name Srv-Jump
```



Создайте сетевой Адаптер для Srv-работа с определенных IP-адресов сервера DNS и без общедоступного IP-адреса для тестирования.

```azurecli-interactive
az network nic create \
    -g Test-FW-RG \
    -n Srv-Work-NIC \
   --vnet-name Test-FW-VN \
   --subnet Workload-SN \
   --public-ip-address "" \
   --dns-servers 209.244.0.3 209.244.0.4
```

Теперь создайте виртуальную машину рабочей нагрузки.
Когда появится запрос, введите пароль для виртуальной машины.

```azurecli-interactive
az vm create \
    --resource-group Test-FW-RG \
    --name Srv-Work \
    --location eastus \
    --image win2016datacenter \
    --nics Srv-Work-NIC \
    --admin-username azureadmin
```

## <a name="deploy-the-firewall"></a>Развертывание брандмауэра

Теперь можно разверните брандмауэр в виртуальной сети.

```azurecli-interactive
az network firewall create \
    --name Test-FW01 \
    --resource-group Test-FW-RG \
    --location eastus
az network public-ip create \
    --name fw-pip \
    --resource-group Test-FW-RG \
    --location eastus \
    --allocation-method static \
    --sku standard
az network firewall ip-config create \
    --firewall-name Test-FW01 \
    --name FW-config \
    --public-ip-address fw-pip \
    --resource-group Test-FW-RG \
    --vnet-name Test-FW-VN
az network firewall update \
    --name Test-FW01 \
    --resource-group Test-FW-RG 
az network public-ip show \
    --name fw-pip \
    --resource-group Test-FW-RG
fwprivaddr="$(az network firewall ip-config list -g Test-FW-RG -f Test-FW01 --query "[?name=='FW-config'].privateIpAddress" --output tsv)"
```

Запишите частный IP-адрес. Вы будете использовать его позже при создании маршрута по умолчанию.

## <a name="create-a-default-route"></a>создание маршрута по умолчанию;

Создание таблицы с отключить распространение маршрутов BGP

```azurecli-interactive
az network route-table create \
    --name Firewall-rt-table \
    --resource-group Test-FW-RG \
    --location eastus \
    --disable-bgp-route-propagation true
```

Создайте маршрут.

```azurecli-interactive
az network route-table route create \
  --resource-group Test-FW-RG \
  --name DG-Route \
  --route-table-name Firewall-rt-table \
  --address-prefix 0.0.0.0/0 \
  --next-hop-type VirtualAppliance \
  --next-hop-ip-address $fwprivaddr
```

Привязка таблицы маршрутов к подсети

```azurecli-interactive
az network vnet subnet update \
    -n Workload-SN \
    -g Test-FW-RG \
    --vnet-name Test-FW-VN \
    --address-prefixes 10.0.2.0/24 \
    --route-table Firewall-rt-table
```

## <a name="configure-an-application-rule"></a>Настройка правила приложения

Приложение правило разрешает исходящий доступ к www.google.com.

```azurecli-interactive
az network firewall application-rule create \
   --collection-name App-Coll01 \
   --firewall-name Test-FW01 \
   --name Allow-Google \
   --protocols Http=80 Https=443 \
   --resource-group Test-FW-RG \
   --target-fqdns www.google.com \
   --source-addresses 10.0.2.0/24 \
   --priority 200 \
   --action Allow
```

Брандмауэр Azure содержит встроенную коллекцию правил для целевых полных доменных имен инфраструктуры, которые разрешены по умолчанию. Эти доменные имена предназначены для платформы и не могут использоваться для других целей. См. дополнительные сведения об [FQDN инфраструктуры](infrastructure-fqdns.md).

## <a name="configure-a-network-rule"></a>Настройка правила сети

Сетевое правило разрешает исходящий доступ к два IP-адреса на порт 53 (DNS).

```azurecli-interactive
az network firewall network-rule create \
   --collection-name Net-Coll01 \
   --destination-addresses 209.244.0.3 209.244.0.4 \
   --destination-ports 53 \
   --firewall-name Test-FW01 \
   --name Allow-DNS \
   --protocols UDP \
   --resource-group Test-FW-RG \
   --priority 200 \
   --source-addresses 10.0.2.0/24 \
   --action Allow
```

## <a name="test-the-firewall"></a>тестирование брандмауэра.

Теперь проверьте брандмауэр, чтобы убедиться, что он работает должным образом.

1. Обратите внимание, частный IP-адрес для **Srv рабочих** виртуальной машины:

   ```azurecli-interactive
   az vm list-ip-addresses \
   -g Test-FW-RG \
   -n Srv-Work
   ```

1. Подключите удаленный рабочий стол к виртуальной машине **Srv-Jump** и выполните вход. Откройте удаленный рабочий стол для **Srv рабочих** частных IP-адрес и входа.

3. На **SRV рабочих**, откройте окно PowerShell и выполните следующие команды:

   ```
   nslookup www.google.com
   nslookup www.microsoft.com
   ```

   Обе команды должна возвращать ответы, показывающий, что в брандмауэре получают запросы DNS.

1. Выполните следующие команды:

   ```
   Invoke-WebRequest -Uri https://www.google.com
   Invoke-WebRequest -Uri https://www.google.com

   Invoke-WebRequest -Uri https://www.microsoft.com
   Invoke-WebRequest -Uri https://www.microsoft.com
   ```

   Запросы www.google.com должны завершиться успешно и не выполняется, запросы www.microsoft.com. Это показывает, что правила брандмауэра работают должным образом.

Итак, теперь вы убедились в том, что правила брандмауэра работают:

* Вы можете разрешать имена DNS с помощью настроенного внешнего DNS-сервера.
* Вы можете перейти только к одному разрешенному имени FQDN.

## <a name="clean-up-resources"></a>Очистка ресурсов

Можно сохранить ресурсы брандмауэра к следующему руководству, или если больше не нужен, удалите **Test-FW-RG** группы ресурсов, чтобы удалить все ресурсы, связанные с брандмауэра:

```azurecli-interactive
az group delete \
  -n Test-FW-RG
```

## <a name="next-steps"></a>Дальнейшие действия

* [Учебник. Мониторинг журналов и метрик Брандмауэра Azure](./tutorial-diagnostics.md)
