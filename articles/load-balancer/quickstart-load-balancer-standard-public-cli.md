---
title: Краткое руководство. Создание общедоступной подсистемы балансировки нагрузки, используя Azure СLI
titleSuffix: Azure Load Balancer
description: В рамках этого краткого руководства вы узнаете, как создать общедоступную подсистему балансировки нагрузки с помощью Azure CLI
services: load-balancer
documentationcenter: na
author: asudbring
manager: KumudD
tags: azure-resource-manager
Customer intent: I want to create a load balancer so that I can load balance internet traffic to VMs.
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/20/2020
ms.author: allensu
ms.custom: mvc, devx-track-javascript, devx-track-azurecli
ms.openlocfilehash: c80b4e57c94737778d8e6f63804d95f4d1b35fb0
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/31/2020
ms.locfileid: "87501803"
---
# <a name="quickstart-create-a-public-load-balancer-to-load-balance-vms-using-azure-cli"></a>Краткое руководство. Создание общедоступной подсистемы балансировки нагрузки с помощью Azure CLI для распределения нагрузки между виртуальными машинами

Начните работу с Azure Load Balancer, создав с помощью Azure CLI общедоступную подсистему балансировки нагрузки и три виртуальные машины.

## <a name="prerequisites"></a>Предварительные требования

- Учетная запись Azure с активной подпиской. [Создайте учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) бесплатно.
- Локальная установка Azure CLI или Azure Cloud Shell

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

Если вы решили установить и использовать CLI локально, для выполнения инструкций из этого руководства вам потребуется Azure CLI версии 2.0.28 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Группа ресурсов Azure является логическим контейнером, в котором происходит развертывание ресурсов Azure и управление ими.

Создайте группу ресурсов с помощью команды [az group create](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create):

* с именем **myResourceGroupLB**; 
* в расположении **eastus**.

```azurecli-interactive
  az group create \
    --name myResourceGroupLB \
    --location eastus
```
---

# <a name="option-1-default-create-a-load-balancer-standard-sku"></a>[Вариант 1 (по умолчанию). Создание подсистемы балансировки нагрузки (ценовая категория "Стандартный")](#tab/option-1-create-load-balancer-standard)

>[!NOTE]
>Для производственных рабочих нагрузок рекомендуется использовать подсистему балансировки нагрузки ценовой категории "Стандартный". Дополнительные сведения о доступных ценовых категориях см. в статье **[Номера SKU для Azure Load Balancer](skus.md)** .


## <a name="create-a-public-ip-address"></a>Создание общедоступного IP-адреса

Для доступа к веб-приложению через Интернет подсистеме балансировки нагрузки требуется общедоступный IP-адрес. 

Используйте [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create), чтобы:

* создать стандартный избыточный между зонами общедоступный IP-адрес с именем **myPublicIP**;
* в **myResourceGroupLB**.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupLB \
    --name myPublicIP \
    --sku Standard
```

Создайте избыточный между зонами общедоступный IP-адрес в зоне 1, используя приведенные ниже сведения:

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupLB \
    --name myPublicIP \
    --sku Standard \
    --zone 1
```

## <a name="create-standard-load-balancer"></a>Создание подсистемы балансировки нагрузки (цен. категория "Стандартный")

В этом разделе описано, как создать и настроить следующие компоненты подсистемы балансировки нагрузки:

  * интерфейсный пул IP-адресов, который получает входящий трафик в подсистеме балансировки нагрузки;
  * внутренний пул IP-адресов, на который интерфейсный пул отправляет трафик с балансировкой нагрузки;
  * проверка работоспособности, определяющая работоспособность внутренних экземпляров виртуальной машины;
  * правило подсистемы балансировки нагрузки, определяющее порядок распределения трафика между виртуальными машинами.

### <a name="create-the-load-balancer-resource"></a>Создание ресурса подсистемы балансировки нагрузки

С помощью команды [az network lb create](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest#az-network-lb-create) создайте общедоступную подсистему балансировки нагрузки:

* с именем **myLoadBalancer**,
* пулом переднего плана **myFrontEnd**
* и серверным пулом **myBackEndPool**,
* связанным с общедоступным IP-адресом **myPublicIP**, созданным на предыдущем шаге. 

```azurecli-interactive
  az network lb create \
    --resource-group myResourceGroupLB \
    --name myLoadBalancer \
    --sku Standard \
    --public-ip-address myPublicIP \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool       
```

### <a name="create-the-health-probe"></a>Создание зонда работоспособности

При пробе работоспособности выполняется проверка всех экземпляров виртуальной машины, чтобы убедиться, что они могут отправлять сетевой трафик. 

Виртуальная машина с неудачной пробой удаляется из подсистемы балансировки нагрузки и снова добавляется в нее после устранения сбоя.

Создайте пробу работоспособности с помощью команды [az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe?view=azure-cli-latest#az-network-lb-probe-create), которая:

* отслеживает работоспособность виртуальных машин;
* имеет имя **myHealthProbe**;
* протокол **TCP**;
* отслеживает **порт 80**.

```azurecli-interactive
  az network lb probe create \
    --resource-group myResourceGroupLB \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80   
```

### <a name="create-the-load-balancer-rule"></a>Создание правила подсистемы балансировки нагрузки

Правило подсистемы балансировки нагрузки определяет:

* конфигурацию интерфейсных IP-адресов для входящего трафика;
* серверный пул IP-адресов для приема трафика;
* требуемые порты источника и назначения. 

С помощью команды [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest#az-network-lb-rule-create) создайте правило подсистемы балансировки нагрузки, которое:

* имеет имя **myHTTPRule**;
* ожидает передачи данных от **порта 80**, используемого интерфейсным пулом **myFrontEnd**;
* перенаправляет трафик, для которого настроена балансировка нагрузки, ко внутреннему пулу адресов **myBackEndPool**, который использует **порт 80**; 
* использует пробу работоспособности **myHealthProbe**;
* протокол **TCP**.
* Вы можете включить преобразование сетевого адреса (SNAT) источника исходящего трафика с помощью внешнего IP-адреса.

```azurecli-interactive
  az network lb rule create \
    --resource-group myResourceGroupLB \
    --lb-name myLoadBalancer \
    --name myHTTPRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe \
    --disable-outbound-snat true 
```

## <a name="configure-virtual-network"></a>Настройка виртуальной сети

Прежде чем развертывать виртуальные машины и тестировать подсистему балансировки нагрузки, создайте вспомогательные ресурсы виртуальной сети.

### <a name="create-a-virtual-network"></a>Создание виртуальной сети

Создайте виртуальную сеть с помощью команды [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet?view=azure-cli-latest#az-network-vnet-createt):

* с именем **myVNet**;
* именем подсети **myBackendSubnet**;
* в группе ресурсов **myResourceGroupLB**;
* расположении **eastus**.

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroupLB \
    --location eastus \
    --name myVNet \
    --subnet-name myBackendSubnet
```

### <a name="create-a-network-security-group"></a>Создание группы безопасности сети

Для подсистемы балансировки нагрузки уровня "Стандартный" у серверных виртуальных машин должны быть сетевые интерфейсы, связанные с группой безопасности сети. 

Создайте группу безопасности сети с помощью команды [az network nsg create](https://docs.microsoft.com/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-create):

* с именем **myNSG**;
* в группе ресурсов **myResourceGroupLB**.

```azurecli-interactive
  az network nsg create \
    --resource-group myResourceGroupLB \
    --name myNSG
```

### <a name="create-a-network-security-group-rule"></a>Создание правила группы безопасности сети

Создайте правило группы безопасности сети с помощью команды [az network nsg rule create](https://docs.microsoft.com/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create), используя такие сведения:

* имя **myNSGRuleHTTP**;
* группа безопасности сети, созданная на предыдущем шаге, **myNSG**;
* группа ресурсов **myResourceGroupLB**;
* протокол **TCP**;
* направление **Входящий**;
* источник **(*)** ;
* назначение **(*)** ;
* порт назначения **порт 80**;
* доступ **Разрешить**;
* приоритет **200**.

```azurecli-interactive
  az network nsg rule create \
    --resource-group myResourceGroupLB \
    --nsg-name myNSG \
    --name myNSGRuleHTTP \
    --protocol tcp \
    --direction inbound \
    --source-address-prefix '*' \
    --source-port-range '*' \
    --destination-address-prefix '*' \
    --destination-port-range 80 \
    --access allow \
    --priority 200
```

### <a name="create-network-interfaces-for-the-virtual-machines"></a>Создание сетевых интерфейсов для виртуальных машин

Создайте три сетевых интерфейса с помощью команды [az network nic create](https://docs.microsoft.com/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create), используя следующие сведения:

#### <a name="vm1"></a>VM1

* имя **myNicVM1**;
* группа ресурсов **myResourceGroupLB**;
* виртуальная сеть **myVNet**;
* подсеть **myBackendSubnet**;
* группа безопасности сети **myNSG**;
* подключена к подсистеме балансировки нагрузки **myLoadBalancer** в **myBackEndPool**.

```azurecli-interactive

  az network nic create \
    --resource-group myResourceGroupLB \
    --name myNicVM1 \
    --vnet-name myVNet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG \
    --lb-name myLoadBalancer \
    --lb-address-pools myBackEndPool
```
#### <a name="vm2"></a>VM2

* имя **myNicVM2**;
* группа ресурсов **myResourceGroupLB**;
* виртуальная сеть **myVNet**;
* подсеть **myBackendSubnet**;
* группа безопасности сети **myNSG**;
* подключена к подсистеме балансировки нагрузки **myLoadBalancer** в **myBackEndPool**.

```azurecli-interactive
  az network nic create \
    --resource-group myResourceGroupLB \
    --name myNicVM2 \
    --vnet-name myVnet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG \
    --lb-name myLoadBalancer \
    --lb-address-pools myBackEndPool
```
#### <a name="vm3"></a>VM3

* имя **myNicVM3**;
* группа ресурсов **myResourceGroupLB**;
* виртуальная сеть **myVNet**;
* подсеть **myBackendSubnet**;
* группа безопасности сети **myNSG**;
* подключена к подсистеме балансировки нагрузки **myLoadBalancer** в **myBackEndPool**.

```azurecli-interactive
  az network nic create \
    --resource-group myResourceGroupLB \
    --name myNicVM3 \
    --vnet-name myVnet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG \
    --lb-name myLoadBalancer \
    --lb-address-pools myBackEndPool
```

## <a name="create-backend-servers"></a>Создание внутренних серверов

В этом разделе показано, как создать:

* файл конфигурации облака с именем **cloud-init.txt** для конфигурации сервера;
* три виртуальные машины, которые будут использоваться в качестве внутренних серверов для подсистемы балансировки нагрузки.

### <a name="create-cloud-init-configuration-file"></a>Создание файла конфигурации cloud-init

Тот же самый файл конфигурации cloud-init можно использовать и для установки NGINX, а также запуска приложения Node.js "Hello World" на виртуальной машине Linux. 

В текущей оболочке создайте файл с именем cloud-init.txt. Скопируйте следующую конфигурацию и вставьте ее в оболочку. Убедитесь, что весь файл cloud-init скопирован правильно, особенно первая строка.

```yaml
#cloud-config
package_upgrade: true
packages:
  - nginx
  - nodejs
  - npm
write_files:
  - owner: www-data:www-data
  - path: /etc/nginx/sites-available/default
    content: |
      server {
        listen 80;
        location / {
          proxy_pass http://localhost:3000;
          proxy_http_version 1.1;
          proxy_set_header Upgrade $http_upgrade;
          proxy_set_header Connection keep-alive;
          proxy_set_header Host $host;
          proxy_cache_bypass $http_upgrade;
        }
      }
  - owner: azureuser:azureuser
  - path: /home/azureuser/myapp/index.js
    content: |
      var express = require('express')
      var app = express()
      var os = require('os');
      app.get('/', function (req, res) {
        res.send('Hello World from host ' + os.hostname() + '!')
      })
      app.listen(3000, function () {
        console.log('Hello world app listening on port 3000!')
      })
runcmd:
  - service nginx restart
  - cd "/home/azureuser/myapp"
  - npm init
  - npm install express -y
  - nodejs index.js
```
### <a name="create-virtual-machines"></a>Создание виртуальных машин

Создайте виртуальные машины с помощью команды [az vm create](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create), используя следующие сведения:

#### <a name="vm1"></a>VM1
* имя **myVM1**;
* группа ресурсов **myResourceGroupLB**;
* подключена к сетевому интерфейсу **myNicVM1**;
* образ виртуальной машины **UbuntuLTS**;
* файл конфигурации **cloud-init.txt**, созданный на шаге выше;
* в **зоне 1**.

```azurecli-interactive
  az vm create \
    --resource-group myResourceGroupLB \
    --name myVM1 \
    --nics myNicVM1 \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --zone 1 \
    --no-wait
    
```
#### <a name="vm2"></a>VM2
* имя **myVM2**;
* группа ресурсов **myResourceGroupLB**;
* подключена к сетевому интерфейсу **myNicVM2**;
* образ виртуальной машины **UbuntuLTS**;
* файл конфигурации **cloud-init.txt**, созданный на шаге выше;
* в **зоне 2**.

```azurecli-interactive
  az vm create \
    --resource-group myResourceGroupLB \
    --name myVM2 \
    --nics myNicVM2 \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --zone 2 \
    --no-wait
```

#### <a name="vm3"></a>VM3
* имя **myVM3**;
* группа ресурсов **myResourceGroupLB**;
* подключена к сетевому интерфейсу **myNicVM3**;
* образ виртуальной машины **UbuntuLTS**;
* файл конфигурации **cloud-init.txt**, созданный на шаге выше;
* в **зоне 3**.

```azurecli-interactive
   az vm create \
    --resource-group myResourceGroupLB \
    --name myVM3 \
    --nics myNicVM3 \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --zone 3 \
    --no-wait
```
На развертывание виртуальных машин может потребоваться несколько минут.

## <a name="create-outbound-rule-configuration"></a>Создание конфигурации правила для исходящего трафика
Правила для исходящего трафика подсистемы балансировки нагрузки настраивают исходящий SNAT для виртуальных машин во внутреннем пуле. 

Дополнительные сведения об исходящих подключениях см. в статье [Исходящие подключения в Azure](load-balancer-outbound-connections.md).

### <a name="create-outbound-public-ip-address-or-public-ip-prefix"></a>Создайте исходящий общедоступный IP-адрес или префикс общедоступного IP-адреса.

Используйте команду [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create), чтобы создать отдельный IP-адрес для исходящего подключения.  

Используйте команду [az network public-ip prefix create](https://docs.microsoft.com/cli/azure/network/public-ip/prefix?view=azure-cli-latest#az-network-public-ip-prefix-create), чтобы создать префикс общедоступного IP-адреса для исходящего подключения.

Дополнительные сведения о масштабировании исходящего преобразования сетевых адресов (NAT) и исходящего подключения см. в разделе [Масштабирование NAT для исходящего трафика при использовании нескольких IP-адресов](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#scale).

#### <a name="public-ip"></a>Общедоступный IP-адрес

* с именем **myPublicIPOutbound**;
* в **myResourceGroupLB**.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupLB \
    --name myPublicIPOutbound \
    --sku Standard
```

Создайте избыточный между зонами общедоступный IP-адрес в зоне 1, используя приведенные ниже сведения:

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupLB \
    --name myPublicIPOutbound \
    --sku Standard \
    --zone 1
```
#### <a name="public-ip-prefix"></a>Префикс общедоступного IP-адреса

* с именем **myPublicIPPrefixOutbound**;
* в **myResourceGroupLB**.
* длина префикса **28**.

```azurecli-interactive
  az network public-ip prefix create \
    --resource-group myResourceGroupLB \
    --name myPublicIPPrefixOutbound \
    --length 28
```
Создайте зональный общедоступный IP-адрес в зоне доступности 1, используя приведенные ниже сведения:

```azurecli-interactive
  az network public-ip prefix create \
    --resource-group myResourceGroupLB \
    --name myPublicIPPrefixOutbound \
    --length 28 \
    --zone 1
```

### <a name="create-outbound-frontend-ip-configuration"></a>Создайте интерфейсные IP-конфигурации для исходящего трафика

Создайте новую интерфейсную IP-конфигурацию с помощью [az network lb frontend-ip create ](https://docs.microsoft.com/cli/azure/network/lb/frontend-ip?view=azure-cli-latest#az-network-lb-frontend-ip-create):

Выберите команды "общедоступный IP-адрес" или "префикс общедоступного IP-адреса" на основе решения на предыдущем шаге.

#### <a name="public-ip"></a>Общедоступный IP-адрес

* с именем **myFrontEndOutbound**;
* в группе ресурсов **myResourceGroupLB**;
* связанный с общедоступным IP-адресом **myPublicIPOutbound**;
* связанный с подсистемой балансировки нагрузки **myLoadBalancer**.

```azurecli-interactive
  az network lb frontend-ip create \
    --resource-group myResourceGroupLB \
    --name myFrontEndOutbound \
    --lb-name myLoadBalancer \
    --public-ip-address myPublicIPOutbound 
```

#### <a name="public-ip-prefix"></a>Префикс общедоступного IP-адреса

* с именем **myFrontEndOutbound**;
* в группе ресурсов **myResourceGroupLB**;
* связанный с префиксом общедоступного IP-адреса **myPublicIPPrefixOutbound**;
* связанный с подсистемой балансировки нагрузки **myLoadBalancer**.

```azurecli-interactive
  az network lb frontend-ip create \
    --resource-group myResourceGroupLB \
    --name myFrontEndOutbound \
    --lb-name myLoadBalancer \
    --public-ip-prefix myPublicIPPrefixOutbound 
```

### <a name="create-outbound-pool"></a>Создание исходящего пула

Создайте исходящий пул, используя команду [az network lb address-pool create](https://docs.microsoft.com/cli/azure/network/lb/address-pool?view=azure-cli-latest#az-network-lb-address-pool-create) и cледующие сведения:

* имя **myBackEndPoolOutbound**;
* в группе ресурсов **myResourceGroupLB**;
* связанный с подсистемой балансировки нагрузки **myLoadBalancer**.

```azurecli-interactive
  az network lb address-pool create \
    --resource-group myResourceGroupLB \
    --lb-name myLoadBalancer \
    --name myBackendPoolOutbound
```
### <a name="create-outbound-rule"></a>Создание правила для исходящего трафика

Создайте правило для исходящего трафика для исходящего внутреннего пула, используя команду [az network lb outbound-rule create](https://docs.microsoft.com/cli/azure/network/lb/outbound-rule?view=azure-cli-latest#az-network-lb-outbound-rule-create) и следующие сведения:

* имя **myOutboundRule**;
* в группе ресурсов **myResourceGroupLB**;
* связь с подсистемой балансировки нагрузки **myLoadBalancer**;
* связь с внешним интерфейсом **myFrontEndOutbound**;
* протокол **Все**;
* время ожидания перед переходом в режим простоя **15**;
* исходящие порты **10000**;
* связь с серверным пулом **myBackEndPoolOutbound**.

```azurecli-interactive
  az network lb outbound-rule create \
    --resource-group myResourceGroupLB \
    --lb-name myLoadBalancer \
    --name myOutboundRule \
    --frontend-ip-configs myFrontEndOutbound \
    --protocol All \
    --idle-timeout 15 \
    --outbound-ports 10000 \
    --address-pool myBackEndPoolOutbound
```
### <a name="add-virtual-machines-to-outbound-pool"></a>Добавление виртуальных машин в исходящий пул

Добавьте виртуальные машины в исходящий пул, используя команду [az network nic ip-config address-pool add](https://docs.microsoft.com/cli/azure/network/nic/ip-config/address-pool?view=azure-cli-latest#az-network-nic-ip-config-address-pool-add) и следующие сведения:


#### <a name="vm1"></a>VM1
* в серверном пуле адресов **myBackEndPoolOutbound**;
* в группе ресурсов **myResourceGroupLB**;
* связь с сетевым интерфейсом **myNicVM1** и **ipconfig1**;
* связанный с подсистемой балансировки нагрузки **myLoadBalancer**.

```azurecli-interactive
  az network nic ip-config address-pool add \
   --address-pool myBackendPoolOutbound \
   --ip-config-name ipconfig1 \
   --nic-name myNicVM1 \
   --resource-group myResourceGroupLB \
   --lb-name myLoadBalancer
```

#### <a name="vm2"></a>VM2
* в серверном пуле адресов **myBackEndPoolOutbound**;
* в группе ресурсов **myResourceGroupLB**;
* связь с сетевым интерфейсом **myNicVM2** и **ipconfig1**;
* связанный с подсистемой балансировки нагрузки **myLoadBalancer**.

```azurecli-interactive
  az network nic ip-config address-pool add \
   --address-pool myBackendPoolOutbound \
   --ip-config-name ipconfig1 \
   --nic-name myNicVM2 \
   --resource-group myResourceGroupLB \
   --lb-name myLoadBalancer
```

#### <a name="vm3"></a>VM3
* в серверном пуле адресов **myBackEndPoolOutbound**;
* в группе ресурсов **myResourceGroupLB**;
* связь с сетевым интерфейсом **myNicVM3** и **ipconfig1**;
* связанный с подсистемой балансировки нагрузки **myLoadBalancer**.

```azurecli-interactive
  az network nic ip-config address-pool add \
   --address-pool myBackendPoolOutbound \
   --ip-config-name ipconfig1 \
   --nic-name myNicVM3 \
   --resource-group myResourceGroupLB \
   --lb-name myLoadBalancer
```

# <a name="option-2-create-a-load-balancer-basic-sku"></a>[Вариант 2. Создание подсистемы балансировки нагрузки ценовой категории "Базовый"](#tab/option-1-create-load-balancer-basic)

>[!NOTE]
>Для производственных рабочих нагрузок рекомендуется использовать подсистему балансировки нагрузки ценовой категории "Стандартный". Дополнительные сведения о доступных ценовых категориях см. в статье **[Номера SKU для Azure Load Balancer](skus.md)** .


## <a name="create-a-public-ip-address"></a>Создание общедоступного IP-адреса

Для доступа к веб-приложению через Интернет подсистеме балансировки нагрузки требуется общедоступный IP-адрес. 

Используйте [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create), чтобы:

* создать стандартный избыточный между зонами общедоступный IP-адрес с именем **myPublicIP**;
* в **myResourceGroupLB**.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupLB \
    --name myPublicIP \
    --sku Basic
```

## <a name="create-basic-load-balancer"></a>Создание подсистемы балансировки нагрузки уровня "Базовый"

В этом разделе описано, как создать и настроить следующие компоненты подсистемы балансировки нагрузки:

  * интерфейсный пул IP-адресов, который получает входящий трафик в подсистеме балансировки нагрузки;
  * внутренний пул IP-адресов, на который интерфейсный пул отправляет трафик с балансировкой нагрузки;
  * проверка работоспособности, определяющая работоспособность внутренних экземпляров виртуальной машины;
  * правило подсистемы балансировки нагрузки, определяющее порядок распределения трафика между виртуальными машинами.

### <a name="create-the-load-balancer-resource"></a>Создание ресурса подсистемы балансировки нагрузки

С помощью команды [az network lb create](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest#az-network-lb-create) создайте общедоступную подсистему балансировки нагрузки:

* с именем **myLoadBalancer**,
* пулом переднего плана **myFrontEnd**
* и серверным пулом **myBackEndPool**,
* связанным с общедоступным IP-адресом **myPublicIP**, созданным на предыдущем шаге. 

```azurecli-interactive
  az network lb create \
    --resource-group myResourceGroupLB \
    --name myLoadBalancer \
    --sku Basic \
    --public-ip-address myPublicIP \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool       
```

### <a name="create-the-health-probe"></a>Создание зонда работоспособности

При пробе работоспособности выполняется проверка всех экземпляров виртуальной машины, чтобы убедиться, что они могут отправлять сетевой трафик. 

Виртуальная машина с неудачной пробой удаляется из подсистемы балансировки нагрузки и снова добавляется в нее после устранения сбоя.

Создайте пробу работоспособности с помощью команды [az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe?view=azure-cli-latest#az-network-lb-probe-create), которая:

* отслеживает работоспособность виртуальных машин;
* имеет имя **myHealthProbe**;
* протокол **TCP**;
* отслеживает **порт 80**.

```azurecli-interactive
  az network lb probe create \
    --resource-group myResourceGroupLB \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80   
```

### <a name="create-the-load-balancer-rule"></a>Создание правила подсистемы балансировки нагрузки

Правило подсистемы балансировки нагрузки определяет:

* конфигурацию интерфейсных IP-адресов для входящего трафика;
* серверный пул IP-адресов для приема трафика;
* требуемые порты источника и назначения. 

С помощью команды [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest#az-network-lb-rule-create) создайте правило подсистемы балансировки нагрузки, которое:

* имеет имя **myHTTPRule**;
* ожидает передачи данных от **порта 80**, используемого интерфейсным пулом **myFrontEnd**;
* перенаправляет трафик, для которого настроена балансировка нагрузки, ко внутреннему пулу адресов **myBackEndPool**, который использует **порт 80**; 
* использует пробу работоспособности **myHealthProbe**;
* протокол **TCP**.

```azurecli-interactive
  az network lb rule create \
    --resource-group myResourceGroupLB \
    --lb-name myLoadBalancer \
    --name myHTTPRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe
```

## <a name="configure-virtual-network"></a>Настройка виртуальной сети

Прежде чем развертывать виртуальные машины и тестировать подсистему балансировки нагрузки, создайте вспомогательные ресурсы виртуальной сети.

### <a name="create-a-virtual-network"></a>Создание виртуальной сети

Создайте виртуальную сеть с помощью команды [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet?view=azure-cli-latest#az-network-vnet-createt):

* с именем **myVNet**;
* именем подсети **myBackendSubnet**;
* в группе ресурсов **myResourceGroupLB**;
* расположении **eastus**.

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroupLB \
    --location eastus \
    --name myVNet \
    --subnet-name myBackendSubnet
```

### <a name="create-a-network-security-group"></a>Создание группы безопасности сети

Для подсистемы балансировки нагрузки уровня "Стандартный" у серверных виртуальных машин должны быть сетевые интерфейсы, связанные с группой безопасности сети. 

Создайте группу безопасности сети с помощью команды [az network nsg create](https://docs.microsoft.com/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-create):

* с именем **myNSG**;
* в группе ресурсов **myResourceGroupLB**;

```azurecli-interactive
  az network nsg create \
    --resource-group myResourceGroupLB \
    --name myNSG
```

### <a name="create-a-network-security-group-rule"></a>Создание правила группы безопасности сети

Создайте правило группы безопасности сети с помощью команды [az network nsg rule create](https://docs.microsoft.com/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create), используя такие сведения:

* имя **myNSGRuleHTTP**;
* группа безопасности сети, созданная на предыдущем шаге, **myNSG**;
* в группе ресурсов **myResourceGroupLB**;
* протокол **TCP**.
* направление **Входящий**;
* источник **(*)** ;
* назначение **(*)** ;
* порт назначения **порт 80**;
* доступ **Разрешить**;
* приоритет **200**.

```azurecli-interactive
  az network nsg rule create \
    --resource-group myResourceGroupLB \
    --nsg-name myNSG \
    --name myNSGRuleHTTP \
    --protocol tcp \
    --direction inbound \
    --source-address-prefix '*' \
    --source-port-range '*' \
    --destination-address-prefix '*' \
    --destination-port-range 80 \
    --access allow \
    --priority 200
```

### <a name="create-network-interfaces-for-the-virtual-machines"></a>Создание сетевых интерфейсов для виртуальных машин

Создайте три сетевых интерфейса с помощью команды [az network nic create](https://docs.microsoft.com/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create), используя следующие сведения:

#### <a name="vm1"></a>VM1

* имя **myNicVM1**;
* в группе ресурсов **myResourceGroupLB**;
* виртуальная сеть **myVNet**;
* подсеть **myBackendSubnet**;
* группа безопасности сети **myNSG**;
* подключена к подсистеме балансировки нагрузки **myLoadBalancer** в **myBackEndPool**.

```azurecli-interactive

  az network nic create \
    --resource-group myResourceGroupLB \
    --name myNicVM1 \
    --vnet-name myVNet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG \
    --lb-name myLoadBalancer \
    --lb-address-pools myBackEndPool
```
#### <a name="vm2"></a>VM2

* имя **myNicVM2**;
* в группе ресурсов **myResourceGroupLB**;
* виртуальная сеть **myVNet**;
* подсеть **myBackendSubnet**;
* группа безопасности сети **myNSG**;
* подключена к подсистеме балансировки нагрузки **myLoadBalancer** в **myBackEndPool**.

```azurecli-interactive
  az network nic create \
    --resource-group myResourceGroupLB \
    --name myNicVM2 \
    --vnet-name myVnet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG \
    --lb-name myLoadBalancer \
    --lb-address-pools myBackEndPool
```
#### <a name="vm3"></a>VM3

* имя **myNicVM3**;
* в группе ресурсов **myResourceGroupLB**;
* виртуальная сеть **myVNet**;
* подсеть **myBackendSubnet**;
* группа безопасности сети **myNSG**;
* подключена к подсистеме балансировки нагрузки **myLoadBalancer** в **myBackEndPool**.

```azurecli-interactive
  az network nic create \
    --resource-group myResourceGroupLB \
    --name myNicVM3 \
    --vnet-name myVnet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG \
    --lb-name myLoadBalancer \
    --lb-address-pools myBackEndPool
```

## <a name="create-backend-servers"></a>Создание внутренних серверов

В этом разделе показано, как создать:

* файл конфигурации облака с именем **cloud-init.txt** для конфигурации сервера; 
* группу доступности для виртуальных машин;
* три виртуальные машины, которые будут использоваться в качестве внутренних серверов для подсистемы балансировки нагрузки.


Чтобы проверить, успешно ли создана подсистема балансировки нагрузки, установите NGINX на виртуальных машинах.

### <a name="create-cloud-init-configuration-file"></a>Создание файла конфигурации cloud-init

Тот же самый файл конфигурации cloud-init можно использовать и для установки NGINX, а также запуска приложения Node.js "Hello World" на виртуальной машине Linux. 

В текущей оболочке создайте файл с именем cloud-init.txt. Скопируйте следующую конфигурацию и вставьте ее в оболочку. Убедитесь, что весь файл cloud-init скопирован правильно, особенно первая строка.

```yaml
#cloud-config
package_upgrade: true
packages:
  - nginx
  - nodejs
  - npm
write_files:
  - owner: www-data:www-data
  - path: /etc/nginx/sites-available/default
    content: |
      server {
        listen 80;
        location / {
          proxy_pass http://localhost:3000;
          proxy_http_version 1.1;
          proxy_set_header Upgrade $http_upgrade;
          proxy_set_header Connection keep-alive;
          proxy_set_header Host $host;
          proxy_cache_bypass $http_upgrade;
        }
      }
  - owner: azureuser:azureuser
  - path: /home/azureuser/myapp/index.js
    content: |
      var express = require('express')
      var app = express()
      var os = require('os');
      app.get('/', function (req, res) {
        res.send('Hello World from host ' + os.hostname() + '!')
      })
      app.listen(3000, function () {
        console.log('Hello world app listening on port 3000!')
      })
runcmd:
  - service nginx restart
  - cd "/home/azureuser/myapp"
  - npm init
  - npm install express -y
  - nodejs index.js
```
### <a name="create-availability-set-for-virtual-machines"></a>Создание группы доступности для виртуальных машин

Создайте группу доступности, используя команду [az vm availability-set create](https://docs.microsoft.com/cli/azure/vm/availability-set?view=azure-cli-latest#az-vm-availability-set-create) и следующие сведения:

* имя **myAvSet**;
* в группе ресурсов **myResourceGroupLB**;
* расположение **eastus**.

```azurecli-interactive
  az vm availability-set create \
    --name myAvSet \
    --resource-group myResourceGroupLB \
    --location eastus 
    
```

### <a name="create-virtual-machines"></a>Создание виртуальных машин

Создайте виртуальные машины с помощью команды [az vm create](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create), используя следующие сведения:

#### <a name="vm1"></a>VM1
* имя **myVM1**;
* в группе ресурсов **myResourceGroupLB**;
* подключена к сетевому интерфейсу **myNicVM1**;
* образ виртуальной машины **UbuntuLTS**;
* файл конфигурации **cloud-init.txt**, созданный на шаге выше;
* группа доступности **myAvSet**.

```azurecli-interactive
  az vm create \
    --resource-group myResourceGroupLB \
    --name myVM1 \
    --nics myNicVM1 \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --availability-set myAvSet \
    --no-wait
    
```
#### <a name="vm2"></a>VM2
* имя **myVM2**;
* в группе ресурсов **myResourceGroupLB**;
* подключена к сетевому интерфейсу **myNicVM2**;
* образ виртуальной машины **UbuntuLTS**;
* файл конфигурации **cloud-init.txt**, созданный на шаге выше;
* в **зоне 2**.

```azurecli-interactive
  az vm create \
    --resource-group myResourceGroupLB \
    --name myVM2 \
    --nics myNicVM2 \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --availability-set myAvSet  \
    --no-wait
```

#### <a name="vm3"></a>VM3
* имя **myVM3**;
* в группе ресурсов **myResourceGroupLB**;
* подключена к сетевому интерфейсу **myNicVM3**;
* образ виртуальной машины **UbuntuLTS**;
* файл конфигурации **cloud-init.txt**, созданный на шаге выше;
* в **зоне 3**.

```azurecli-interactive
   az vm create \
    --resource-group myResourceGroupLB \
    --name myVM3 \
    --nics myNicVM3 \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --availability-set myAvSet  \
    --no-wait
```
На развертывание виртуальных машин может потребоваться несколько минут.

---

## <a name="test-the-load-balancer"></a>Тестирование подсистемы балансировки нагрузки

Чтобы получить общедоступный IP-адрес подсистемы балансировки нагрузки, используйте команду [az network public-ip show](https://docs.microsoft.com/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-show). 

Скопируйте общедоступный IP-адрес и вставьте его в адресную строку браузера.

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroupLB \
    --name myPublicIP \
    --query [ipAddress] \
    --output tsv
```
:::image type="content" source="./media/load-balancer-standard-public-cli/running-nodejs-app.png" alt-text="Тестирование подсистемы балансировки нагрузки" border="true":::

## <a name="clean-up-resources"></a>Очистка ресурсов

Вы можете удалить ненужную группу ресурсов, подсистему балансировки нагрузки и все связанные с ней ресурсы, выполнив команду [az group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete).

```azurecli-interactive
  az group delete \
    --name myResourceGroupLB
```

## <a name="next-steps"></a>Дальнейшие действия
Из этого краткого руководства вы узнали, как:

* создать стандартную или общедоступную подсистему балансировки нагрузки;
* подключить к ней виртуальные машины; 
* настроить правило трафика подсистемы балансировки нагрузки и пробу работоспособности;
* тестировать подсистему балансировки нагрузки.

Дополнительные сведения об Azure Load Balancer см. в статье [Что такое Azure Load Balancer?](load-balancer-overview.md) и на странице [часто задаваемых вопросов о Load Balancer](load-balancer-faqs.md).

Дополнительные сведения см. в статье [Standard Load Balancer and Availability Zones](load-balancer-standard-availability-zones.md) (Azure Load Balancer ценовой категории "Стандартный" и зоны доступности).
