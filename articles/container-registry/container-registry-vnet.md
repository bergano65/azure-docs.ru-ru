---
title: Развертывание реестра контейнеров Azure в виртуальной сети
description: Разрешить доступ к реестру контейнеров Azure только с ресурсами в виртуальной сети Azure или общедоступный IP-адресов.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 04/03/2019
ms.author: danlep
ms.openlocfilehash: dc08fd5cc4abbf5d16f9d49874ec2c70cace165b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67067965"
---
# <a name="restrict-access-to-an-azure-container-registry-using-an-azure-virtual-network-or-firewall-rules"></a>Ограничение доступа к реестру контейнеров Azure с помощью виртуальной сети Azure или правила брандмауэра

[Виртуальная сеть Azure](../virtual-network/virtual-networks-overview.md) обеспечивает безопасное и частных сетей для Azure и локальным ресурсам. Развертывание в частный реестр контейнеров Azure в виртуальной сети Azure, гарантирует, что только ресурсы в виртуальной сети, доступ к реестру. Для сценариев распределенного развертывания можно также настроить правила брандмауэра, чтобы разрешить доступ к реестру, только с определенных IP-адресов.

В этой статье показано два сценария, чтобы создать правила доступа к сети для ограничения доступа к реестру контейнеров Azure: из виртуальной машине, развернутой в той же сети, или из виртуальной Машины общедоступный IP-адрес.

> [!IMPORTANT]
> Сейчас эта функция доступна в предварительной версии, и применяются [некоторые ограничения](#preview-limitations). Предварительные версии предоставляются только в том случае, если вы принимаете [дополнительные условия использования][terms-of-use]. Некоторые аспекты этой функции могут быть изменены до выхода общедоступной версии.
>

## <a name="preview-limitations"></a>Ограничения предварительной версии

* Только **уровня "премиум"** реестр контейнеров можно настроить правила доступа к сети. Сведения об уровнях служб реестра, см. в разделе [SKU реестра контейнеров Azure](container-registry-skus.md). 

* Только [службе Azure Kubernetes](../aks/intro-kubernetes.md) кластера или Azure [виртуальной машины](../virtual-machines/linux/overview.md) может использоваться в качестве узла для доступа к реестру контейнеров в виртуальной сети. *Другие службы Azure, включая экземпляры контейнеров Azure сейчас не поддерживаются.*

* [Задачи ACR](container-registry-tasks-overview.md) операции не поддерживаются в реестр контейнеров, развернутых в виртуальной сети.

* Каждый реестр поддерживает не более 100 правил виртуальной сети.

## <a name="prerequisites"></a>Технические условия

* Чтобы использовать Azure CLI описанные в этой статье, Azure CLI версии 2.0.58 или более поздней. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0][azure-cli].

* Если у вас еще нет в реестр контейнеров, создайте ее (требуется SKU уровня "премиум") и Push-пример изображения, такие как `hello-world` из Docker Hub. Например, использовать [портала Azure] [ quickstart-portal] или [Azure CLI] [ quickstart-cli] Создание реестра. 

## <a name="about-network-rules-for-a-container-registry"></a>О правилах сети для реестра контейнеров

Реестр контейнеров Azure по умолчанию принимает подключения через Интернет из узлов в любой сети. С помощью виртуальной сети можно разрешить только Azure ресурсы, например кластер службы контейнеров AZURE или виртуальной Машины Azure для безопасного доступа к реестру, не пересекая границ сети. Можно также настроить правила брандмауэра сети в список разрешений определенные общедоступный Интернет диапазоны IP-адресов. 

Чтобы ограничить доступ к реестру, сначала измените действие по умолчанию реестра таким образом, чтобы он запрещает всех сетевых подключений. Затем добавьте правила доступа к сети. Клиентам предоставляется доступ через правила сети должны продолжать [проходить проверку подлинности в реестр контейнеров](https://docs.microsoft.com/azure/container-registry/container-registry-authentication) и иметь разрешение на доступ к данным.

### <a name="service-endpoint-for-subnets"></a>Конечная точка службы для подсети

Чтобы разрешить доступ из подсети в виртуальной сети, необходимо добавить [конечная точка службы](../virtual-network/virtual-network-service-endpoints-overview.md) для службы реестра контейнеров Azure. 

Службы несколькими клиентами, такие как реестр контейнеров Azure, используют один набор IP-адресов для всех клиентов. Конечная точка службы назначает конечную точку для доступа к реестру. Эта конечная точка предоставляет трафика оптимального маршрута к ресурсу через магистральную сеть Azure. В каждом запросе также передаются удостоверения виртуальной сети и подсети.

### <a name="firewall-rules"></a>Правила файрволла

Для правила IP-сети, укажите разрешенные internet диапазоны адресов, с помощью нотации CIDR, например *16.17.18.0/24* или отдельные IP-адреса в том, как *16.17.18.19*. Правила IP-сети можно применять только для *открытый* Интернета IP-адреса. Диапазоны IP-адресов, зарезервированные для частных сетей (как определено в RFC 1918) запрещено использовать в правилах IP.

## <a name="create-a-docker-enabled-virtual-machine"></a>Создание виртуальной машины с поддержкой Docker

В этой статье используйте виртуальную Машину Ubuntu с поддержкой Docker для доступа к реестру контейнеров Azure. Чтобы использовать аутентификацию Azure Active Directory в реестр, следует также установить [Azure CLI] [ azure-cli] на виртуальной Машине. Если у вас уже есть виртуальная машина Azure, пропустите этот шаг создания.

Можно использовать ту же группу ресурсов для виртуальной машины и реестр контейнеров. Эта конфигурация упрощает очистки в конце, но не требуется. Если вы решили создать отдельную группу ресурсов для виртуальной машины и виртуальной сети, запустите [Создание группы az][az-group-create]. В следующем примере создается группа ресурсов, с именем *myResourceGroup* в *westcentralus* расположение:

```azurecli
az group create --name myResourceGroup --location westus
```

Теперь развертывание по умолчанию виртуальная машина Ubuntu в Azure с [Создание виртуальной машины az][az-vm-create]. В следующем примере создается виртуальная машина с именем *myDockerVM*:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myDockerVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

Создание виртуальной машины может занять несколько минут. После выполнения команды запишите значение `publicIpAddress`, отображаемое Azure CLI. Этот адрес используется для установления SSH-подключений к виртуальной Машине и при необходимости для более поздних Настройка правил брандмауэра.

### <a name="install-docker-on-the-vm"></a>Установка Docker на виртуальной машине

После запуска виртуальной машины установите SSH-подключение к ней. Замените *publicIpAddress* общедоступным IP-адресом виртуальной машины.

```bash
ssh azureuser@publicIpAddress
```

Выполните следующую команду, чтобы установить Docker на виртуальной Машине Ubuntu:

```bash
sudo apt install docker.io -y
```

После установки выполните следующую команду, чтобы проверить правильность работы Docker на виртуальной машине:

```bash
sudo docker run -it hello-world
```

Выходные данные:

```
Hello from Docker!
This message shows that your installation appears to be working correctly.
[...]
```

### <a name="install-the-azure-cli"></a>Установка Azure CLI

Выполните действия, описанные в статье [Установка Azure CLI с помощью apt](/cli/azure/install-azure-cli-apt?view=azure-cli-latest), чтобы установить Azure CLI на виртуальной машине Ubuntu. В этой статье убедитесь, что вы установили версию 2.0.58 или более поздней версии.

Выйдите из SSH-подключения.

## <a name="allow-access-from-a-virtual-network"></a>Разрешить доступ из виртуальной сети

В этом разделе настройте реестр контейнеров, чтобы разрешить доступ из подсети в виртуальной сети Azure. Предоставляются аналогичные шаги, с помощью Azure CLI и портала Azure.

### <a name="allow-access-from-a-virtual-network---cli"></a>Разрешить доступ из виртуальной сети — интерфейс командной строки

#### <a name="add-a-service-endpoint-to-a-subnet"></a>Добавьте конечную точку службы к подсети

При создании виртуальной Машины Azure по умолчанию создает виртуальную сеть в той же группе ресурсов. Имя виртуальной сети основан на имени виртуальной машины. Например, если вы используете имя виртуального компьютера *myDockerVM*, имя виртуальной сети по умолчанию — *myDockerVMVNET*, с подсетью, называющейся *myDockerVMSubnet*. Проверить это, на портале Azure или с помощью [az сети vnet списка] [ az-network-vnet-list] команды:

```azurecli
az network vnet list --resource-group myResourceGroup --query "[].{Name: name, Subnet: subnets[0].name}"
```

Выходные данные:

```console
[
  {
    "Name": "myDockerVMVNET",
    "Subnet": "myDockerVMSubnet"
  }
]
```

Используйте [обновление подсети виртуальной сети сети az] [ az-network-vnet-subnet-update] команду, чтобы добавить **Microsoft.ContainerRegistry** конечной точки службы в подсети. Замените имена виртуальной сети и подсети в следующую команду:

```azurecli
az network vnet subnet update \
  --name myDockerVMSubnet \
  --vnet-name myDockerVMVNET \
  --resource-group myResourceGroup \
  --service-endpoints Microsoft.ContainerRegistry
```

Используйте [show подсети виртуальной сети сети az] [ az-network-vnet-subnet-show] команду, чтобы получить идентификатор ресурса подсети. Он понадобится позже для настройки сетевого правила доступа.

```azurecli
az network vnet subnet show \
  --name myDockerVMSubnet \
  --vnet-name myDockerVMVNET \
  --resource-group myResourceGroup \
  --query "id"
  --output tsv
``` 

Выходные данные:

```
/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myDockerVMVNET/subnets/myDockerVMSubnet
```

#### <a name="change-default-network-access-to-registry"></a>Изменить по умолчанию сетевой доступ к реестру

По умолчанию реестр контейнеров Azure разрешены подключения из узлов в любой сети. Чтобы ограничить доступ к выбранной сети, измените действие по умолчанию, чтобы запретить доступ. Замените имя реестра в следующем [az acr update] [ az-acr-update] команды:

```azurecli
az acr update --name myContainerRegistry --default-action Deny
```

#### <a name="add-network-rule-to-registry"></a>Добавьте правило сети для реестра

Используйте [добавить запись контроля доступа Azure network-rule] [ az-acr-network-rule-add] команду, чтобы добавить правило сети для реестра, обеспечивающий доступ из подсети виртуальной Машины. Замените имя реестра контейнеров и идентификатор ресурса подсети в следующую команду: 

 ```azurecli
az acr network-rule add --name mycontainerregistry --subnet <subnet-resource-id>
```

По-прежнему [проверка доступа к реестру](#verify-access-to-the-registry).

### <a name="allow-access-from-a-virtual-network---portal"></a>Разрешить доступ из виртуальной сети — портал

#### <a name="add-service-endpoint-to-subnet"></a>Добавьте конечную точку службы к подсети

При создании виртуальной Машины Azure по умолчанию создает виртуальную сеть в той же группе ресурсов. Имя виртуальной сети основан на имени виртуальной машины. Например, если вы используете имя виртуального компьютера *myDockerVM*, имя виртуальной сети по умолчанию — *myDockerVMVNET*, с подсетью, называющейся *myDockerVMSubnet*.

Чтобы добавить конечную точку службы для реестра контейнеров Azure в подсети:

1. В поле поиска в верхней части [портала Azure][azure-portal], введите *виртуальных сетей*. Когда в результатах поиска появится пункт **Виртуальные сети**, выберите его.
1. В списке виртуальных сетей выберите виртуальную сеть, где ваш развернута виртуальная машина, такие как *myDockerVMVNET*.
1. В разделе **параметры**выберите **подсети**.
1. Выберите подсеть, где ваш развернута виртуальная машина, такие как *myDockerVMSubnet*.
1. В разделе **конечные точки службы**выберите **Microsoft.ContainerRegistry**.
1. Щелкните **Сохранить**.

![Добавьте конечную точку службы к подсети][acr-subnet-service-endpoint] 

#### <a name="configure-network-access-for-registry"></a>Настройка доступа к сети для реестра

По умолчанию реестр контейнеров Azure разрешены подключения из узлов в любой сети. Для ограничения доступа к виртуальной сети:

1. На портале перейдите к реестру контейнеров.
1. В разделе **параметры**выберите **брандмауэр и виртуальные сети**.
1. Чтобы запретить доступ по умолчанию, разрешите доступ в разделе **Выбранные сети**. 
1. Выберите **добавить существующую виртуальную сеть**и выберите виртуальную сеть и подсеть, вы настроили с конечной точкой службы. Выберите **Добавить**.
1. Щелкните **Сохранить**.

![Настройка виртуальной сети для реестра контейнеров][acr-vnet-portal]

По-прежнему [проверка доступа к реестру](#verify-access-to-the-registry).

## <a name="allow-access-from-an-ip-address"></a>Разрешить доступ с IP-адресом

В этом разделе настройте реестр контейнеров, чтобы разрешить доступ из конкретного IP-адреса или диапазона. Предоставляются аналогичные шаги, с помощью Azure CLI и портала Azure.

### <a name="allow-access-from-an-ip-address---cli"></a>Разрешить доступ с IP-адресом - CLI

#### <a name="change-default-network-access-to-registry"></a>Изменить по умолчанию сетевой доступ к реестру

Если вы еще не сделали, обновите конфигурацию реестра для запрета доступа по умолчанию. Замените имя реестра в следующем [az acr update] [ az-acr-update] команды:

```azurecli
az acr update --name myContainerRegistry --default-action Deny
```

#### <a name="remove-network-rule-from-registry"></a>Удалите правило сети из реестра

Если вы ранее добавили сети правило, разрешающее доступ из подсети виртуальной Машины, удалите конечную точку службы для подсети и правила сети. Замените на имя реестра контейнеров и идентификатор ресурса подсети, полученный на предыдущем шаге в [удалить запись контроля доступа Azure network-rule] [ az-acr-network-rule-remove] команды: 

```azurecli
# Remove service endpoint

az network vnet subnet update \
  --name myDockerVMSubnet \
  --vnet-name myDockerVMVNET \
  --resource-group myResourceGroup \
  --service-endpoints ""

# Remove network rule

az acr network-rule remove --name mycontainerregistry --subnet <subnet-resource-id>
```

#### <a name="add-network-rule-to-registry"></a>Добавьте правило сети для реестра

Используйте [добавить запись контроля доступа Azure network-rule] [ az-acr-network-rule-add] команду, чтобы добавить сетевые правила в реестр, которое разрешает доступ с IP-адреса виртуальной Машины. Подставьте имя реестра контейнеров и общедоступный IP-адрес виртуальной машины в следующую команду.

```azurecli
az acr network-rule add --name mycontainerregistry --ip-address <public-IP-address>
```

По-прежнему [проверка доступа к реестру](#verify-access-to-the-registry).

### <a name="allow-access-from-an-ip-address---portal"></a>Разрешить доступ с IP-адресом - портала

#### <a name="remove-existing-network-rule-from-registry"></a>Удалить существующее правило сети из реестра

Если вы ранее добавили сети правило, разрешающее доступ из подсети виртуальной Машины, удалите существующее правило. Этот раздел можно пропустите, если требуется доступ к реестру из другой виртуальной Машины.

* Чтобы удалить подсеть конечную точку службы для реестра контейнеров Azure в параметрах подсети. 

  1. В [портала Azure][azure-portal], перейдите к виртуальной сети, где развернута виртуальная машина.
  1. В разделе **параметры**выберите **подсети**.
  1. Выберите подсеть, где развернута виртуальная машина.
  1. В разделе **конечные точки службы**, удалите флажок **Microsoft.ContainerRegistry**. 
  1. Щелкните **Сохранить**.

* Удалите правило сети, которое разрешает подсеть для доступа к реестру.

  1. На портале перейдите к реестру контейнеров.
  1. В разделе **параметры**выберите **брандмауэр и виртуальные сети**.
  1. В разделе **виртуальные сети**, выберите имя виртуальной сети и выберите **удалить**.
  1. Щелкните **Сохранить**.

#### <a name="add-network-rule-to-registry"></a>Добавьте правило сети для реестра

1. На портале перейдите к реестру контейнеров.
1. В разделе **параметры**выберите **брандмауэр и виртуальные сети**.
1. Если вы еще не сделали, выберите Разрешить доступ из **выбранные сети**. 
1. В разделе **виртуальные сети**, убедитесь, сеть не выбрана.
1. В разделе **брандмауэра**, введите общедоступный IP-адрес виртуальной машины. Или введите диапазон адресов в нотации CIDR, который содержит IP-адрес виртуальной Машины.
1. Щелкните **Сохранить**.

![Настройка правила брандмауэра для реестра контейнеров][acr-vnet-firewall-portal]

По-прежнему [проверка доступа к реестру](#verify-access-to-the-registry).

## <a name="verify-access-to-the-registry"></a>Проверка доступа к реестру

Спустя несколько минут, чтобы обновления, убедитесь, что конфигурация виртуальной Машины можно получить доступ к реестра контейнеров. Подключитесь по протоколу SSH к виртуальной Машине и запустите [az acr login] [ az-acr-login] команду для входа в реестр. 

```bash
az acr login --name mycontainerregistry
```

Можно выполнять операции с реестром, например запускать `docker pull` для извлечения образца из реестра. Замените значение образ и тег, соответствующий для реестра, префикс с именем сервера входа реестра (прописными буквами):

```bash
docker pull mycontainerregistry.azurecr.io/hello-world:v1
``` 

Docker успешно извлекается образ к виртуальной Машине.

В этом примере показано, что можно обращаться из закрытого реестра контейнеров сетевого правила доступа. Тем не менее реестру нет доступа из имени узла, у которых настроен правило доступа к сети. При попытке выполнить вход с другого узла с помощью `az acr login` команды или `docker login` команды выходные данные следующего вида:

```Console
Error response from daemon: login attempt to https://xxxxxxx.azurecr.io/v2/ failed with status: 403 Forbidden
```

## <a name="restore-default-registry-access"></a>Восстановить доступ к реестру по умолчанию

Чтобы восстановить реестр, чтобы разрешить доступ по умолчанию, удалите все правила сети, настроенные. Задайте действие по умолчанию, чтобы разрешить доступ. Предоставляются аналогичные шаги, с помощью Azure CLI и портала Azure.

### <a name="restore-default-registry-access---cli"></a>Восстановить доступ к реестру по умолчанию - CLI

#### <a name="remove-network-rules"></a>Удалить правила сети

Чтобы просмотреть список правил сети, настроенных для реестра, выполните следующую команду [az acr правило сетевого списка] [ az-acr-network-rule-list] команды:

```azurecli
az acr network-rule list--name mycontainerregistry 
```

Для каждого правила, настроенного, запустите [удалить запись контроля доступа Azure network-rule] [ az-acr-network-rule-remove] команду, чтобы удалить его. Пример:

```azurecli
# Remove a rule that allows access for a subnet. Substitute the subnet resource ID.

az acr network-rule remove \
  --name mycontainerregistry \
  --subnet /subscriptions/ \
  xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myDockerVMVNET/subnets/myDockerVMSubnet

# Remove a rule that allows access for an IP address or CIDR range such as 23.45.1.0/24.

az acr network-rule remove \
  --name mycontainerregistry \
  --ip-address 23.45.1.0/24
```

#### <a name="allow-access"></a>Разрешить доступ

Замените имя реестра в следующем [az acr update] [ az-acr-update] команды:
```azurecli
az acr update --name myContainerRegistry --default-action Allow
```

### <a name="restore-default-registry-access---portal"></a>Восстановление по умолчанию доступ к реестру - портала


1. На портале перейдите к реестру контейнеров и выберите **брандмауэр и виртуальные сети**.
1. В разделе **виртуальные сети**, выберите каждую виртуальную сеть, а затем выберите **удалить**.
1. В разделе **брандмауэра**, выбирайте каждый диапазон адресов и выберите значок "Удалить".
1. В разделе **разрешить доступ из**выберите **всех сетей**. 
1. Щелкните **Сохранить**.

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы создали все группы ресурсов Azure в тот же ресурс и они больше не нужны, при необходимости можно удалить ресурсы с помощью одной [удаление группы az](/cli/azure/group) команды:

```azurecli
az group delete --name myResourceGroup
```

Чтобы очистить ресурсы на портале, перейдите к группе ресурсов myResourceGroup. После загрузки группы ресурсов щелкните **удалить группу ресурсов** для удаления группы ресурсов и ресурсы, хранящиеся в ней.

## <a name="next-steps"></a>Дальнейшие действия

В этой статье были кратко описаны несколько ресурсов и функций виртуальной сети. Более подробно эти темы рассматриваются в следующей документации по виртуальным сетям Azure:

* [Виртуальная сеть](https://docs.microsoft.com/azure/virtual-network/manage-virtual-network)
* [Подсеть](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet)
* [Конечные точки службы](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview)

<!-- IMAGES -->

[acr-subnet-service-endpoint]: ./media/container-registry-vnet/acr-subnet-service-endpoint.png
[acr-vnet-portal]: ./media/container-registry-vnet/acr-vnet-portal.png
[acr-vnet-firewall-portal]: ./media/container-registry-vnet/acr-vnet-firewall-portal.png

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
