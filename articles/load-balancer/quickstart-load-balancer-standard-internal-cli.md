---
title: Краткое руководство. Создание внутренней подсистемы балансировки нагрузки — Azure CLI
titleSuffix: Azure Load Balancer
description: В этом кратком руководстве показано, как создать внутреннюю подсистему балансировки нагрузки с помощью Azure CLI
services: load-balancer
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to create a load balancer so that I can load balance internal traffic to VMs.
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/23/2020
ms.author: allensu
ms.custom: mvc, devx-track-js, devx-track-azurecli
ms.openlocfilehash: 834b5c3651a7fff085dc53096f66d5e3f4bf27b4
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/17/2020
ms.locfileid: "94700417"
---
# <a name="quickstart-create-an-internal-load-balancer-to-load-balance-vms-using-azure-cli"></a>Краткое руководство. Создание внутренней подсистемы балансировки нагрузки с помощью Azure CLI для распределения нагрузки между виртуальными машинами

Начните работу с Azure Load Balancer, создав с помощью Azure CLI общедоступную подсистему балансировки нагрузки и три виртуальные машины.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)] 

- Для работы с этим кратким руководством требуется Azure CLI версии 2.0.28 или более поздней. Если вы используете Azure Cloud Shell, последняя версия уже установлена.

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Группа ресурсов Azure является логическим контейнером, в котором происходит развертывание ресурсов Azure и управление ими.

Создайте группу ресурсов с помощью команды [az group create](/cli/azure/group?view=azure-cli-latest#az-group-create):

* с именем **CreateIntLBQS-rg**; 
* в расположении **eastus**.

```azurecli-interactive
  az group create \
    --name CreateIntLBQS-rg \
    --location eastus
```
---

# <a name="standard-sku"></a>[**SKU "Стандартный"**](#tab/option-1-create-load-balancer-standard)

>[!NOTE]
>Для производственных рабочих нагрузок рекомендуется использовать подсистему балансировки нагрузки ценовой категории "Стандартный". Дополнительные сведения о доступных ценовых категориях см. в статье **[Номера SKU для Azure Load Balancer](skus.md)** .

## <a name="configure-virtual-network"></a>Настройка виртуальной сети

Прежде чем развертывать виртуальные машины и подсистему балансировки нагрузки, создайте вспомогательные ресурсы виртуальной сети.

### <a name="create-a-virtual-network"></a>Создание виртуальной сети

Создайте виртуальную сеть с помощью команды [az network vnet create](/cli/azure/network/vnet?view=azure-cli-latest#az-network-vnet-createt):

* с именем **myVNet**;
* с префиксом подсети **10.1.0.0/16**;
* с именем подсети **myBackendSubnet**;
* с префиксом подсети **10.1.0.0/24**;
* в группе ресурсов **CreateIntLBQS-rg**;
* расположении **eastus**.

```azurecli-interactive
  az network vnet create \
    --resource-group CreateIntLBQS-rg \
    --location eastus \
    --name myVNet \
    --address-prefixes 10.1.0.0/16 \
    --subnet-name myBackendSubnet \
    --subnet-prefixes 10.1.0.0/24
```
### <a name="create-a-network-security-group"></a>Создание группы безопасности сети

Для подсистемы балансировки нагрузки уровня "Стандартный" у серверных виртуальных машин должны быть сетевые интерфейсы, связанные с группой безопасности сети. 

Создайте группу безопасности сети с помощью команды [az network nsg create](/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-create):

* с именем **myNSG**;
* в группе ресурсов **CreateIntLBQS-rg**.

```azurecli-interactive
  az network nsg create \
    --resource-group CreateIntLBQS-rg \
    --name myNSG
```

### <a name="create-a-network-security-group-rule"></a>Создание правила группы безопасности сети

Создайте правило группы безопасности сети с помощью команды [az network nsg rule create](/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create), используя такие сведения:

* имя **myNSGRuleHTTP**;
* группа безопасности сети, созданная на предыдущем шаге, **myNSG**;
* в группе ресурсов **CreateIntLBQS-rg**.
* с протоколом **(*)** ;
* направление **Входящий**;
* источник **(*)** ;
* назначение **(*)** ;
* порт назначения **порт 80**;
* доступ **Разрешить**;
* приоритет **200**.

```azurecli-interactive
  az network nsg rule create \
    --resource-group CreateIntLBQS-rg \
    --nsg-name myNSG \
    --name myNSGRuleHTTP \
    --protocol '*' \
    --direction inbound \
    --source-address-prefix '*' \
    --source-port-range '*' \
    --destination-address-prefix '*' \
    --destination-port-range 80 \
    --access allow \
    --priority 200
```

## <a name="create-backend-servers"></a>Создание внутренних серверов

В этом разделе показано, как создать:

* сетевые интерфейсы для внутренних серверов;
* файл конфигурации облака с именем **cloud-init.txt** для конфигурации сервера;
* две виртуальные машины, которые будут использоваться в качестве внутренних серверов для подсистемы балансировки нагрузки.

### <a name="create-network-interfaces-for-the-virtual-machines"></a>Создание сетевых интерфейсов для виртуальных машин

Создайте два сетевых интерфейса с помощью команды [az network nic create](/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create).

#### <a name="vm1"></a>VM1

* имя **myNicVM1**;
* в группе ресурсов **CreateIntLBQS-rg**;
* виртуальная сеть **myVNet**;
* подсеть **myBackendSubnet**;
* группа безопасности сети **myNSG**;

```azurecli-interactive
  az network nic create \
    --resource-group CreateIntLBQS-rg \
    --name myNicVM1 \
    --vnet-name myVNet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG
```
#### <a name="vm2"></a>VM2

* имя **myNicVM2**;
* в группе ресурсов **CreateIntLBQS-rg**;
* виртуальная сеть **myVNet**;
* подсеть **myBackendSubnet**;
* группа безопасности сети **myNSG**;

```azurecli-interactive
  az network nic create \
    --resource-group CreateIntLBQS-rg \
    --name myNicVM2 \
    --vnet-name myVnet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG
```

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

Создайте виртуальные машины с помощью команды [az vm create](/cli/azure/vm?view=azure-cli-latest#az-vm-create), используя следующие сведения:

#### <a name="vm1"></a>VM1
* имя **myVM1**;
* в группе ресурсов **CreateIntLBQS-rg**;
* подключена к сетевому интерфейсу **myNicVM1**;
* образ виртуальной машины **UbuntuLTS**;
* файл конфигурации **cloud-init.txt**, созданный на шаге выше;
* в **зоне 1**.

```azurecli-interactive
  az vm create \
    --resource-group CreateIntLBQS-rg \
    --name myVM1 \
    --nics myNicVM1 \
    --image UbuntuLTS \
    --admin-user azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --zone 1 \
    --no-wait
    
```
#### <a name="vm2"></a>VM2
* имя **myVM2**;
* в группе ресурсов **CreateIntLBQS-rg**;
* подключена к сетевому интерфейсу **myNicVM2**;
* образ виртуальной машины **UbuntuLTS**;
* файл конфигурации **cloud-init.txt**, созданный на шаге выше;
* в **зоне 2**.

```azurecli-interactive
  az vm create \
    --resource-group CreateIntLBQS-rg \
    --name myVM2 \
    --nics myNicVM2 \
    --image UbuntuLTS \
    --admin-user azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --zone 2 \
    --no-wait
```

На развертывание виртуальных машин может потребоваться несколько минут.

## <a name="create-standard-load-balancer"></a>Создание подсистемы балансировки нагрузки (цен. категория "Стандартный")

В этом разделе описано, как создать и настроить следующие компоненты подсистемы балансировки нагрузки:

  * интерфейсный пул IP-адресов, который получает входящий трафик в подсистеме балансировки нагрузки;
  * внутренний пул IP-адресов, на который интерфейсный пул отправляет трафик с балансировкой нагрузки;
  * проверка работоспособности, определяющая работоспособность внутренних экземпляров виртуальной машины;
  * правило подсистемы балансировки нагрузки, определяющее порядок распределения трафика между виртуальными машинами.

### <a name="create-the-load-balancer-resource"></a>Создание ресурса подсистемы балансировки нагрузки

С помощью команды [az network lb create](/cli/azure/network/lb?view=azure-cli-latest#az-network-lb-create) создайте общедоступную подсистему балансировки нагрузки:

* с именем **myLoadBalancer**,
* пулом переднего плана **myFrontEnd**
* и серверным пулом **myBackEndPool**,
* связанную с виртуальной сетью **myVNet**
* и внутренней подсетью **myBackendSubnet**.

```azurecli-interactive
  az network lb create \
    --resource-group CreateIntLBQS-rg \
    --name myLoadBalancer \
    --sku Standard \
    --vnet-name myVnet \
    --subnet myBackendSubnet \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool       
```

### <a name="create-the-health-probe"></a>Создание зонда работоспособности

При пробе работоспособности выполняется проверка всех экземпляров виртуальной машины, чтобы убедиться, что они могут отправлять сетевой трафик. 

Виртуальная машина с неудачной пробой удаляется из подсистемы балансировки нагрузки и снова добавляется в нее после устранения сбоя.

Создайте пробу работоспособности с помощью команды [az network lb probe create](/cli/azure/network/lb/probe?view=azure-cli-latest#az-network-lb-probe-create), которая:

* отслеживает работоспособность виртуальных машин;
* имеет имя **myHealthProbe**;
* использует протокол **TCP**;
* отслеживает **порт 80**.

```azurecli-interactive
  az network lb probe create \
    --resource-group CreateIntLBQS-rg \
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

С помощью команды [az network lb rule create](/cli/azure/network/lb/rule?view=azure-cli-latest#az-network-lb-rule-create) создайте правило подсистемы балансировки нагрузки, которое:

* имеет имя **myHTTPRule**;
* ожидает передачи данных от **порта 80**, используемого интерфейсным пулом **myFrontEnd**;
* перенаправляет трафик, для которого настроена балансировка нагрузки, ко внутреннему пулу адресов **myBackEndPool**, который использует **порт 80**; 
* использует пробу работоспособности **myHealthProbe**;
* использует протокол **TCP**;
* с временем ожидания перед переходом в режим простоя **15 минут**;
* и включенной функцией сброса подключений TCP.

```azurecli-interactive
  az network lb rule create \
    --resource-group CreateIntLBQS-rg \
    --lb-name myLoadBalancer \
    --name myHTTPRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe \
    --disable-outbound-snat true \
    --idle-timeout 15 \
    --enable-tcp-reset true
```
>[!NOTE]
>Виртуальные машины во внутреннем пуле не смогут создавать исходящие подключения к Интернету с такой конфигурацией. </br> Дополнительные сведения о включении исходящих подключений см. в следующих статьях: </br> **[Исходящие подключения в Azure](load-balancer-outbound-connections.md)**</br> Варианты обеспечения подключений: </br> **[Конфигурация подсистемы балансировки нагрузки только для исходящего трафика](egress-only.md)** </br> **[Что такое NAT виртуальной сети?](../virtual-network/nat-overview.md)**

### <a name="add-virtual-machines-to-load-balancer-backend-pool"></a>Добавление виртуальных машин во внутренний пул подсистемы балансировки нагрузки

Добавьте виртуальные машины во внутренний пул, используя команду [az network nic ip-config address-pool add](/cli/azure/network/nic/ip-config/address-pool?view=azure-cli-latest#az-network-nic-ip-config-address-pool-add):


#### <a name="vm1"></a>VM1
* в серверном пуле адресов **myBackEndPool**;
* в группе ресурсов **CreateIntLBQS-rg**;
* связь с сетевым интерфейсом **myNicVM1** и **ipconfig1**;
* связанный с подсистемой балансировки нагрузки **myLoadBalancer**.

```azurecli-interactive
  az network nic ip-config address-pool add \
   --address-pool myBackendPool \
   --ip-config-name ipconfig1 \
   --nic-name myNicVM1 \
   --resource-group CreateIntLBQS-rg \
   --lb-name myLoadBalancer
```

#### <a name="vm2"></a>VM2
* в серверном пуле адресов **myBackEndPool**;
* в группе ресурсов **CreateIntLBQS-rg**;
* связь с сетевым интерфейсом **myNicVM2** и **ipconfig1**;
* связанный с подсистемой балансировки нагрузки **myLoadBalancer**.

```azurecli-interactive
  az network nic ip-config address-pool add \
   --address-pool myBackendPool \
   --ip-config-name ipconfig1 \
   --nic-name myNicVM2 \
   --resource-group CreateIntLBQS-rg \
   --lb-name myLoadBalancer
```

# <a name="basic-sku"></a>[**SKU "Базовый"**](#tab/option-1-create-load-balancer-basic)

>[!NOTE]
>Для производственных рабочих нагрузок рекомендуется использовать подсистему балансировки нагрузки ценовой категории "Стандартный". Дополнительные сведения о доступных ценовых категориях см. в статье **[Номера SKU для Azure Load Balancer](skus.md)** .

## <a name="configure-virtual-network"></a>Настройка виртуальной сети

Прежде чем развертывать виртуальные машины и подсистему балансировки нагрузки, создайте вспомогательные ресурсы виртуальной сети.

### <a name="create-a-virtual-network"></a>Создание виртуальной сети

Создайте виртуальную сеть с помощью команды [az network vnet create](/cli/azure/network/vnet?view=azure-cli-latest#az-network-vnet-createt):

* с именем **myVNet**;
* с префиксом подсети **10.1.0.0/16**;
* с именем подсети **myBackendSubnet**;
* с префиксом подсети **10.1.0.0/24**;
* в группе ресурсов **CreateIntLBQS-rg**;
* расположении **eastus**.

```azurecli-interactive
  az network vnet create \
    --resource-group CreateIntLBQS-rg \
    --location eastus \
    --name myVNet \
    --address-prefixes 10.1.0.0/16 \
    --subnet-name myBackendSubnet \
    --subnet-prefixes 10.1.0.0/24
```
### <a name="create-a-network-security-group"></a>Создание группы безопасности сети

Для подсистемы балансировки нагрузки уровня "Стандартный" у серверных виртуальных машин должны быть сетевые интерфейсы, связанные с группой безопасности сети. 

Создайте группу безопасности сети с помощью команды [az network nsg create](/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-create):

* с именем **myNSG**;
* в группе ресурсов **CreateIntLBQS-rg**.

```azurecli-interactive
  az network nsg create \
    --resource-group CreateIntLBQS-rg \
    --name myNSG
```

### <a name="create-a-network-security-group-rule"></a>Создание правила группы безопасности сети

Создайте правило группы безопасности сети с помощью команды [az network nsg rule create](/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create), используя такие сведения:

* имя **myNSGRuleHTTP**;
* группа безопасности сети, созданная на предыдущем шаге, **myNSG**;
* в группе ресурсов **CreateIntLBQS-rg**;
* с протоколом **(*)** ;
* направление **Входящий**;
* источник **(*)** ;
* назначение **(*)** ;
* порт назначения **порт 80**;
* доступ **Разрешить**;
* приоритет **200**.

```azurecli-interactive
  az network nsg rule create \
    --resource-group CreateIntLBQS-rg \
    --nsg-name myNSG \
    --name myNSGRuleHTTP \
    --protocol '*' \
    --direction inbound \
    --source-address-prefix '*' \
    --source-port-range '*' \
    --destination-address-prefix '*' \
    --destination-port-range 80 \
    --access allow \
    --priority 200
```

### <a name="create-network-interfaces-for-the-virtual-machines"></a>Создание сетевых интерфейсов для виртуальных машин

Создайте два сетевых интерфейса с помощью команды [az network nic create](/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create).

#### <a name="vm1"></a>VM1

* имя **myNicVM1**;
* в группе ресурсов **CreateIntLBQS-rg**;
* виртуальная сеть **myVNet**;
* подсеть **myBackendSubnet**;
* группа безопасности сети **myNSG**;

```azurecli-interactive

  az network nic create \
    --resource-group CreateIntLBQS-rg \
    --name myNicVM1 \
    --vnet-name myVNet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG
```
#### <a name="vm2"></a>VM2

* имя **myNicVM2**;
* в группе ресурсов **CreateIntLBQS-rg**;
* виртуальная сеть **myVNet**;
* подсеть **myBackendSubnet**;

```azurecli-interactive
  az network nic create \
    --resource-group CreateIntLBQS-rg \
    --name myNicVM2 \
    --vnet-name myVnet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG
```

## <a name="create-backend-servers"></a>Создание внутренних серверов

В этом разделе показано, как создать:

* файл конфигурации облака с именем **cloud-init.txt** для конфигурации сервера; 
* группу доступности для виртуальных машин;
* две виртуальные машины, которые будут использоваться в качестве внутренних серверов для подсистемы балансировки нагрузки.

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

Создайте группу доступности, используя команду [az vm availability-set create](/cli/azure/vm/availability-set?view=azure-cli-latest#az-vm-availability-set-create) и следующие сведения:

* имя **myAvSet**;
* в группе ресурсов **CreateIntLBQS-rg**;
* расположение **eastus**.

```azurecli-interactive
  az vm availability-set create \
    --name myAvSet \
    --resource-group CreateIntLBQS-rg \
    --location eastus 
    
```

### <a name="create-virtual-machines"></a>Создание виртуальных машин

Создайте виртуальные машины с помощью команды [az vm create](/cli/azure/vm?view=azure-cli-latest#az-vm-create), используя следующие сведения:

#### <a name="vm1"></a>VM1
* имя **myVM1**;
* в группе ресурсов **CreateIntLBQS-rg**;
* подключена к сетевому интерфейсу **myNicVM1**;
* образ виртуальной машины **UbuntuLTS**;
* файл конфигурации **cloud-init.txt**, созданный на шаге выше;
* группа доступности **myAvSet**.

```azurecli-interactive
  az vm create \
    --resource-group CreateIntLBQS-rg \
    --name myVM1 \
    --nics myNicVM1 \
    --image UbuntuLTS \
    --admin-user azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --availability-set myAvSet \
    --no-wait
    
```
#### <a name="vm2"></a>VM2
* имя **myVM2**;
* в группе ресурсов **CreateIntLBQS-rg**;
* подключена к сетевому интерфейсу **myNicVM2**;
* образ виртуальной машины **UbuntuLTS**;
* файл конфигурации **cloud-init.txt**, созданный на шаге выше;
* в **зоне 2**.

```azurecli-interactive
  az vm create \
    --resource-group CreateIntLBQS-rg \
    --name myVM2 \
    --nics myNicVM2 \
    --image UbuntuLTS \
    --admin-user azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --availability-set myAvSet  \
    --no-wait
```

На развертывание виртуальных машин может потребоваться несколько минут.

## <a name="create-basic-load-balancer"></a>Создание подсистемы балансировки нагрузки уровня "Базовый"

В этом разделе описано, как создать и настроить следующие компоненты подсистемы балансировки нагрузки:

  * интерфейсный пул IP-адресов, который получает входящий трафик в подсистеме балансировки нагрузки;
  * внутренний пул IP-адресов, на который интерфейсный пул отправляет трафик с балансировкой нагрузки;
  * проверка работоспособности, определяющая работоспособность внутренних экземпляров виртуальной машины;
  * правило подсистемы балансировки нагрузки, определяющее порядок распределения трафика между виртуальными машинами.

### <a name="create-the-load-balancer-resource"></a>Создание ресурса подсистемы балансировки нагрузки

С помощью команды [az network lb create](/cli/azure/network/lb?view=azure-cli-latest#az-network-lb-create) создайте общедоступную подсистему балансировки нагрузки:

* с именем **myLoadBalancer**,
* пулом переднего плана **myFrontEnd**
* и серверным пулом **myBackEndPool**,
* связанную с виртуальной сетью **myVNet**
* и внутренней подсетью **myBackendSubnet**.

```azurecli-interactive
  az network lb create \
    --resource-group CreateIntLBQS-rg \
    --name myLoadBalancer \
    --sku Basic \
    --vnet-name myVNet \
    --subnet myBackendSubnet \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool       
```

### <a name="create-the-health-probe"></a>Создание зонда работоспособности

При пробе работоспособности выполняется проверка всех экземпляров виртуальной машины, чтобы убедиться, что они могут отправлять сетевой трафик. 

Виртуальная машина с неудачной пробой удаляется из подсистемы балансировки нагрузки и снова добавляется в нее после устранения сбоя.

Создайте пробу работоспособности с помощью команды [az network lb probe create](/cli/azure/network/lb/probe?view=azure-cli-latest#az-network-lb-probe-create), которая:

* отслеживает работоспособность виртуальных машин;
* имеет имя **myHealthProbe**;
* использует протокол **TCP**;
* отслеживает **порт 80**.

```azurecli-interactive
  az network lb probe create \
    --resource-group CreateIntLBQS-rg \
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

С помощью команды [az network lb rule create](/cli/azure/network/lb/rule?view=azure-cli-latest#az-network-lb-rule-create) создайте правило подсистемы балансировки нагрузки, которое:

* имеет имя **myHTTPRule**;
* ожидает передачи данных от **порта 80**, используемого интерфейсным пулом **myFrontEnd**;
* перенаправляет трафик, для которого настроена балансировка нагрузки, ко внутреннему пулу адресов **myBackEndPool**, который использует **порт 80**; 
* использует пробу работоспособности **myHealthProbe**;
* использует протокол **TCP**;
* с временем ожидания перед переходом в режим простоя **15 минут**;

```azurecli-interactive
  az network lb rule create \
    --resource-group CreateIntLBQS-rg \
    --lb-name myLoadBalancer \
    --name myHTTPRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe \
    --idle-timeout 15 
```
### <a name="add-virtual-machines-to-load-balancer-backend-pool"></a>Добавление виртуальных машин во внутренний пул подсистемы балансировки нагрузки

Добавьте виртуальные машины во внутренний пул, используя команду [az network nic ip-config address-pool add](/cli/azure/network/nic/ip-config/address-pool?view=azure-cli-latest#az-network-nic-ip-config-address-pool-add):


#### <a name="vm1"></a>VM1
* в серверном пуле адресов **myBackEndPool**;
* в группе ресурсов **CreateIntLBQS-rg**;
* связь с сетевым интерфейсом **myNicVM1** и **ipconfig1**;
* связанный с подсистемой балансировки нагрузки **myLoadBalancer**.

```azurecli-interactive
  az network nic ip-config address-pool add \
   --address-pool myBackendPool \
   --ip-config-name ipconfig1 \
   --nic-name myNicVM1 \
   --resource-group CreateIntLBQS-rg \
   --lb-name myLoadBalancer
```

#### <a name="vm2"></a>VM2
* в серверном пуле адресов **myBackEndPool**;
* в группе ресурсов **CreateIntLBQS-rg**;
* связь с сетевым интерфейсом **myNicVM2** и **ipconfig1**;
* связанный с подсистемой балансировки нагрузки **myLoadBalancer**.

```azurecli-interactive
  az network nic ip-config address-pool add \
   --address-pool myBackendPool \
   --ip-config-name ipconfig1 \
   --nic-name myNicVM2 \
   --resource-group CreateIntLBQS-rg \
   --lb-name myLoadBalancer
```

---

## <a name="test-the-load-balancer"></a>Тестирование подсистемы балансировки нагрузки

### <a name="create-azure-bastion-public-ip"></a>Создание общедоступного IP-адреса Бастиона Azure

Чтобы создать общедоступный IP-адрес узла-бастиона, воспользуйтесь командой [az network public-ip create](/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create):

* создайте стандартный избыточный между зонами общедоступный IP-адрес с именем **myPublicIP**;
* в группе ресурсов **CreateIntLBQS-rg**.

```azurecli-interactive
  az network public-ip create \
    --resource-group CreateIntLBQS-rg \
    --name myBastionIP \
    --sku Standard
```

### <a name="create-azure-bastion-subnet"></a>Создание подсети Бастиона Azure

Используйте команду [az network vnet subnet create](/cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-create), чтобы создать подсеть:

* с именем **AzureBastionSubnet**;
* с префиксом подсети **10.1.1.0/24**.
* в виртуальной сети **myVNet**;
* в группе ресурсов **CreateIntLBQS-rg**;

```azurecli-interactive
  az network vnet subnet create \
    --resource-group CreateIntLBQS-rg \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.1.1.0/24
```

### <a name="create-azure-bastion-host"></a>Создание узла-бастиона Azure
Используйте команду [az network bastion create](/cli/azure/network/bastion?view=azure-cli-latest#az-network-bastion-create) для создания узла-бастиона:

* с именем **myBastionHost**;
* в группе ресурсов **CreateIntLBQS-rg**;
* связанного с общедоступным IP-адресом **myBastionIP**;
* связанного с виртуальной сетью **myVNet**;
* в расположении **eastus**.

```azurecli-interactive
  az network bastion create \
    --resource-group CreateIntLBQS-rg \
    --name myBastionHost \
    --public-ip-address myBastionIP \
    --vnet-name myVNet \
    --location eastus
```
Развертывание узла бастиона займет несколько минут.

### <a name="create-test-virtual-machine"></a>Создание тестовой виртуальной машины

Создайте сетевой интерфейс с помощью команды [az network nic create](/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create):

* с именем **myNicTestVM**;
* в группе ресурсов **CreateIntLBQS-rg**;
* виртуальная сеть **myVNet**;
* подсеть **myBackendSubnet**;
* группа безопасности сети **myNSG**;

```azurecli-interactive
  az network nic create \
    --resource-group CreateIntLBQS-rg \
    --name myNicTestVM \
    --vnet-name myVNet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG
```
С помощью команды [az vm create](/cli/azure/vm?view=azure-cli-latest#az-vm-create) создайте виртуальную машину:

* с именем **myTestVM**;
* в группе ресурсов **CreateIntLBQS-rg**;
* с подключением к сетевому интерфейсу **myNicTestVM**;
* с образом виртуальной машины **Win2019Datacenter**;
* с выбранными значениями для **\<adminpass>** и **\<adminuser>** .
  

```azurecli-interactive
  az vm create \
    --resource-group CreateIntLBQS-rg \
    --name myTestVM \
    --nics myNicTestVM \
    --image Win2019Datacenter \
    --admin-username <adminuser> \
    --admin-password <adminpass> \
    --no-wait
```
Развертывание виртуальной машины может занять несколько минут.

### <a name="test"></a>Тест

1. [Войдите](https://portal.azure.com) на портал Azure.

1. Найдите частный IP-адрес для подсистемы балансировки нагрузки на экране **обзора**. В меню слева щелкните **Все службы**, выберите **Все ресурсы**, а затем — **myLoadBalancer**.

2. Запишите или скопируйте адрес рядом с **частным IP-адресом** на экране **обзора** **myLoadBalancer**.

3. В меню слева щелкните **Все службы**, выберите **Все ресурсы**, а затем в списке ресурсов выберите виртуальную машину **myTestVM**, расположенную в группе ресурсов **CreateIntLBQS-rg**.

4. На странице **Обзор** выберите **Подключиться** и **Бастион**.

6. Введите имя пользователя и пароль, введенные в процессе создания виртуальной машины.

7. На виртуальной машине **myTestVM** откройте браузер **Internet Explorer**.

8. Введите IP-адрес с предыдущего шага в адресную строку браузера. В браузере отобразится страница по умолчанию веб-сервера IIS.

    :::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/load-balancer-test.png" alt-text="Создание внутренней подсистемы балансировки нагрузки уровня Стандартный" border="true":::
   
Чтобы подсистема балансировки нагрузки распределяла трафик между всеми тремя виртуальными машинами, вы можете настроить стандартную страницу веб-сервера IIS каждой из них, а затем принудительно обновить браузер с клиентского компьютера.

## <a name="clean-up-resources"></a>Очистка ресурсов

Вы можете удалить ненужную группу ресурсов, подсистему балансировки нагрузки и все связанные с ней ресурсы, выполнив команду [az group delete](/cli/azure/group?view=azure-cli-latest#az-group-delete).

```azurecli-interactive
  az group delete \
    --name CreateIntLBQS-rg
```

## <a name="next-steps"></a>Дальнейшие действия
Из этого краткого руководства вы узнали, как:

* создать стандартную или общедоступную подсистему балансировки нагрузки;
* подключить к ней виртуальные машины; 
* настроить правило трафика подсистемы балансировки нагрузки и пробу работоспособности;
* тестировать подсистему балансировки нагрузки.

Чтобы узнать больше об Azure Load Balancer, ознакомьтесь со следующей статьей: 
> [!div class="nextstepaction"]
> [Что такое Azure Load Balancer](load-balancer-overview.md)