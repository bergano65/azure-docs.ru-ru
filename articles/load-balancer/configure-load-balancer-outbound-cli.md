---
title: Настройка правил балансировки нагрузки и правил для исходящего трафика с использованием Azure CLI
titleSuffix: Azure Load Balancer
description: В этой статье показано, как настраивать правила для балансировки нагрузки и правила для исходящего трафика в Load Balancer (цен. категория "Стандартный") с использованием Azure CLI.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/01/2019
ms.author: allensu
ms.openlocfilehash: 7230b0c2b80137b068bbeacf43ab2133491a69b0
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74225469"
---
# <a name="configure-load-balancing-and-outbound-rules-in-standard-load-balancer-using-azure-cli"></a>Настройка правил балансировки нагрузки и правил для исходящего трафика в Load Balancer (цен. категория "Стандартный") с использованием Azure CLI

Из этого краткого руководства вы узнаете, как с помощью Azure CLI настроить правила для исходящего трафика в Load Balancer (цен. категория "Стандартный").  

Когда вы закончите работу с этим руководством, ваш ресурс Load Balancer будет содержать два внешних интерфейса и связанные с ними правила (одно для входящего трафика, а второе для исходящего).  Каждый внешний интерфейс содержит ссылку на общедоступный IP-адрес. В этом сценарии для входящего и исходящего трафика используются разные общедоступные IP-адреса.   Правило балансировки нагрузки обеспечивает балансировку нагрузки только входящего трафика, а правило для исходящего трафика управляет исходящим трафиком NAT, предоставленным для виртуальной машины.  В этом кратком руководстве используются два отдельных серверных пула: один для входящего и один для исходящего трафика, чтобы продемонстрировать возможности и обеспечить гибкость в этом сценарии.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

Если вы решили установить и использовать интерфейс командной строки локально, для работы с этим руководством вам понадобится Azure CLI 2.0.28 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0]( /cli/azure/install-azure-cli).

## <a name="create-resource-group"></a>Создать группу ресурсов

Создайте группу ресурсов с помощью команды [az group create](https://docs.microsoft.com/cli/azure/group). Группа ресурсов Azure является логическим контейнером, в котором происходит развертывание ресурсов Azure и управление ими.

В следующем примере создается группа ресурсов с именем *myresourcegroupoutbound* в расположении *eastus2*.

```azurecli-interactive
  az group create \
    --name myresourcegroupoutbound \
    --location eastus2
```
## <a name="create-virtual-network"></a>Создание виртуальной сети
Создайте виртуальную сеть с именем *myvnetoutbound* и подсетью с именем *mysubnetoutbound* в группе ресурсов *myresourcegroupoutbound* с помощью команды [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet).

```azurecli-interactive
  az network vnet create \
    --resource-group myresourcegroupoutbound \
    --name myvnetoutbound \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mysubnetoutbound \
    --subnet-prefix 192.168.0.0/24
```

## <a name="create-inbound-public-ip-address"></a>Создание общедоступного входящего IP-адреса 

Для доступа к веб-приложению через Интернет подсистеме балансировки нагрузки требуется общедоступный IP-адрес. Подсистема балансировки нагрузки уровня "Стандартный" поддерживает только стандартные общедоступные IP-адреса. С помощью команды [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip) создайте общедоступный IP-адрес ценовой категории "Стандартный" с именем *mypublicipinbound* в группе ресурсов *myresourcegroupoutbound*.

```azurecli-interactive
  az network public-ip create --resource-group myresourcegroupoutbound --name mypublicipinbound --sku standard
```

## <a name="create-outbound-public-ip-address"></a>Создание исходящего общедоступного IP-адреса 

Создайте IP-адрес категории "Стандартный" для внешней исходящей конфигурации Load Balancer с помощью [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip).

```azurecli-interactive
  az network public-ip create --resource-group myresourcegroupoutbound --name mypublicipoutbound --sku standard
```

## <a name="create-azure-load-balancer"></a>Создание Azure Load Balancer

В этом разделе описано, как создать и настроить следующие компоненты подсистемы балансировки нагрузки:
  - интерфейсный IP-адрес, который получает входящий трафик в подсистеме балансировки нагрузки;
  - Внутренний пул, в котором интерфейсный IP-адрес отправляет сетевой трафик с балансировкой нагрузки.
  - Внутренний пул для исходящих подключений. 
  - проверка работоспособности, определяющая работоспособность внутренних экземпляров виртуальной машины;
  - правило подсистемы балансировки нагрузки для входящего трафика, определяющее порядок распределения трафика между виртуальными машинами;
  - правило подсистемы балансировки нагрузки для исходящего трафика, определяющее порядок распределения трафика из виртуальных машин.

### <a name="create-load-balancer"></a>Создание балансировщика нагрузки

Создайте Load Balancer с входящим IP-адресом, выполнив команду [AZ Network фунтов Create](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest) именованной *балансировки нагрузки* , включающую внешнюю IP-конфигурацию входящего трафика и серверный пул *бепулинбаунд* , связанный с общедоступным IP-адресом *. мипублиЦипинбаунд* , созданный на предыдущем шаге.

```azurecli-interactive
  az network lb create \
    --resource-group myresourcegroupoutbound \
    --name lb \
    --sku standard \
    --backend-pool-name bepoolinbound \
    --frontend-ip-name myfrontendinbound \
    --location eastus2 \
    --public-ip-address mypublicipinbound   
  ```

### <a name="create-outbound-pool"></a>Создать исходящий пул

Создайте дополнительный внутренний пул адресов для определения исходящих подключений для пула виртуальных машин с помощью команды [AZ Network фунтов Address-Pool Create](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest) с именем *бепулаутбаунд*.  Создание отдельного исходящего пула обеспечивает максимальную гибкость, но вы можете опустить этот шаг и использовать только входящие *бепулинбаунд* .

```azurecli-interactive
  az network lb address-pool create \
    --resource-group myresourcegroupoutbound \
    --lb-name lb \
    --name bepooloutbound
```

### <a name="create-outbound-frontend-ip"></a>Создание IP-адреса исходящего внешнего интерфейса
Создайте IP-конфигурацию исходящего внешнего интерфейса для Load Balancer с помощью команды [az network lb frontend-ip create](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest) с исходящей интерфейсной конфигурацией IP-адресов с именем *myfrontendoutbound*, которая связана с общедоступным IP-адресом *mypublicipoutbound*

```azurecli-interactive
  az network lb frontend-ip create \
    --resource-group myresourcegroupoutbound \
    --name myfrontendoutbound \
    --lb-name lb \
    --public-ip-address mypublicipoutbound 
  ```

### <a name="create-health-probe"></a>Создание пробы работоспособности

Проба работоспособности проверяет все экземпляры виртуальной машины, чтобы убедиться, что они могут отправлять сетевой трафик. Экземпляр виртуальной машины с неудачной пробой удаляется из балансировщика нагрузки, пока не перейдет в оперативный режим и проба не определит его работоспособность. Создайте зонд работоспособности с помощью команды [az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe?view=azure-cli-latest), чтобы отслеживать работоспособность виртуальных машин. 

```azurecli-interactive
  az network lb probe create \
    --resource-group myresourcegroupoutbound \
    --lb-name lb \
    --name http \
    --protocol http \
    --port 80 \
    --path /  
```

### <a name="create-load-balancing-rule"></a>Создание правила балансировки нагрузки

Правило подсистемы балансировки нагрузки определяет интерфейсную конфигурацию IP-адресов для входящего трафика и внутренний пул для приема трафика, а также порты источника и назначения. Создайте правило балансировки нагрузки *myinboundlbrule* с помощью команды [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest) для прослушивания порта 80 интерфейсным пулом *myfrontendinbound* и отправки сетевого трафика со сбалансированной нагрузкой на внутренний пул адресов *bepool*, также используя порт 80. 

>[!NOTE]
>Параметр правила балансировки нагрузки --disable-outbound-snat запрещает автоматическое применение (S)NAT для исходящего трафика. NAT для исходящего трафика предоставляется только с помощью правила для исходящего трафика.

```azurecli-interactive
az network lb rule create \
--resource-group myresourcegroupoutbound \
--lb-name lb \
--name inboundlbrule \
--protocol tcp \
--frontend-port 80 \
--backend-port 80 \
--probe http \
--frontend-ip-name myfrontendinbound \
--backend-pool-name bepoolinbound \
--disable-outbound-snat
```

### <a name="create-outbound-rule"></a>Создание правила для исходящего трафика

Правило для исходящего трафика определяет общедоступный IP-адрес внешнего интерфейса, представленный внешним интерфейсом *myfrontendoutbound*, который будет использоваться для всего исходящего трафика NAT, а также внутреннего пула, к которому применяется это правило.  Создайте правило для исходящего трафика *myoutboundrule* для сетевого преобразования исходящего трафика всех виртуальных машин (IP-конфигурации NIC) в серверном пуле *bepool*.  Приведенная ниже команда также изменяет время ожидания исходящего трафика в режиме простоя от 4 до 15 минут и выделяет 10 000 портов SNAT вместо 1024.  См. дополнительные сведения в статье о [правилах для исходящего трафика](https://aka.ms/lboutboundrules).

```azurecli-interactive
az network lb outbound-rule create \
 --resource-group myresourcegroupoutbound \
 --lb-name lb \
 --name outboundrule \
 --frontend-ip-configs myfrontendoutbound \
 --protocol All \
 --idle-timeout 15 \
 --outbound-ports 10000 \
 --address-pool bepooloutbound
```

Если вы не хотите использовать отдельный исходящий пул, можно изменить аргумент пула адресов в предыдущей команде, указав вместо этого параметр *бепулинбаунд* .  Рекомендуется использовать отдельные пулы для обеспечения гибкости и удобочитаемости результирующей конфигурации.

На этом этапе можно приступить к добавлению виртуальной машины в серверный пул *бепулинбаунд* __и__ *бепулаутбаунд* , обновив IP-конфигурацию соответствующих сетевых ресурсов с помощью команды [AZ Network NIC IP-config адрес-пул добавить](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest).

## <a name="clean-up-resources"></a>Очистка ресурсов

Вы можете удалить ставшие ненужными группу ресурсов, подсистему балансировки нагрузки и все связанные с ними ресурсы, выполнив команду [az group delete](/cli/azure/group#az-group-delete).

```azurecli-interactive 
  az group delete --name myresourcegroupoutbound
```

## <a name="next-steps"></a>Дополнительная информация
Изучая эту статью, вы создали Load Balancer (цен. категория "Стандартный"), настроили правила балансировки нагрузки для входящего трафика, а также настроили проверку работоспособности для виртуальных машин в серверном пуле. Чтобы узнать больше об Azure Load Balancer, ознакомьтесь с другими руководствами по этой службе.

> [!div class="nextstepaction"]
> [Руководства по Azure Load Balancer](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
