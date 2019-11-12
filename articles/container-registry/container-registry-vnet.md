---
title: Ограничение доступа к реестру контейнеров Azure с помощью виртуальной сети
description: Разрешите доступ к реестру контейнеров Azure только из ресурсов в виртуальной сети Azure или из диапазонов общедоступных IP-адресов.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 07/01/2019
ms.author: danlep
ms.openlocfilehash: 5ba5c180def9539c486fb8727a0a78b4f98fa185
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/12/2019
ms.locfileid: "73931335"
---
# <a name="restrict-access-to-an-azure-container-registry-using-an-azure-virtual-network-or-firewall-rules"></a>Ограничение доступа к реестру контейнеров Azure с помощью виртуальной сети или правил брандмауэра Azure

[Виртуальная сеть Azure](../virtual-network/virtual-networks-overview.md) предоставляет защищенные частные сети для Azure и локальных ресурсов. Ограничив доступ к частному реестру контейнеров Azure из виртуальной сети Azure, вы гарантируете, что только ресурсы в виртуальной сети будут иметь доступ к реестру. Для распределенных сценариев можно также настроить правила брандмауэра, чтобы разрешить доступ к реестру только с конкретных IP-адресов.

В этой статье описаны два сценария настройки правил входящего доступа к сети в реестре контейнеров: из виртуальной машины, развернутой в виртуальной сети, или из общедоступного IP-адреса виртуальной машины.

> [!IMPORTANT]
> Сейчас эта функция доступна в предварительной версии, и применяются [некоторые ограничения](#preview-limitations). Предварительные версии предоставляются при условии, что вы принимаете [дополнительные условия использования][terms-of-use]. Некоторые аспекты этой функции могут быть изменены до выхода общедоступной версии.
>

Если вместо этого необходимо настроить правила доступа для ресурсов, чтобы получить доступ к реестру контейнеров из защищенного брандмауэра, см. раздел [Настройка правил для доступа к реестру контейнеров Azure за брандмауэром](container-registry-firewall-access-rules.md).


## <a name="preview-limitations"></a>Ограничения предварительной версии

* Только реестр контейнеров уровня " **премиум** " можно настроить с помощью правил доступа к сети. Сведения о уровнях служб реестра см. в статье [номера SKU реестра контейнеров Azure](container-registry-skus.md). 

* В качестве узла для доступа к реестру контейнеров в виртуальной сети можно использовать только кластер [службы Kubernetes Azure](../aks/intro-kubernetes.md) или [виртуальную машину](../virtual-machines/linux/overview.md) Azure. *Другие службы Azure, включая экземпляры контейнеров Azure, сейчас не поддерживаются.*

* Операции с [задачами записи контроля](container-registry-tasks-overview.md) доступа в настоящее время не поддерживаются в реестре контейнеров, доступном в виртуальной сети.

* Каждый реестр поддерживает не более 100 правил виртуальной сети.

## <a name="prerequisites"></a>предварительным требованиям

* Чтобы использовать Azure CLI действия, описанные в этой статье, требуется Azure CLI версии 2.0.58 или более поздней. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0][azure-cli].

* Если у вас еще нет реестра контейнеров, создайте его (номер SKU Premium) и отправьте пример изображения, например `hello-world` из DOCKER Hub. Например, для создания реестра используйте [портал Azure][quickstart-portal] или [Azure CLI][quickstart-cli] . 

* Если вы хотите ограничить доступ к реестру с помощью виртуальной сети в другой подписке Azure, необходимо зарегистрировать поставщик ресурсов для реестра контейнеров Azure в этой подписке. Например,

  ```azurecli
  az account set --subscription <Name or ID of subscription of virtual network>

  az provider register --namespace Microsoft.ContainerRegistry
  ``` 

## <a name="about-network-rules-for-a-container-registry"></a>Общие сведения о правилах сети для реестра контейнеров

По умолчанию реестр контейнеров Azure принимает подключения через Интернет от узлов в любой сети. С помощью виртуальной сети можно разрешить безопасный доступ к реестру только ресурсам Azure, таким как кластер AKS или виртуальная машина Azure, без пересечения границ сети. Можно также настроить правила брандмауэра сети так, чтобы разрешить только определенные диапазоны общедоступных IP-адресов Интернета. 

Чтобы ограничить доступ к реестру, сначала измените действие по умолчанию реестра, чтобы оно отклоняет все сетевые подключения. Затем добавьте правила сетевого доступа. Клиенты, которым предоставлен доступ через сетевые правила, должны продолжать [проходить проверку подлинности в реестре контейнеров](https://docs.microsoft.com/azure/container-registry/container-registry-authentication) и иметь права доступа к данным.

### <a name="service-endpoint-for-subnets"></a>Конечная точка службы для подсетей

Чтобы разрешить доступ из подсети в виртуальной сети, необходимо добавить [конечную точку службы](../virtual-network/virtual-network-service-endpoints-overview.md) для службы реестра контейнеров Azure. 

Службы с несколькими клиентами, такие как реестр контейнеров Azure, используют один набор IP-адресов для всех клиентов. Конечная точка службы назначает конечную точку для доступа к реестру. Эта конечная точка предоставляет трафику оптимальный маршрут к ресурсу через магистральную сеть Azure. В каждом запросе также передаются удостоверения виртуальной сети и подсети.

### <a name="firewall-rules"></a>Правила брандмауэра

Для правил IP-сети укажите разрешенные диапазоны адресов Интернета, используя нотацию CIDR, например *16.17.18.0/24* , или отдельные IP-адреса, например *16.17.18.19*. Правила IP-сети разрешены только для *общедоступных* IP-адресов Интернета. Диапазоны IP-адресов, зарезервированные для частных сетей (как определено в RFC 1918), не разрешены в правилах IP-адресов.

## <a name="create-a-docker-enabled-virtual-machine"></a>Создание виртуальной машины с поддержкой DOCKER

В этой статье для доступа к реестру контейнеров Azure Используйте виртуальную машину Ubuntu с поддержкой DOCKER. Чтобы использовать Azure Active Directoryную проверку подлинности в реестре, также установите [Azure CLI][azure-cli] на виртуальной машине. Если у вас уже есть виртуальная машина Azure, пропустите этот шаг создания.

Вы можете использовать одну и ту же группу ресурсов для виртуальной машины и реестра контейнеров. Эта настройка упрощает очистку в конце, но не является обязательной. Если вы решили создать отдельную группу ресурсов для виртуальной машины и виртуальной сети, выполните команду [AZ Group Create][az-group-create]. В следующем примере создается группа ресурсов с именем *myResourceGroup* в расположении *westcentralus* :

```azurecli
az group create --name myResourceGroup --location westus
```

Теперь разверните виртуальную машину Ubuntu Azure по умолчанию с помощью команды [AZ VM Create][az-vm-create]. В следующем примере создается виртуальная машина с именем *myDockerVM*:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myDockerVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

Создание виртуальной машины может занять несколько минут. После выполнения команды запишите значение `publicIpAddress`, отображаемое Azure CLI. Используйте этот адрес, чтобы установить SSH-подключения к виртуальной машине и, возможно, для последующей установки правил брандмауэра.

### <a name="install-docker-on-the-vm"></a>Установка Docker на виртуальной машине

После запуска виртуальной машины установите SSH-подключение к ней. Замените *publicIpAddress* общедоступным IP-адресом виртуальной машины.

```bash
ssh azureuser@publicIpAddress
```

Выполните следующую команду, чтобы установить DOCKER на виртуальной машине Ubuntu:

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

Выполните действия, описанные в статье [Установка Azure CLI с помощью apt](/cli/azure/install-azure-cli-apt?view=azure-cli-latest), чтобы установить Azure CLI на виртуальной машине Ubuntu. Для этой статьи убедитесь, что установлена версия 2.0.58 или более поздняя.

Выйдите из SSH-подключения.

## <a name="allow-access-from-a-virtual-network"></a>Разрешить доступ из виртуальной сети

В этом разделе вы настроите реестр контейнеров, чтобы разрешить доступ из подсети в виртуальной сети Azure. Предусмотрены эквивалентные действия с использованием Azure CLI и портал Azure.

### <a name="allow-access-from-a-virtual-network---cli"></a>Разрешение доступа из виртуальной сети с помощью интерфейса командной строки

#### <a name="add-a-service-endpoint-to-a-subnet"></a>Добавление конечной точки службы в подсеть

При создании виртуальной машины Azure по умолчанию создает виртуальную сеть в той же группе ресурсов. Имя виртуальной сети зависит от имени виртуальной машины. Например, если вы назначите имя виртуальной машины *myDockerVM*, имя виртуальной сети по умолчанию — *мидоккервмвнет*с подсетью с именем *мидоккервмсубнет*. Проверьте это в портал Azure или с помощью команды [AZ Network vnet List][az-network-vnet-list] :

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

Выходные данные:

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
az acr network-rule add --name mycontainerregistry --subnet <subnet-resource-id>
```

Продолжайте [проверку доступа к реестру](#verify-access-to-the-registry).

### <a name="allow-access-from-a-virtual-network---portal"></a>Разрешение доступа из виртуальной сети с помощью портала

#### <a name="add-service-endpoint-to-subnet"></a>Добавление конечной точки службы в подсеть

При создании виртуальной машины Azure по умолчанию создает виртуальную сеть в той же группе ресурсов. Имя виртуальной сети зависит от имени виртуальной машины. Например, если вы назначите имя виртуальной машины *myDockerVM*, имя виртуальной сети по умолчанию — *мидоккервмвнет*с подсетью с именем *мидоккервмсубнет*.

Чтобы добавить конечную точку службы для реестра контейнеров Azure в подсеть, выполните следующие действия.

1. В поле поиска в верхней части [портал Azure][azure-portal]введите *виртуальные сети*. Когда в результатах поиска появится пункт **Виртуальные сети**, выберите его.
1. В списке виртуальных сетей выберите виртуальную сеть, в которой развернута виртуальная машина, например *мидоккервмвнет*.
1. В разделе **Параметры**выберите **подсети**.
1. Выберите подсеть, в которой развернута виртуальная машина, например *мидоккервмсубнет*.
1. В разделе **конечные точки службы**выберите **Microsoft. ContainerRegistry**.
1. Щелкните **Сохранить**.

![Добавление конечной точки службы в подсеть][acr-subnet-service-endpoint] 

#### <a name="configure-network-access-for-registry"></a>Настройка доступа к сети для реестра

По умолчанию реестр контейнеров Azure разрешает подключения от узлов в любой сети. Чтобы ограничить доступ к виртуальной сети, сделайте следующее:

1. На портале перейдите к реестру контейнеров.
1. В разделе **Параметры**выберите **брандмауэр и виртуальные сети**.
1. Чтобы запретить доступ по умолчанию, разрешите доступ в разделе **Выбранные сети**. 
1. Выберите **Добавить существующую виртуальную сеть**и выберите виртуальную сеть и подсеть, настроенные с помощью конечной точки службы. Выберите **Добавить**.
1. Щелкните **Сохранить**.

![Настройка виртуальной сети для реестра контейнеров][acr-vnet-portal]

Продолжайте [проверку доступа к реестру](#verify-access-to-the-registry).

## <a name="allow-access-from-an-ip-address"></a>Разрешить доступ с IP-адреса

В этом разделе вы настроите реестр контейнеров, чтобы разрешить доступ с определенного IP-адреса или диапазона. Предусмотрены эквивалентные действия с использованием Azure CLI и портал Azure.

### <a name="allow-access-from-an-ip-address---cli"></a>Разрешение доступа с IP-адреса — CLI

#### <a name="change-default-network-access-to-registry"></a>Изменение сетевого доступа по умолчанию к реестру

Если вы еще не сделали этого, обновите конфигурацию реестра, чтобы запретить доступ по умолчанию. Замените имя реестра в следующей команде [AZ контроля][az-acr-update] доступа на обновление:

```azurecli
az acr update --name myContainerRegistry --default-action Deny
```

#### <a name="remove-network-rule-from-registry"></a>Удалить правило сети из реестра

Если вы ранее добавили сетевое правило, разрешающее доступ из подсети виртуальной машины, удалите конечную точку службы подсети и правило сети. Замените имя реестра контейнеров и идентификатор ресурса подсети, полученной на предыдущем шаге, в команде [AZ запись контроля доступа сеть — правило удаления][az-acr-network-rule-remove] : 

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

#### <a name="add-network-rule-to-registry"></a>Добавить сетевое правило в реестр

Чтобы добавить сетевое правило в реестр, которое разрешает доступ с IP-адреса виртуальной машины, используйте команду AZ запись в [сеть — правило добавления][az-acr-network-rule-add] . Замените имя реестра контейнеров и общедоступный IP-адрес виртуальной машины в следующей команде.

```azurecli
az acr network-rule add --name mycontainerregistry --ip-address <public-IP-address>
```

Продолжайте [проверку доступа к реестру](#verify-access-to-the-registry).

### <a name="allow-access-from-an-ip-address---portal"></a>Разрешение доступа с IP-адреса на портале

#### <a name="remove-existing-network-rule-from-registry"></a>Удалить существующее правило сети из реестра

Если вы ранее добавили сетевое правило, разрешающее доступ из подсети виртуальной машины, удалите существующее правило. Пропустите этот раздел, если хотите получить доступ к реестру из другой виртуальной машины.

* Обновите параметры подсети, чтобы удалить конечную точку службы подсети для реестра контейнеров Azure. 

  1. В [портал Azure][azure-portal]перейдите к виртуальной сети, в которой развернута виртуальная машина.
  1. В разделе **Параметры**выберите **подсети**.
  1. Выберите подсеть, в которой развернута виртуальная машина.
  1. В разделе **конечные точки службы**удалите флажок для **Microsoft. ContainerRegistry**. 
  1. Щелкните **Сохранить**.

* Удалите сетевое правило, которое разрешает подсети доступ к реестру.

  1. На портале перейдите к реестру контейнеров.
  1. В разделе **Параметры**выберите **брандмауэр и виртуальные сети**.
  1. В разделе **виртуальные сети**выберите имя виртуальной сети, а затем щелкните **Удалить**.
  1. Щелкните **Сохранить**.

#### <a name="add-network-rule-to-registry"></a>Добавить сетевое правило в реестр

1. На портале перейдите к реестру контейнеров.
1. В разделе **Параметры**выберите **брандмауэр и виртуальные сети**.
1. Если вы еще не сделали этого, выберите Разрешить доступ из **выбранных сетей**. 
1. Убедитесь, что в разделе **виртуальные сети**не выбрана сеть.
1. В разделе **брандмауэр**введите общедоступный IP-адрес виртуальной машины. Или введите диапазон адресов в нотации CIDR, которая содержит IP-адрес виртуальной машины.
1. Щелкните **Сохранить**.

![Настройка правила брандмауэра для реестра контейнеров][acr-vnet-firewall-portal]

Продолжайте [проверку доступа к реестру](#verify-access-to-the-registry).

## <a name="verify-access-to-the-registry"></a>Проверка доступа к реестру

После ожидания обновления конфигурации через несколько минут убедитесь, что виртуальная машина может получить доступ к реестру контейнеров. Установите SSH-подключение к виртуальной машине и выполните команду [AZ запись контроля][az-acr-login] доступа, чтобы войти в реестр. 

```bash
az acr login --name mycontainerregistry
```

Вы можете выполнять операции с реестром, такие как Run `docker pull`, чтобы извлечь пример образа из реестра. Замените образ и значение тега, подходящее для реестра, с префиксом имени сервера входа в реестр (все строчные):

```bash
docker pull mycontainerregistry.azurecr.io/hello-world:v1
``` 

DOCKER успешно запрашивает образ на виртуальной машине.

В этом примере показано, как получить доступ к частному реестру контейнеров с помощью правила доступа к сети. Однако доступ к реестру с другого узла входа, на котором не настроено правило доступа к сети, невозможен. При попытке войти с другого узла с помощью команды `az acr login` или команды `docker login` выходные данные будут выглядеть следующим образом:

```Console
Error response from daemon: login attempt to https://xxxxxxx.azurecr.io/v2/ failed with status: 403 Forbidden
```

## <a name="restore-default-registry-access"></a>Восстановление доступа к реестру по умолчанию

Чтобы восстановить реестр, чтобы разрешить доступ по умолчанию, удалите все настроенные сетевые правила. Затем задайте действие по умолчанию, разрешающее доступ. Предусмотрены эквивалентные действия с использованием Azure CLI и портал Azure.

### <a name="restore-default-registry-access---cli"></a>Восстановление доступа к реестру по умолчанию — CLI

#### <a name="remove-network-rules"></a>Удалить правила сети

Чтобы просмотреть список сетевых правил, настроенных для реестра, выполните следующую команду [AZ контроля сети — список правил][az-acr-network-rule-list] :

```azurecli
az acr network-rule list--name mycontainerregistry 
```

Для каждого настроенного правила выполните команду AZ запись [контроля сети — правило удаления][az-acr-network-rule-remove] , чтобы удалить его. Например,

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

Замените имя реестра в следующей команде [AZ контроля][az-acr-update] доступа на обновление:
```azurecli
az acr update --name myContainerRegistry --default-action Allow
```

### <a name="restore-default-registry-access---portal"></a>Восстановление доступа к реестру по умолчанию — портал


1. На портале перейдите к реестру контейнеров и выберите **брандмауэр и виртуальные сети**.
1. В разделе **виртуальные сети**выберите каждую виртуальную сеть, а затем щелкните **Удалить**.
1. В разделе **брандмауэр**выберите каждый диапазон адресов, а затем щелкните значок Удалить.
1. В разделе **Разрешить доступ из**выберите **все сети**. 
1. Щелкните **Сохранить**.

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы создали все ресурсы Azure в одной группе ресурсов и больше не нуждаются в них, вы можете при необходимости удалить ресурсы с помощью одной команды [AZ Group Delete](/cli/azure/group) :

```azurecli
az group delete --name myResourceGroup
```

Чтобы очистить ресурсы на портале, перейдите к группе ресурсов myResourceGroup. После загрузки группы ресурсов щелкните **Удалить группу ресурсов** , чтобы удалить группу ресурсов и ресурсы, хранящиеся в ней.

## <a name="next-steps"></a>Дополнительная информация

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
