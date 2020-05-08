---
title: Ограничение доступа с помощью конечной точки службы
description: Ограничение доступа к реестру контейнеров Azure с помощью конечной точки службы в виртуальной сети Azure
ms.topic: article
ms.date: 05/04/2020
ms.openlocfilehash: da5ab67d6658d8760565353e2a690c53d862d0ed
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/08/2020
ms.locfileid: "82982587"
---
# <a name="restrict-access-to-a-container-registry-using-a-service-endpoint-in-an-azure-virtual-network"></a>Ограничение доступа к реестру контейнеров с помощью конечной точки службы в виртуальной сети Azure

[Виртуальная сеть Azure](../virtual-network/virtual-networks-overview.md) предоставляет защищенные частные сети для Azure и локальных ресурсов. [Конечная точка службы](../virtual-network/virtual-network-service-endpoints-overview.md) позволяет защитить общедоступный IP-адрес реестра контейнеров только от виртуальной сети. Эта конечная точка предоставляет трафику оптимальный маршрут к ресурсу через магистральную сеть Azure. В каждом запросе также передаются удостоверения виртуальной сети и подсети.

В этой статье показано, как настроить конечную точку службы реестра контейнеров (Предварительная версия) в виртуальной сети. 

> [!IMPORTANT]
> Реестр контейнеров Azure теперь поддерживает [частную связь Azure](container-registry-private-link.md), позволяя размещать частные конечные точки из виртуальной сети в реестре. Частные конечные точки доступны в виртуальной сети с использованием частных IP-адресов. В большинстве сетевых сценариев вместо конечных точек службы рекомендуется использовать частные конечные точки.

Настройка конечной точки службы реестра доступна на уровне служб реестра **класса Premium** . Сведения об уровнях и ограничениях служб реестра см. в статье [уровни реестра контейнеров Azure](container-registry-skus.md).

## <a name="preview-limitations"></a>Ограничения предварительной версии

* Дальнейшая разработка конечных точек служб для реестра контейнеров Azure сейчас не запланирована. Вместо этого рекомендуется использовать [частные конечные точки](container-registry-private-link.md) .
* Вы не можете использовать портал Azure для настройки конечных точек службы в реестре.
* В качестве узла для доступа к реестру контейнеров с помощью конечной точки службы можно использовать только кластер [службы Kubernetes Azure](../aks/intro-kubernetes.md) или [виртуальную машину](../virtual-machines/linux/overview.md) Azure. *Другие службы Azure, включая службу "экземпляры контейнеров Azure", не поддерживаются.*
* Каждый реестр поддерживает не более 100 правил доступа к сети.

## <a name="prerequisites"></a>Предварительные условия

* Чтобы использовать Azure CLI действия, описанные в этой статье, требуется Azure CLI версии 2.0.58 или более поздней. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0][azure-cli].

* Если у вас еще нет реестра контейнеров, создайте его (требуется номер SKU уровня "Премиум") и отправьте пример образа `hello-world` , например из DOCKER Hub. Например, для создания реестра используйте [портал Azure][quickstart-portal] или [Azure CLI][quickstart-cli] . 

* Если вы хотите ограничить доступ к реестру с помощью конечной точки службы в другой подписке Azure, зарегистрируйте поставщик ресурсов для реестра контейнеров Azure в этой подписке. Пример:

  ```azurecli
  az account set --subscription <Name or ID of subscription of virtual network>

  az provider register --namespace Microsoft.ContainerRegistry
  ``` 

[!INCLUDE [Set up Docker-enabled VM](../../includes/container-registry-docker-vm-setup.md)]

## <a name="configure-network-access-for-registry"></a>Настройка доступа к сети для реестра

В этом разделе вы настроите реестр контейнеров, чтобы разрешить доступ из подсети в виртуальной сети Azure. Предусмотрены эквивалентные действия с использованием Azure CLI и портал Azure.

### <a name="allow-access-from-a-virtual-network---cli"></a>Разрешение доступа из виртуальной сети с помощью интерфейса командной строки

#### <a name="add-a-service-endpoint-to-a-subnet"></a>Добавление конечной точки службы в подсеть

При создании виртуальной машины Azure по умолчанию создает виртуальную сеть в той же группе ресурсов. Имя виртуальной сети зависит от имени виртуальной машины. Например, если вы назначите имя виртуальной машины *myDockerVM*, имя виртуальной сети по умолчанию — *мидоккервмвнет*с подсетью с именем *мидоккервмсубнет*. Проверьте это в портал Azure или с помощью команды [AZ Network vnet List][az-network-vnet-list] :

```azurecli
az network vnet list \
  --resource-group myResourceGroup \
  --query "[].{Name: name, Subnet: subnets[0].name}"
```

Результат

```console
[
  {
    "Name": "myDockerVMVNET",
    "Subnet": "myDockerVMSubnet"
  }
]
```

Чтобы добавить конечную точку службы **Microsoft. ContainerRegistry** в подсеть, используйте команду [AZ Network vnet подсети Update][az-network-vnet-subnet-update] . Замените имена виртуальной сети и подсети в следующей команде:

```azurecli
az network vnet subnet update \
  --name myDockerVMSubnet \
  --vnet-name myDockerVMVNET \
  --resource-group myResourceGroup \
  --service-endpoints Microsoft.ContainerRegistry
```

Чтобы получить идентификатор ресурса подсети, используйте команду [AZ Network vnet Subnet показывать][az-network-vnet-subnet-show] . Это необходимо на следующем шаге, чтобы настроить правило доступа к сети.

```azurecli
az network vnet subnet show \
  --name myDockerVMSubnet \
  --vnet-name myDockerVMVNET \
  --resource-group myResourceGroup \
  --query "id"
  --output tsv
``` 

Результат

```
/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myDockerVMVNET/subnets/myDockerVMSubnet
```

#### <a name="change-default-network-access-to-registry"></a>Изменение сетевого доступа по умолчанию к реестру

По умолчанию реестр контейнеров Azure разрешает подключения от узлов в любой сети. Чтобы ограничить доступ к выбранной сети, измените действие по умолчанию, чтобы запретить доступ. Замените имя реестра в следующей команде [AZ контроля][az-acr-update] доступа на обновление:

```azurecli
az acr update --name myContainerRegistry --default-action Deny
```

#### <a name="add-network-rule-to-registry"></a>Добавить сетевое правило в реестр

Чтобы добавить сетевое правило в реестр, которое разрешает доступ из подсети виртуальной машины, используйте команду AZ запись в [сеть — правило Add][az-acr-network-rule-add] . Замените имя и идентификатор ресурса подсети в реестре контейнеров следующей командой: 

 ```azurecli
az acr network-rule add \
  --name mycontainerregistry \
  --subnet <subnet-resource-id>
```

## <a name="verify-access-to-the-registry"></a>Проверка доступа к реестру

После ожидания обновления конфигурации через несколько минут убедитесь, что виртуальная машина может получить доступ к реестру контейнеров. Установите SSH-подключение к виртуальной машине и выполните команду [AZ запись контроля][az-acr-login] доступа, чтобы войти в реестр. 

```bash
az acr login --name mycontainerregistry
```

Для извлечения образца образа из реестра можно выполнить `docker pull` операции с реестром, такие как Run. Замените образ и значение тега, подходящее для реестра, с префиксом имени сервера входа в реестр (все строчные):

```bash
docker pull mycontainerregistry.azurecr.io/hello-world:v1
``` 

DOCKER успешно запрашивает образ на виртуальной машине.

В этом примере показано, как получить доступ к частному реестру контейнеров с помощью правила доступа к сети. Однако доступ к реестру с узла входа, на котором не настроено правило доступа к сети, невозможен. При попытке войти с другого узла с помощью `az acr login` команды или `docker login` команды Output будет выглядеть следующим образом:

```Console
Error response from daemon: login attempt to https://xxxxxxx.azurecr.io/v2/ failed with status: 403 Forbidden
```

## <a name="restore-default-registry-access"></a>Восстановление доступа к реестру по умолчанию

Чтобы восстановить реестр, чтобы разрешить доступ по умолчанию, удалите все настроенные сетевые правила. Затем задайте действие по умолчанию, разрешающее доступ. Предусмотрены эквивалентные действия с использованием Azure CLI и портал Azure.

### <a name="restore-default-registry-access---cli"></a>Восстановление доступа к реестру по умолчанию — CLI

#### <a name="remove-network-rules"></a>Удалить правила сети

Чтобы просмотреть список сетевых правил, настроенных для реестра, выполните следующую команду [AZ контроля сети — список правил][az-acr-network-rule-list] :

```azurecli
az acr network-rule list --name mycontainerregistry 
```

Для каждого настроенного правила выполните команду AZ запись [контроля сети — правило удаления][az-acr-network-rule-remove] , чтобы удалить его. Пример:

```azurecli
# Remove a rule that allows access for a subnet. Substitute the subnet resource ID.

az acr network-rule remove \
  --name mycontainerregistry \
  --subnet /subscriptions/ \
  xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myDockerVMVNET/subnets/myDockerVMSubnet
```

#### <a name="allow-access"></a>Разрешить доступ

Замените имя реестра в следующей команде [AZ контроля][az-acr-update] доступа на обновление:
```azurecli
az acr update --name myContainerRegistry --default-action Allow
```

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы создали все ресурсы Azure в одной группе ресурсов и больше не нуждаются в них, вы можете при необходимости удалить ресурсы с помощью одной команды [AZ Group Delete](/cli/azure/group) :

```azurecli
az group delete --name myResourceGroup
```

Чтобы очистить ресурсы на портале, перейдите к группе ресурсов myResourceGroup. После загрузки группы ресурсов щелкните **Удалить группу ресурсов** , чтобы удалить группу ресурсов и ресурсы, хранящиеся в ней.

## <a name="next-steps"></a>Дальнейшие действия

* Сведения об ограничении доступа к реестру с помощью частной конечной точки в виртуальной сети см. в статье [Настройка частного канала Azure для реестра контейнеров Azure](container-registry-private-link.md).
* Если необходимо настроить правила доступа к реестру из брандмауэра клиента, см. раздел [Настройка правил для доступа к реестру контейнеров Azure за брандмауэром](container-registry-firewall-access-rules.md).


<!-- IMAGES -->

[acr-subnet-service-endpoint]: ./media/container-registry-vnet/acr-subnet-service-endpoint.png


<!-- LINKS - External -->
[aci-helloworld]: https://hub.docker.com/r/microsoft/aci-helloworld/
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-acr-repository-show]: /cli/azure/acr/repository#az-acr-repository-show
[az-acr-repository-list]: /cli/azure/acr/repository#az-acr-repository-list
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-network-rule-add]: /cli/azure/acr/network-rule/#az-acr-network-rule-add
[az-acr-network-rule-remove]: /cli/azure/acr/network-rule/#az-acr-network-rule-remove
[az-acr-network-rule-list]: /cli/azure/acr/network-rule/#az-acr-network-rule-list
[az-acr-run]: /cli/azure/acr#az-acr-run
[az-acr-update]: /cli/azure/acr#az-acr-update
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-group-create]: /cli/azure/group
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-vm-create]: /cli/azure/vm#az-vm-create
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet/#az-network-vnet-subnet-show
[az-network-vnet-subnet-update]: /cli/azure/network/vnet/subnet/#az-network-vnet-subnet-update
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet/#az-network-vnet-subnet-show
[az-network-vnet-list]: /cli/azure/network/vnet/#az-network-vnet-list
[quickstart-portal]: container-registry-get-started-portal.md
[quickstart-cli]: container-registry-get-started-azure-cli.md
[azure-portal]: https://portal.azure.com
