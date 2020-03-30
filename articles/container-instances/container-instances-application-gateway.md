---
title: Статический IP-адрес для контейнерной группы
description: Создайте группу контейнеров в виртуальной сети и используйте шлюз приложения Azure для предоставления статического IP-адреса переднего устройства контейнерному веб-приложению
ms.topic: article
ms.date: 03/16/2020
ms.openlocfilehash: 5c3a14f93af3ecc614dc296f0a4d2815d7a64a66
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481795"
---
# <a name="expose-a-static-ip-address-for-a-container-group"></a>Разоблачить статический IP-адрес для контейнерной группы

В этой статье показан один из способов разоблачать статический общедоступный IP-адрес для [контейнерной группы](container-instances-container-groups.md) с помощью [шлюза приложения](../application-gateway/overview.md)Azure. Выполните следующие действия, когда вам нужна статическая точка входа для контейнерного приложения, работаемого в контейнерных инстанциях Azure Container Instances. 

В этой статье используется Azure CLI для создания ресурсов для этого сценария:

* Виртуальная сеть Azure.
* Контейнерная группа, [развернутая в виртуальной сети (предварительный просмотр),](container-instances-vnet.md) в котором размещено небольшое веб-приложение
* Шлюз приложения с общедоступным IP-адресом, слушателем для размещения веб-сайта на шлюзе и маршрутом к группе контейнеров бэкэнда

До тех пор, пока работает шлюз приложения и контейнерная группа предоставляет стабильный частный IP-адрес в делегированной подсети сети, группа контейнеров доступна по данному общедоступному IP-адресу.

> [!NOTE]
> Azure взимает плату за шлюз приложения в зависимости от времени, времени подготовки и доступности шлюза, а также объема данных, которые он обрабатывает. Посмотреть [цены](https://azure.microsoft.com/pricing/details/application-gateway/).

## <a name="create-virtual-network"></a>Создание виртуальной сети

В типичном случае у вас уже может быть виртуальная сеть Azure. Если у вас его нет, создайте его, как показано на следующих примерах команд. Виртуальная сеть нуждается в отдельных подсетях для шлюза приложения и контейнерной группы.

Если вам это необходимо, создайте группу ресурсов Azure. Пример:

```azureci
az group create --name myResourceGroup --location eastus
```

Создайте виртуальную сеть с помощью команды [az network vnet create][az-network-vnet-create]. Эта команда создает подсеть *myAGSubnet* в сети.

```azurecli
az network vnet create \
  --name myVNet \
  --resource-group myResourceGroup \
  --location eastus \
  --address-prefix 10.0.0.0/16 \
  --subnet-name myAGSubnet \
  --subnet-prefix 10.0.1.0/24
```

Используйте [подсеть az network vnet для создания][az-network-vnet-subnet-create] команды для создания подсети для группы контейнеров бэкэнда. Здесь он называется *myACISubnet*.

```azurecli
az network vnet subnet create \
  --name myACISubnet \
  --resource-group myResourceGroup \
  --vnet-name myVNet   \
  --address-prefix 10.0.2.0/24
```

Используйте команду [public-ip сети az для][az-network-public-ip-create] создания статического общедоступного ресурса IP. На более позднем этапе этот адрес настраивается как передний конец шлюза приложения.

```azurecli
az network public-ip create \
  --resource-group myResourceGroup \
  --name myAGPublicIPAddress \
  --allocation-method Static \
  --sku Standard
```

## <a name="create-container-group"></a>Создание группы контейнеров

Выполнить следующий [контейнер az, создаемый][az-container-create] для создания группы контейнеров в виртуальной сети, настроенной на предыдущем этапе. 

Группа развернута в подсетевом *myACISubnet* и содержит один экземпляр под `aci-helloworld` названием *appcontainer,* который вытягивает изображение. Как показано в других статьях в документации, это изображение упаковывает небольшое веб-приложение, написанное в Node.js, которое служит статичной странице HTML. 

```azurecli
az container create \
  --name appcontainer \
  --resource-group myResourceGroup \
  --image mcr.microsoft.com/azuredocs/aci-helloworld \
  --vnet myVNet \
  --subnet myACISubnet
```

При успешном развертывании группе контейнеров присваивается частный IP-адрес в виртуальной сети. Например, выполнить команду [показа следующего контейнера az][az-container-show] для получения IP-адреса группы:

```azurecli
az container show \
  --name appcontainer --resource-group myResourceGroup \
  --query ipAddress.ip --output tsv
```

Выходные данные должны быть следующего вида: `10.0.2.4`.

Для использования на более позднем этапе сохраните IP-адрес в переменной среды:

```azurecli
ACI_IP=$(az container show \
  --name appcontainer \
  --resource-group myResourceGroup \
  --query ipAddress.ip --output tsv)
```

## <a name="create-application-gateway"></a>Создание шлюза приложений

Создайте шлюз приложения в виртуальной сети, следуя шагам в [быстром запуске шлюза приложения.](../application-gateway/quick-create-cli.md) Следующие [az сетевого приложения-шлюза создают][az-network-application-gateway-create] команду создает шлюз с общедоступным IP-адресом и маршрутом к группе контейнеров бэкэнда. Ознакомиться с [документацией прикладного шлюза](/azure/application-gateway/) можно узнать о настройках шлюза.

```azurecli
az network application-gateway create \
  --name myAppGateway \
  --location eastus \
  --resource-group myResourceGroup \
  --capacity 2 \
  --sku Standard_v2 \
  --http-settings-protocol http \
  --public-ip-address myAGPublicIPAddress \
  --vnet-name myVNet \
  --subnet myAGSubnet \
  --servers "$ACI_IP" 
```


Создание шлюза приложения может занять до 15 минут. 

## <a name="test-public-ip-address"></a>Тестировать публичный IP-адрес
  
Теперь вы можете протестировать доступ к веб-приложению, работая в группе контейнеров за шлюзом приложения.

Выполнить команду [публичного шоу сети az-ip,][az-network-public-ip-show] чтобы получить общедоступный IP-адрес шлюза:

```azurecli
az network public-ip show \
--resource-group myresourcegroup \
--name myAGPublicIPAddress \
--query [ipAddress] \
--output tsv
```

Выход является общедоступным IP-адресом, похожим на: `52.142.18.133`.

Чтобы просмотреть запущенное веб-приложение при успешном настройке, перейдите на общедоступный IP-адрес шлюза в браузере. Успешный доступ аналогичен:

![Снимок экрана браузера: приложение, выполняющееся в экземпляре контейнера Azure](./media/container-instances-application-gateway/aci-app-app-gateway.png)

## <a name="next-steps"></a>Дальнейшие действия

* Смотрите [шаблон quickstart](https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-wordpress-vnet) для создания контейнерной группы с экземпляром контейнера WordPress в качестве сервера бэкэнда за шлюзом приложения.
* Вы также можете настроить шлюз приложения с сертификатом для прекращения SSL. Смотрите [обзор](../application-gateway/ssl-overview.md) и [учебник](../application-gateway/create-ssl-portal.md).
* В зависимости от сценария следует использовать другие решения для балансировки нагрузки Azure с azure Container Instances. Например, используйте [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) для распределения трафика по нескольким экземплярам контейнеров и по нескольким регионам. См. [статью в блоге](https://aaronmsft.com/posts/azure-container-instances/).

[az-network-vnet-create]:  /cli/azure/network/vnet#az-network-vnet-create
[az-network-vnet-subnet-create]: /cli/azure/network/vnet/subnet#az-network-vnet-subnet-create
[az-network-public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[az-network-public-ip-show]: /cli/azure/network/public-ip#az-network-public-ip-show
[az-network-application-gateway-create]: /cli/azure/network/application-gateway#az-network-application-gateway-create
[az-container-create]: /cli/azure/container#az-container-create
[az-container-show]: /cli/azure/container#az-container-show