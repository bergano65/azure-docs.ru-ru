---
title: Руководство по созданию и проверке шлюза NAT — Azure CLI
titlesuffix: Azure Virtual Network NAT
description: В этом учебнике объясняется, как создать шлюз NAT с помощью Azure CLI и проверить NAT.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to test a NAT gateway for outbound connectivity for my virtual network.
ms.service: virtual-network
ms.subservice: nat
ms.devlang: na
ms.topic: tutorial
ms.workload: infrastructure-services
ms.date: 06/11/2020
ms.author: allensu
ms.openlocfilehash: 717a9e9d3cc1dec350d0b4ace54687590f741768
ms.sourcegitcommit: c4ad4ba9c9aaed81dfab9ca2cc744930abd91298
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/12/2020
ms.locfileid: "84737297"
---
# <a name="tutorial-create-a-nat-gateway-using-azure-cli-and-test-the-nat-service"></a>Руководство по созданию шлюза NAT с помощью Azure CLI и проверке NAT

Из этого учебника вы узнаете, как создать шлюз NAT для обеспечения исходящего подключения в виртуальных машинах в Azure. Чтобы протестировать шлюз NAT, необходимо развернуть виртуальные машины источника и назначения. Чтобы проверить шлюз NAT, установите исходящие подключения по общедоступному IP-адресу. Из исходной виртуальной машины будут выполняться подключения к виртуальной машине назначения. В этом учебнике развертываются исходная виртуальная машина и виртуальная машина назначения в двух разных виртуальных сетях, но для простоты — в одной и той же группе ресурсов.


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Инструкции из этого учебника можно выполнить, используя Azure Cloud Shell или запустив соответствующие команды локально.  Если вы никогда не использовали Azure Cloud Shell, необходимо [выполнить вход](https://shell.azure.com) сейчас.

Если вы решили выполнить эти команды локально, необходимо установить CLI.  Для выполнения задач из этого учебника требуется Azure CLI 2.0.71 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0]( /cli/azure/install-azure-cli).


## <a name="create-a-resource-group"></a>Создание группы ресурсов

Создайте группу ресурсов с помощью команды [az group create](https://docs.microsoft.com/cli/azure/group). Группа ресурсов Azure является логическим контейнером, в котором происходит развертывание ресурсов Azure и управление ими.

В следующем примере создается группа ресурсов с именем **myResourceGroupNAT** в расположении **eastus2**:

```azurecli-interactive
  az group create \
    --name myResourceGroupNAT \
    --location eastus2
    
```

## <a name="create-the-nat-gateway"></a>Создание шлюза NAT

### <a name="create-a-public-ip-address"></a>Создание общедоступного IP-адреса

Для доступа к Интернету требуется один или несколько общедоступных IP-адресов для шлюза NAT. Создайте общедоступный IP-адрес с именем **myPublicIPsource** в группе **myResourceGroupNAT** с помощью команды [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip).

```azurecli-interactive
  az network public-ip create \
  --resource-group myResourceGroupNAT \
  --name myPublicIPsource \
  --sku standard
  
```

### <a name="create-a-public-ip-prefix"></a>Создание префикса общедоступного IP-адреса

Со шлюзом NAT можно использовать один или несколько ресурсов общедоступных IP-адресов, префиксов общедоступных IP-адресов или оба варианта. Для демонстрации в этот сценарий будет добавлен ресурс префикса общедоступного IP-адреса.   Выполните команду [az network public-ip prefix create](/cli/azure/network/public-ip/prefix?view=azure-cli-latest#az-network-public-ip-prefix-create) для создания ресурса префикса общедоступного IP-адреса с именем **myPublicIPprefixsource** в группе **myResourceGroupNAT**.

```azurecli-interactive
  az network public-ip prefix create \
  --resource-group myResourceGroupNAT \
  --name myPublicIPprefixsource \
  --length 31
  
```

### <a name="create-a-nat-gateway-resource"></a>Создание ресурса шлюза NAT

В этом разделе подробно описано, как создать и настроить следующие компоненты NAT с использованием ресурса Шлюза NAT:
  - пул общедоступных IP-адресов и префикс общедоступного IP-адреса, которые будут использоваться для исходящих потоков, преобразованных ресурсом шлюза NAT;
  - изменение значения времени ожидания простоя с 4 минут по умолчанию на 10 минут.

Создайте глобальный шлюз Azure NAT с помощью команды [az network nat gateway create](https://docs.microsoft.com/cli/azure/network/nat?view=azure-cli-latest) с именем **myNATgateway**. В команде используется общедоступный IP-адрес **myPublicIP** и префикс общедоступного IP-адреса **myPublicIPprefix**. В команде также задается значение 10 минут для времени ожидания простоя.

```azurecli-interactive
  az network nat gateway create \
    --resource-group myResourceGroupNAT \
    --name myNATgateway \
    --public-ip-addresses myPublicIPsource \
    --public-ip-prefixes myPublicIPprefixsource \
    --idle-timeout 10       
    
  ```

На этом этапе шлюз NAT функционирует, и все, что осталось сделать — это настроить, в каких подсетях виртуальной сети он должен использоваться.

## <a name="prepare-the-source-for-outbound-traffic"></a>Подготовка источника для исходящего трафика

Мы расскажем вам, как полностью настроить тестовую среду. Вы настроите тест, который будет использовать средства с открытым кодом, чтобы проверить шлюз NAT. Мы начнем с источника, который будет использовать шлюз NAT, созданный ранее.

### <a name="configure-virtual-network-for-source"></a>Настройка виртуальной сети для источника

Прежде чем развертывать виртуальную машину и проверять шлюз NAT, необходимо создать виртуальную сеть.

Создайте виртуальную сеть с именем **myVnetsource** с подсетью **mySubnetsource** в группе **myResourceGroupNAT**, используя команду [az network Microsoft Azure Virtual Network create](https://docs.microsoft.com/cli/azure/network/vnet).  Диапазон IP-адресов для виртуальной сети — **192.168.0.0/16**. Подсеть в виртуальной сети — **192.168.0.0/24**.

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroupNAT \
    --name myVnetsource \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnetsource \
    --subnet-prefix 192.168.0.0/24
    
```

### <a name="configure-nat-service-for-source-subnet"></a>Настройка NAT для исходной подсети

Настройте исходную подсеть **mySubnetsource** в виртуальной сети **myVnetsource**, чтобы она использовала конкретный ресурс шлюза NAT **myNATgateway**, с помощью команды [az network Microsoft Azure Virtual Network subnet update](https://docs.microsoft.com/cli/azure/network/vnet/subnet). Эта команда активирует NAT в указанной подсети.

```azurecli-interactive
    az network vnet subnet update \
    --resource-group myResourceGroupNAT \
    --vnet-name myVnetsource \
    --name mySubnetsource \
    --nat-gateway myNATgateway
    
```

NAT теперь используется для всего исходящего трафика для назначений в Интернете.  Нет необходимости настраивать UDR.

Чтобы можно было протестировать шлюз NAT, необходимо создать исходную виртуальную машину.  Ресурс общедоступного IP-адреса будет назначен как общедоступный IP-адрес уровня экземпляра для доступа к этой виртуальной машине извне. Этот адрес используется, только чтобы получить доступ для теста.  Мы покажем, что настройки NAT будут приоритетнее, чем другие параметры исходящего трафика.

В качестве упражнения можно создать эту виртуальную машину без общедоступного IP-адреса, а также другую виртуальную машину, чтобы использовать ее как инсталяционный сервер без общедоступного IP-адреса.

### <a name="create-public-ip-for-source-vm"></a>Создание общедоступного IP-адреса для исходной виртуальной машины

Мы создадим общедоступный IP-адрес для доступа к исходной виртуальной машине. Для создания ресурса общедоступного IP-адреса с именем **myPublicIPsourceVM** в группе **myResourceGroupNAT** используйте команду [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip).

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupNAT \
    --name myPublicIPsourceVM \
    --sku standard
    
```

### <a name="create-an-nsg-for-source-vm"></a>Создание группы безопасности сети (NSG) для исходной виртуальной машины

Так как стандартные общедоступные IP-адреса безопасны по умолчанию, необходимо создать группу безопасности сети (NSG), чтобы разрешить входящий доступ по протоколу SSH.  В NAT учитывается направление потоков. Эта NSG не будет использоваться для исходящего трафика, если шлюз NAT настроен в той же подсети. Чтобы создать ресурс NSG с именем **myNSGsource** в группе **myResourceGroupNAT**, используйте команду [az network nsg create](https://docs.microsoft.com/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-create).

```azurecli-interactive
  az network nsg create \
    --resource-group myResourceGroupNAT \
    --name myNSGsource 
    
```

### <a name="expose-ssh-endpoint-on-source-vm"></a>Предоставление конечной точки SSH на исходной виртуальной машине

Мы создадим правило в NSG для SSH-подключения к исходной виртуальной машине. Чтобы создать правило NSG с именем **ssh**, используйте команду [az network nsg rule create](https://docs.microsoft.com/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create). Это правило будет создано в NSG с именем **myNSGsource** в группе ресурсов **myResourceGroupNAT**.

```azurecli-interactive
  az network nsg rule create \
    --resource-group myResourceGroupNAT \
    --nsg-name myNSGsource \
    --priority 100 \
    --name ssh \
    --description "SSH access" \
    --access allow \
    --protocol tcp \
    --direction inbound \
    --destination-port-ranges 22
    
```

### <a name="create-nic-for-source-vm"></a>Создание сетевой карты для исходной виртуальной машины

Создайте сетевой интерфейс с помощью команды [az network nic create](/cli/azure/network/nic#az-network-nic-create) и свяжите его с общедоступным IP-адресом и NSG. 

```azurecli-interactive
  az network nic create \
    --resource-group myResourceGroupNAT \
    --name myNicsource \
    --vnet-name myVnetsource \
    --subnet mySubnetsource \
    --public-ip-address myPublicIPSourceVM \
    --network-security-group myNSGsource
    
```

### <a name="create-a-source-vm"></a>Создание исходной виртуальной машины

Создайте виртуальную машину с помощью команды [az vm create](/cli/azure/vm#az-vm-create).  Мы создадим ключи SSH для этой виртуальной машины и сохраним закрытый ключ для последующего использования.

```azurecli-interactive
  az vm create \
    --resource-group myResourceGroupNAT \
    --name myVMsource \
    --nics myNicsource \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --no-wait
    
```

Хотя команда и вернется немедленно, развертывание виртуальной машины может занять несколько минут.

## <a name="prepare-destination-for-outbound-traffic"></a>Подготовка назначения для исходящего трафика

Теперь мы создадим виртуальную машину назначения для исходящего трафика, преобразовываемого с помощью NAT, чтобы протестировать эту машину.

### <a name="configure-virtual-network-for-destination"></a>Настройка виртуальной сети для назначения

 Необходимо создать виртуальную сеть, в которой будет виртуальная машина назначения.  Здесь выполняются те же шаги с командами, что и для исходной виртуальной машины, с небольшими изменениями для предоставления конечной точки назначения.

Создайте виртуальную сеть с именем **myVnetdestination** с подсетью **mySubnetdestination** в группе **myResourceGroupNAT**, используя команду [az network Microsoft Azure Virtual Network create](https://docs.microsoft.com/cli/azure/network/vnet).  Диапазон IP-адресов для виртуальной сети — **192.168.0.0/16**. Подсеть в виртуальной сети — **192.168.0.0/24**.

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroupNAT \
    --name myVnetdestination \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnetdestination \
    --subnet-prefix 192.168.0.0/24
    
```

### <a name="create-public-ip-for-destination-vm"></a>Создание общедоступного IP-адреса для виртуальной машины назначения

Мы создадим общедоступный IP-адрес для доступа к исходной виртуальной машине. Для создания ресурса общедоступного IP-адреса с именем **myPublicIPdestinationVM** в группе **myResourceGroupNAT** используйте команду [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip). 

```azurecli-interactive
  az network public-ip create \
  --resource-group myResourceGroupNAT \
  --name myPublicIPdestinationVM \
  --sku standard
  
```

### <a name="create-an-nsg-for-destination-vm"></a>Создание NSG для виртуальной машины назначения

Так как стандартные общедоступные IP-адреса безопасны по умолчанию, необходимо создать NSG, чтобы разрешить входящий доступ по протоколу SSH. В NAT учитывается направление потоков. Эта NSG не будет использоваться для исходящего трафика, если шлюз NAT настроен в той же подсети. Чтобы создать ресурс NSG с именем **myNSGdestination** в группе **myResourceGroupNAT**, используйте команду [az network nsg create](https://docs.microsoft.com/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-create).

```azurecli-interactive
    az network nsg create \
    --resource-group myResourceGroupNAT \
    --name myNSGdestination
    
```

### <a name="expose-ssh-endpoint-on-destination-vm"></a>Предоставление конечной точки SSH для виртуальной машины назначения

Мы создадим правило в NSG для SSH-подключения к виртуальной машине назначения. Чтобы создать правило NSG с именем **ssh**, используйте команду [az network nsg rule create](https://docs.microsoft.com/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create). Это правило будет создано в NSG с именем **myNSGdestination** в группе ресурсов **myResourceGroupNAT**.

```azurecli-interactive
    az network nsg rule create \
    --resource-group myResourceGroupNAT \
    --nsg-name myNSGdestination \
    --priority 100 \
    --name ssh \
    --description "SSH access" \
    --access allow \
    --protocol tcp \
    --direction inbound \
    --destination-port-ranges 22
    
```

### <a name="expose-http-endpoint-on-destination-vm"></a>Предоставление конечной точки HTTP на виртуальной машине назначения

Мы создадим правило в NSG для HTTP-доступа к виртуальной машине назначения. Чтобы создать правило NSG с именем **http** в NSG **myNSGdestination** в группе **myResourceGroupNAT**, используйте команду [az network nsg rule create](https://docs.microsoft.com/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create).

```azurecli-interactive
    az network nsg rule create \
    --resource-group myResourceGroupNAT \
    --nsg-name myNSGdestination \
    --priority 101 \
    --name http \
    --description "HTTP access" \
    --access allow \
    --protocol tcp \
    --direction inbound \
    --destination-port-ranges 80
    
```

### <a name="create-nic-for-destination-vm"></a>Создание сетевой карты для виртуальной машины назначения

Создайте сетевой интерфейс с помощью команды [az network nic create](/cli/azure/network/nic#az-network-nic-create) и свяжите его с общедоступным IP-адресом **myPublicIPdestinationVM** и NSG **myNSGdestination**. 

```azurecli-interactive
    az network nic create \
    --resource-group myResourceGroupNAT \
    --name myNicdestination \
    --vnet-name myVnetdestination \
    --subnet mySubnetdestination \
    --public-ip-address myPublicIPdestinationVM \
    --network-security-group myNSGdestination
    
```

### <a name="create-a-destination-vm"></a>Создание виртуальной машины назначения

Создайте виртуальную машину с помощью команды [az vm create](/cli/azure/vm#az-vm-create).  Мы создадим ключи SSH для этой виртуальной машины и сохраним закрытый ключ для последующего использования.

 ```azurecli-interactive
    az vm create \
    --resource-group myResourceGroupNAT \
    --name myVMdestination \
    --nics myNicdestination \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --no-wait
    
```
Хотя команда и вернется немедленно, развертывание виртуальной машины может занять несколько минут.

## <a name="prepare-a-web-server-and-test-payload-on-destination-vm"></a>Подготовка веб-сервера и тестовых полезных данных для виртуальной машины назначения

Сначала необходимо найти IP-адрес виртуальной машины назначения.  Чтобы получить общедоступный IP-адрес виртуальной машины назначения, используйте команду [az network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show). 

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroupNAT \
    --name myPublicIPdestinationVM \
    --query [ipAddress] \
    --output tsv
    
``` 

>[!IMPORTANT]
>Скопируйте общедоступный IP-адрес и вставьте его в блокнот, чтобы его можно было использовать в дальнейшем. Укажите, что это виртуальная машина назначения.

### <a name="sign-in-to-destination-vm"></a>Вход в виртуальную машину назначения

Учетные данные SSH должны сохраниться в Cloud Shell после предыдущей операции.  Откройте [Azure Cloud Shell](https://shell.azure.com) в браузере. Используйте IP-адрес, полученный на предыдущем шаге, для подключения к виртуальной машине по протоколу SSH. 

```bash
ssh <ip-address-destination>
```

После входа скопируйте и вставьте следующие команды.  

```bash
sudo apt -y update && \
sudo apt -y upgrade && \
sudo apt -y install nginx && \
sudo ln -sf /dev/null /var/log/nginx/access.log && \
sudo touch /var/www/html/index.html && \
sudo rm /var/www/html/index.nginx-debian.html && \
sudo dd if=/dev/zero of=/var/www/html/100k bs=1024 count=100

```

Эти команды обновят виртуальную машину, установят nginx и создадут файл размером 100 КБ. Этот файл будет извлечен из виртуальной машины источника с помощью NAT.

Закройте сеанс SSH с виртуальной машиной назначения.

## <a name="prepare-test-on-source-vm"></a>Подготовка теста для виртуальной машины источника

Сначала необходимо найти IP-адрес виртуальной машины источника.  Чтобы получить общедоступный IP-адрес виртуальной машины источника, используйте команду [az network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show). 

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroupNAT \
    --name myPublicIPsourceVM \
    --query [ipAddress] \
    --output tsv
    
``` 

>[!IMPORTANT]
>Скопируйте общедоступный IP-адрес и вставьте его в блокнот, чтобы его можно было использовать в дальнейшем. Укажите, что это виртуальная машина-источник.

### <a name="sign-in-to-source-vm"></a>Вход в виртуальную машину источника

Учетные данные SSH по прежнему будут храниться в Cloud Shell. Откройте новую вкладку для [Azure Cloud Shell](https://shell.azure.com) в браузере.  Используйте IP-адрес, полученный на предыдущем шаге, для подключения к виртуальной машине по протоколу SSH. 

```bash
ssh <ip-address-source>
```

Скопируйте и вставьте следующие команды, чтобы подготовиться к тестированию NAT.

```bash
sudo apt -y update && \
sudo apt -y upgrade && \
sudo apt install -y nload golang && \
echo 'export GOPATH=${HOME}/go' >> .bashrc && \
echo 'export PATH=${PATH}:${GOPATH}/bin' >> .bashrc && \
. ~/.bashrc &&
go get -u github.com/rakyll/hey

```

Эти команды обновят виртуальную машину и среду оболочки, установят компоненты Go и [hey](https://github.com/rakyll/hey) из GitHub.

Теперь вы готовы к тестированию NAT.

## <a name="validate-nat-service"></a>Проверка NAT

Войдите в исходную виртуальную машину и используйте **curl** и **hey** для создания запросов по IP-адресу назначения.

Используйте curl для извлечения файла размером 100 КБ.  В примере ниже замените **\<ip-address-destination>** IP-адресом назначения, скопированным ранее.  Параметр **--output** указывает, что полученный файл будет удален.

```bash
curl http://<ip-address-destination>/100k --output /dev/null
```

Можно также создать серию запросов с помощью **hey**. Снова замените **\<ip-address-destination>** IP-адресом назначения, скопированным ранее.

```bash
hey -n 100 -c 10 -t 30 --disable-keepalive http://<ip-address-destination>/100k
```

Эта команда будет создавать по 100 запросов, 10 из них — параллельно, со временем ожидания 30 секунд. TCP-подключение не будет использоваться повторно.  Каждый запрос будет получать файл размером 100 КБ.  В конце выполнения **hey** сообщит некоторые статистические данные о качестве работы NAT.

## <a name="clean-up-resources"></a>Очистка ресурсов

Вы можете удалить ненужную группу ресурсов и все содержащиеся в ней ресурсы, выполнив команду [az group delete](/cli/azure/group#az-group-delete).

```azurecli-interactive 
  az group delete --name myResourceGroupNAT
  
```

## <a name="next-steps"></a>Дальнейшие действия
В этом учебнике вы создали шлюз NAT, виртуальные машины источника и назначения, а затем проверили шлюз NAT.

Просмотрите метрики в Azure Monitor, чтобы проверить работу NAT. Выполните диагностику проблем, таких как нехватка ресурсов доступных портов SNAT.  Эта проблема легко устраняется путем добавления ресурсов общедоступного IP-адреса, ресурсов префикса общедоступного IP-адреса, или их сочетания.

- Дополнительные сведения о [NAT виртуальной сети](./nat-overview.md).
- Дополнительные сведения о [ресурсе шлюза NAT](./nat-gateway-resource.md).
- Краткое руководство по развертыванию [ресурса Шлюза NAT с помощью Azure CLI](./quickstart-create-nat-gateway-cli.md).
- Краткое руководство по развертыванию [ресурса Шлюза NAT с помощью Azure PowerShell](./quickstart-create-nat-gateway-powershell.md).
- Краткое руководство по развертыванию [ресурса Шлюза NAT с помощью портала Azure](./quickstart-create-nat-gateway-portal.md).

> [!div class="nextstepaction"]

