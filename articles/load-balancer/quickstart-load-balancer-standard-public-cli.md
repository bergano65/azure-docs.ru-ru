---
title: Краткое руководство. Создание общедоступного Load Balancer с помощью Azure CLI
titleSuffix: Azure Load Balancer
description: В рамках этого краткого руководства вы узнаете, как создать общедоступную подсистему балансировки нагрузки с помощью Azure CLI
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
tags: azure-resource-manager
Customer intent: I want to create a Load balancer so that I can load balance internet traffic to VMs.
ms.assetid: a8bcdd88-f94c-4537-8143-c710eaa86818
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/25/2019
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: 1a2d0322436bd91e92a7018552c5827e021ee74e
ms.sourcegitcommit: cec9676ec235ff798d2a5cad6ee45f98a421837b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85851516"
---
# <a name="quickstart-create-a-standard-load-balancer-to-load-balance-vms-using-azure-cli"></a>Краткое руководство. Создание Load Balancer (цен. категория "Стандартный") с помощью Azure CLI для распределения нагрузки между виртуальными машинами

В этом кратком руководстве объясняется процесс создания общедоступного Load Balancer. Чтобы протестировать подсистему балансировки нагрузки, мы развернем две виртуальные машины с установленным сервером Ubuntu для распределения между ними нагрузки веб-приложения.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

Если вы решили установить и использовать интерфейс командной строки локально, для работы с этим руководством вам понадобится Azure CLI 2.0.28 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Создайте группу ресурсов с помощью команды [az group create](https://docs.microsoft.com/cli/azure/group). Группа ресурсов Azure является логическим контейнером, в котором происходит развертывание ресурсов Azure и управление ими.

В следующем примере создается группа ресурсов с именем *myResourceGroupSLB* в расположении *eastus*.

```azurecli-interactive
  az group create \
    --name myResourceGroupSLB \
    --location eastus
```

## <a name="create-a-public-ip-address"></a>Создание общедоступного IP-адреса

Для доступа к веб-приложению через Интернет подсистеме балансировки нагрузки требуется общедоступный IP-адрес. Создайте избыточный в пределах стандартной зоны общедоступный IP-адрес с именем [myPublicIP](https://docs.microsoft.com/cli/azure/network/public-ip) в группе *myResourceGroupSLB* с помощью команды *az network public-ip create*.

```azurecli-interactive
  az network public-ip create --resource-group myResourceGroupSLB --name myPublicIP --sku standard
```

Создайте зональный общедоступный IP-адрес в зоне доступности 1, используя приведенные ниже сведения.

```azurecli-interactive
  az network public-ip create --resource-group myResourceGroupSLB --name myPublicIP --sku standard --zone 1
```

Для создания базового общедоступного IP-адреса используйте `-SKU Basic`. Общедоступные IP-адреса ценовой категории "Базовый" несовместимы с подсистемой балансировки нагрузки ценовой категории **Стандартный**. Для производственных рабочих нагрузок компания Майкрософт рекомендует использовать ценовую категорию **Стандартный**.

> [!IMPORTANT]
> Далее в этом кратком руководстве предполагается, что вы выбрали SKU ценовой категории **Стандартный**, как описано выше.

## <a name="create-azure-load-balancer"></a>Создание Azure Load Balancer

В этом разделе описано, как создать и настроить следующие компоненты подсистемы балансировки нагрузки:
  - интерфейсный пул IP-адресов, который получает входящий трафик в подсистеме балансировки нагрузки;
  - внутренний пул IP-адресов, на который внешний пул отправляет трафик с балансировкой нагрузки;
  - зонд работоспособности, определяющий работоспособность серверных экземпляров виртуальной машины;
  - правило подсистемы балансировки нагрузки, определяющее порядок распределения трафика между виртуальными машинами.

### <a name="create-the-load-balancer"></a>Создание подсистемы балансировки нагрузки

С помощью команды [az network lb create](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest) создайте общедоступную подсистему балансировки нагрузки Azure Load Balancer **myLoadBalancer** с интерфейсным пулом **myFrontEnd** и внутренним пулом **myBackEndPool**, связанным с общедоступным IP-адресом **myPublicIP**, созданным на предыдущем шаге. Для создания Load Balancer уровня "Базовый" используйте `--sku basic`. Для производственных рабочих нагрузок корпорация Майкрософт рекомендует использовать номера SKU ценовой категории "Стандартный".

```azurecli-interactive
  az network lb create \
    --resource-group myResourceGroupSLB \
    --name myLoadBalancer \
    --sku standard \
    --public-ip-address myPublicIP \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool       
```

> [!IMPORTANT]
> Далее в этом кратком руководстве предполагается, что вы выбрали SKU ценовой категории **Стандартный**, как описано выше.

### <a name="create-the-health-probe"></a>Создание зонда работоспособности

Проба работоспособности проверяет все экземпляры виртуальной машины, чтобы убедиться, что они могут отправлять сетевой трафик. Экземпляр виртуальной машины с неудачной пробой удаляется из балансировщика нагрузки, пока не перейдет в оперативный режим и проба не определит его работоспособность. Создайте зонд работоспособности с помощью команды [az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe?view=azure-cli-latest), чтобы отслеживать работоспособность виртуальных машин. 

```azurecli-interactive
  az network lb probe create \
    --resource-group myResourceGroupSLB \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80   
```

### <a name="create-the-load-balancer-rule"></a>Создание правила подсистемы балансировки нагрузки

Правило подсистемы балансировки нагрузки определяет интерфейсную конфигурацию IP-адресов для входящего трафика и внутренний пул IP-адресов для приема трафика, а также порты источника и назначения. С помощью команды [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest) создайте правило подсистемы балансировки нагрузки с именем *myLoadBalancerRuleWeb* для прослушивания порта 80, используемого внешним пулом *myFrontEnd*, и отправки трафика с балансировкой нагрузки внутреннему пулу адресов *myBackEndPool*, который также использует порт 80. 

```azurecli-interactive
  az network lb rule create \
    --resource-group myResourceGroupSLB \
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

С помощью команды [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet) создайте виртуальную сеть с именем *myVnet*, содержащую подсеть *mySubnet*, в группе ресурсов *myResourceGroup*.

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroupSLB \
    --location eastus \
    --name myVnet \
    --subnet-name mySubnet
```

### <a name="create-a-network-security-group"></a>Создание группы безопасности сети

Для подсистемы балансировки нагрузки уровня "Стандартный" у серверных виртуальных машин должны быть сетевые интерфейсы, связанные с группой безопасности сети. Создайте группу безопасности сети для определения входящих подключений к виртуальной сети.

```azurecli-interactive
  az network nsg create \
    --resource-group myResourceGroupSLB \
    --name myNetworkSecurityGroup
```

### <a name="create-a-network-security-group-rule"></a>Создание правила группы безопасности сети

Создайте правило группы безопасности сети, чтобы разрешить входящие подключения через порт 80.

```azurecli-interactive
  az network nsg rule create \
    --resource-group myResourceGroupSLB \
    --nsg-name myNetworkSecurityGroup \
    --name myNetworkSecurityGroupRuleHTTP \
    --protocol tcp \
    --direction inbound \
    --source-address-prefix '*' \
    --source-port-range '*' \
    --destination-address-prefix '*' \
    --destination-port-range 80 \
    --access allow \
    --priority 200
```

### <a name="create-nics"></a>Создание сетевых адаптеров

Создайте три сетевых интерфейса с помощью команды [az network nic create](/cli/azure/network/nic#az-network-nic-create) и привяжите их к общедоступному IP-адресу и группе безопасности сети. 

```azurecli-interactive

  az network nic create \
    --resource-group myResourceGroupSLB \
    --name myNicVM1 \
    --vnet-name myVnet \
    --subnet mySubnet \
    --network-security-group myNetworkSecurityGroup \
    --lb-name myLoadBalancer \
    --lb-address-pools myBackEndPool

  az network nic create \
    --resource-group myResourceGroupSLB \
    --name myNicVM2 \
    --vnet-name myVnet \
    --subnet mySubnet \
    --network-security-group myNetworkSecurityGroup \
    --lb-name myLoadBalancer \
    --lb-address-pools myBackEndPool
  
  az network nic create \
    --resource-group myResourceGroupSLB \
    --name myNicVM3 \
    --vnet-name myVnet \
    --subnet mySubnet \
    --network-security-group myNetworkSecurityGroup \
    --lb-name myLoadBalancer \
    --lb-address-pools myBackEndPool

```

## <a name="create-backend-servers"></a>Создание внутренних серверов

В этом примере описано, как создать три виртуальные машины, которые будут использоваться в качестве внутренних серверов для подсистемы балансировки нагрузки. Чтобы проверить, успешно ли создана подсистема балансировки нагрузки, установите NGINX на виртуальных машинах.

Если вы создаете базовый Load Balancer с базовым общедоступным IP-адресом, вам потребуется создать группу доступности с помощью команды [az vm availabilityset create](/cli/azure/network/nic), чтобы добавить в нее виртуальные машины. Для стандартных Load Balancer этот дополнительный шаг не требуется. Корпорация Майкрософт рекомендует использовать ценовую категорию "Стандартный".

### <a name="create-three-virtual-machines"></a>Создание трех виртуальных машин

Тот же самый файл конфигурации cloud-init можно использовать и для установки NGINX, а также для запуска простого приложения Node.js "Hello World" на виртуальной машине Linux. В текущей оболочке создайте файл cloud-init.txt и вставьте в него следующую конфигурацию. Убедитесь, что весь файл cloud-init скопирован правильно, особенно первая строка.

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

Создайте виртуальные машины с помощью команды [az vm create](/cli/azure/vm#az-vm-create).

```azurecli-interactive

  az vm create \
    --resource-group myResourceGroupSLB \
    --name myVM1 \
    --availability-set myAvailabilitySet \
    --nics myNicVM1 \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --no-wait
   
  az vm create \
    --resource-group myResourceGroupSLB \
    --name myVM2 \
    --availability-set myAvailabilitySet \
    --nics myNicVM2 \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --no-wait

   az vm create \
    --resource-group myResourceGroupSLB \
    --name myVM3 \
    --availability-set myAvailabilitySet \
    --nics myNicVM3 \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --no-wait

```

Развертывание виртуальных машин может занять несколько минут.

## <a name="test-the-load-balancer"></a>Тестирование подсистемы балансировки нагрузки

Чтобы получить общедоступный IP-адрес подсистемы балансировки нагрузки, используйте команду [az network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show). Скопируйте общедоступный IP-адрес и вставьте его в адресную строку браузера.

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroupSLB \
    --name myPublicIP \
    --query [ipAddress] \
    --output tsv
```

   ![Проверка балансировщика нагрузки](./media/load-balancer-standard-public-cli/running-nodejs-app.png)

## <a name="clean-up-resources"></a>Очистка ресурсов

Вы можете удалить ставшие ненужными группу ресурсов, подсистему балансировки нагрузки и все связанные с ними ресурсы, выполнив команду [az group delete](/cli/azure/group#az-group-delete).

```azurecli-interactive
  az group delete --name myResourceGroupSLB
```

## <a name="next-steps"></a>Дальнейшие действия
Из этого краткого руководства вы узнали, как создать Load Balancer (цен. категория "Стандартный"), подключить к ней виртуальные машины, настроить правило трафика Load Balancer, пробу работоспособности, а также протестировать Load Balancer. Дополнительные сведения о Azure Load Balancer см. в руководстве [Распределение нагрузки виртуальных машин в пределах зон доступности с помощью Load Balancer уровня "Стандартный" и портала Azure](tutorial-load-balancer-standard-public-zone-redundant-portal.md).

Дополнительные сведения см. в статье [Standard Load Balancer and Availability Zones](load-balancer-standard-availability-zones.md) (Azure Load Balancer ценовой категории "Стандартный" и зоны доступности).
