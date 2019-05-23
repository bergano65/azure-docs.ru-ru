---
title: Краткое руководство. Направление веб-трафика с помощью шлюза приложений Azure и Azure CLI | Документация Майкрософт
description: Узнайте, как с помощью Azure CLI создать шлюз приложений Azure, который направляет веб-трафик к виртуальным машинам в серверном пуле.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: quickstart
ms.date: 1/8/2019
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: a4f6cc2af7b9e044e5a72767898f876932fbf973
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "66133945"
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway---azure-cli"></a>Краткое руководство. Направление веб-трафика с помощью Шлюза приложений Azure и Azure CLI.

В этом кратком руководстве показано, как создать шлюз приложений с помощью Azure CLI.  Создав шлюз приложений, протестируйте его, чтобы убедиться в том, что он работает правильно. Шлюз приложений Azure позволяет направлять веб-трафик приложения к определенным ресурсам. Для этого портам назначаются прослушиватели, создаются определенные правила и в серверный пул добавляются соответствующие ресурсы. Чтобы упростить восприятие, в этой статье используется простая настройка с открытым интерфейсным IP-адресом, базовый прослушиватель для размещения одного сайта на этом шлюзе приложений, две виртуальные машины, используемые для серверного пула, и базовое правило маршрутизации запросов.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Предварительные требования

### <a name="azure-cli"></a>Инфраструктура CLI Azure

Если вы решили установить и использовать CLI локально, вам потребуется Azure CLI 2.0.4 или более поздней версии. Чтобы узнать версию, выполните команду **az --version**. Дополнительные сведения об установке или обновлении см. [здесь]( /cli/azure/install-azure-cli).

### <a name="resource-group"></a>Группа ресурсов

В Azure выделите связанные ресурсы группе ресурсов. Создайте группу ресурсов, используя команду [az group create](/cli/azure/group#az-group-create). 

В следующем примере создается группа ресурсов с именем *myResourceGroupAG* в расположении *eastus*.

```azurecli-interactive 
az group create --name myResourceGroupAG --location eastus
```

### <a name="required-network-resources"></a>Необходимые сетевые ресурсы 

В Azure для обмена между создаваемыми ресурсами необходима виртуальная сеть.  Подсеть шлюза приложений может содержать только шлюзы приложений. Другие ресурсы запрещены.  Вы можете создать новую подсеть для шлюза приложений или использовать уже существующую. В этом примере вы создаете две подсети: одну — для шлюза приложений, а вторую — для внутренних серверов. Вы можете настроить интерфейсный IP-адрес как общедоступный или частный, в зависимости от варианта использования шлюза приложений. В этом примере мы будем использовать общедоступный интерфейсный IP-адрес.

Чтобы создать виртуальную сеть и подсеть, выполните команду [az network vnet create](/cli/azure/network/vnet#az-network-vnet-create). Чтобы создать общедоступный IP-адрес, выполните команду [az network public-ip create](/cli/azure/network/public-ip).

```azurecli-interactive
az network vnet create \
  --name myVNet \
  --resource-group myResourceGroupAG \
  --location eastus \
  --address-prefix 10.0.0.0/16 \
  --subnet-name myAGSubnet \
  --subnet-prefix 10.0.1.0/24
az network vnet subnet create \
  --name myBackendSubnet \
  --resource-group myResourceGroupAG \
  --vnet-name myVNet   \
  --address-prefix 10.0.2.0/24
az network public-ip create \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress
```

### <a name="backend-servers"></a>Внутренние серверы

Серверы могут состоять из сетевых адаптеров, масштабируемых наборов виртуальных машин, общедоступных IP-адресов, внутренних IP-адресов, полных доменных имен и мультитенантых серверных частей, таких как служба приложений Azure. В этом примере вы создадите две виртуальные машины для Azure, которые будут использоваться как внутренние серверы для шлюза приложений. Вы также установите службы IIS на виртуальных машинах, чтобы убедиться, что шлюз приложений успешно создан в Azure.

#### <a name="create-two-virtual-machines"></a>Создание двух виртуальных машин

Чтобы убедиться в успешном создании шлюза приложения, установите [веб-сервер Nginx](https://docs.nginx.com/nginx/) на виртуальные машины. Тот же самый файл конфигурации cloud-init можно использовать и для установки NGINX, а также для запуска простого приложения Node.js "Hello World" на виртуальной машине Linux. Дополнительные сведения о cloud-init см. в статье [Поддержка cloud-init для виртуальных машин в Azure](https://docs.microsoft.com/azure/virtual-machines/linux/using-cloud-init).

В Azure Cloud Shell создайте файл *cloud-init.txt* и вставьте в него следующую конфигурацию. Чтобы создать файл, введите *editor cloud-init.txt*.

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

Создайте сетевые интерфейсы с помощью команды [az network nic create](/cli/azure/network/nic#az-network-nic-create). Чтобы создать виртуальные машины, используйте команду [az vm create](/cli/azure/vm#az-vm-create).

```azurecli-interactive
for i in `seq 1 2`; do
  az network nic create \
    --resource-group myResourceGroupAG \
    --name myNic$i \
    --vnet-name myVNet \
    --subnet myBackendSubnet
  az vm create \
    --resource-group myResourceGroupAG \
    --name myVM$i \
    --nics myNic$i \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init.txt
done
```

## <a name="create-the-application-gateway"></a>Создание шлюза приложений

Создайте шлюз приложения с помощью команды [az network application-gateway create](/cli/azure/network/application-gateway). При создании шлюза приложения с помощью Azure CLI укажите такие сведения о конфигурации, как емкость, номер SKU и параметры HTTP. Azure добавляет частные IP-адреса сетевых интерфейсов в качестве серверов внутреннего пула шлюза приложения.

```azurecli-interactive
address1=$(az network nic show --name myNic1 --resource-group myResourceGroupAG | grep "\"privateIpAddress\":" | grep -oE '[^ ]+$' | tr -d '",')
address2=$(az network nic show --name myNic2 --resource-group myResourceGroupAG | grep "\"privateIpAddress\":" | grep -oE '[^ ]+$' | tr -d '",')
az network application-gateway create \
  --name myAppGateway \
  --location eastus \
  --resource-group myResourceGroupAG \
  --capacity 2 \
  --sku Standard_Medium \
  --http-settings-cookie-based-affinity Enabled \
  --public-ip-address myAGPublicIPAddress \
  --vnet-name myVNet \
  --subnet myAGSubnet \
  --servers "$address1" "$address2"
```

Чтобы создать шлюз приложения в Azure, может понадобиться до 30 минут. После его создания в разделе **Параметры** страницы **Шлюз приложений** можно просматривать следующие параметры:

- **appGatewayBackendPool**: отображается на странице **Серверные пулы**. Он задает требуемые значения для пулов серверной части.
- **appGatewayBackendHttpSettings**: отображается на странице **Параметры HTTP**. Он указывает, что для связи шлюз приложений использует протокол HTTP и 80 порт.
- **appGatewayHttpListener**: отображается на **Странице слушателей**. Задает прослушиватель по умолчанию, связанный с **appGatewayBackendPool**.
- **appGatewayFrontendIP**: отображается на странице **Конфигурации IP внешнего интерфейса**. Он назначает адрес *myAGPublicIPAddress* для прослушивателя **appGatewayHttpListener**.
- **rule1**: отображается на странице **Правила**. Указывает правило маршрутизации по умолчанию, связанное с **appGatewayHttpListener**.

## <a name="test-the-application-gateway"></a>Тестирование шлюза приложений

Несмотря на то что Azure не требует устанавливать веб-сервер NGINX для создания шлюза приложения, в рамках этого руководства он устанавливается, чтобы проверить, создан ли шлюз приложений. Чтобы получить общедоступный IP-адрес нового шлюза приложения, используйте команду [az network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show). 

```azurepowershell-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [ipAddress] \
  --output tsv
```

Скопируйте и вставьте общедоступный IP-адрес в адресную строку браузера.
    
![Тестирование шлюза приложений](./media/quick-create-cli/application-gateway-nginxtest.png)

Когда вы обновляете браузер, должно отобразиться имя второй виртуальной машины. Допустимый ответ подтверждает, что шлюз приложений успешно создан и может успешно подключиться к серверу.

## <a name="clean-up-resources"></a>Очистка ресурсов

Если ресурсы, которые были созданы с помощью шлюза приложения, больше не нужны, выполните команду [az group delete](/cli/azure/group#az-group-delete), чтобы удалить группу ресурсов. Удалив ее, вы также удалите шлюз приложений и все связанные с ним ресурсы.

```azurecli-interactive 
az group delete --name myResourceGroupAG
```

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Руководство по управлению веб-трафиком с помощью шлюза приложений и Azure CLI](./tutorial-manage-web-traffic-cli.md)

