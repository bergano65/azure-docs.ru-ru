---
title: Ограничение доступа с помощью виртуальной сети
description: Разрешить доступ к реестру контейнеров Azure только из ресурсов в виртуальной сети Azure или из общедоступных диапазонов IP-адресов.
ms.topic: article
ms.date: 07/01/2019
ms.openlocfilehash: a6b89b074c25ea0948597ede7e5681b100c7f429
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74454334"
---
# <a name="restrict-access-to-an-azure-container-registry-using-an-azure-virtual-network-or-firewall-rules"></a>Ограничьте доступ к реестру контейнеров Azure с помощью виртуальной сети Azure или правил брандмауэра

[Виртуальная сеть Azure](../virtual-network/virtual-networks-overview.md) предоставляет безопасные, частные сети для ваших ресурсов Azure и на территории. Ограничивая доступ к частному реестру контейнеров Azure из виртуальной сети Azure, вы гарантируете, что только ресурсы в виртуальной сети будут доступ к реестру. Для кросс-предпосылок можно также настроить правила брандмауэра, чтобы разрешить доступ в реестр только с определенных IP-адресов.

В этой статье показаны два сценария настройки правил входящего доступа к сети в реестре контейнеров: с виртуальной машины, развернутой в виртуальной сети, или с общедоступного IP-адреса VM.

> [!IMPORTANT]
> Сейчас эта функция доступна в предварительной версии, и применяются [некоторые ограничения](#preview-limitations). Предварительные версии предоставляются при условии, что вы принимаете [дополнительные условия использования][terms-of-use]. Некоторые аспекты этой функции могут быть изменены до выхода общедоступной версии.
>

Если вместо этого необходимо настроить правила доступа для ресурсов для доступа к реестру контейнеров из-за брандмауэра, см. [Правила настройки для доступа к реестру контейнеров Azure за брандмауэром.](container-registry-firewall-access-rules.md)


## <a name="preview-limitations"></a>Ограничения предварительной версии

* Только **реестр** контейнеров Premium может быть настроен с правилами сетевого доступа. Для получения информации о уровнях обслуживания реестра, см [Azure контейнерных реестров SKUs](container-registry-skus.md). 

* Только кластер [службы Azure Kubernetes](../aks/intro-kubernetes.md) или [виртуальная машина](../virtual-machines/linux/overview.md) Azure могут быть использованы в качестве хоста для доступа к реестру контейнеров в виртуальной сети. *Другие службы Azure, включая Экземпляры контейнеров Azure, в настоящее время не поддерживаются.*

* Операции [ACR Tasks](container-registry-tasks-overview.md) в настоящее время не поддерживаются в реестре контейнеров, доступ к которой осуществляется в виртуальной сети.

* Каждый реестр поддерживает не более 100 виртуальных сетевых правил.

## <a name="prerequisites"></a>Предварительные требования

* Для использования шагов Azure CLI в этой статье требуется версия Azure CLI 2.0.58 или позже. Если вам нужно установить или обновить, [см.][azure-cli]

* Если у вас еще нет реестра контейнеров, создайте его (требуется Premium `hello-world` SKU) и нажмите образец изображения, например, из Docker Hub. Например, используйте [портал Azure][quickstart-portal] или [Azure CLI][quickstart-cli] для создания реестра. 

* Если вы хотите ограничить доступ к реестру с помощью виртуальной сети в другой подписке Azure, необходимо зарегистрировать поставщика ресурсов для реестра контейнеров Azure в этой подписке. Пример:

  ```azurecli
  az account set --subscription <Name or ID of subscription of virtual network>

  az provider register --namespace Microsoft.ContainerRegistry
  ``` 

## <a name="about-network-rules-for-a-container-registry"></a>О правилах сети для контейнерного реестра

Реестр контейнеров Azure по умолчанию принимает соединения через Интернет от хостов в любой сети. С помощью виртуальной сети можно разрешить только ресурсам Azure, таким как кластер AKS или Azure VM, безопасного доступа к реестру, не пересекая границу сети. Вы также можете настроить правила сетевого брандмауэра, чтобы разрешить только определенные общедоступные диапазоны IP-адресов в Интернете. 

Чтобы ограничить доступ к реестру, сначала измените действие реестра по умолчанию, чтобы оно отрицало все сетевые соединения. Затем добавьте правила доступа к сети. Клиенты, получивдоступные через правила сети, должны продолжать [аутентифицировать проверку в реестре контейнеров](https://docs.microsoft.com/azure/container-registry/container-registry-authentication) и иметь право на доступ к данным.

### <a name="service-endpoint-for-subnets"></a>Конечная точка обслуживания для подсетей

Чтобы обеспечить доступ из подсети в виртуальной сети, необходимо добавить [конечную точку обслуживания](../virtual-network/virtual-network-service-endpoints-overview.md) для службы реестра контейнеров Azure. 

Мультитенантные службы, такие как реестр контейнеров Azure, используют единый набор IP-адресов для всех клиентов. Конечная точка службы назначает конечную точку для доступа к реестру. Эта конечная точка дает трафику оптимальный маршрут к ресурсу по сети магистральных базисов Azure. В каждом запросе также передаются удостоверения виртуальной сети и подсети.

### <a name="firewall-rules"></a>Правила брандмауэра

Для правил IP сети, обеспечить разрешенные диапазоны интернет-адресов с помощью обозначения CIDR, таких как *16.17.18.0/24* или отдельных IP-адресов, как *16.17.18.19*. Правила IP-сети разрешены только для *общедоступных* IP-адресов в Интернете. Диапазоны IP-адресов, зарезервированные для частных сетей (как определено в документе RFC 1918), запрещено использовать в правилах IP.

## <a name="create-a-docker-enabled-virtual-machine"></a>Создание виртуальной машины с поддержкой Docker

Для этой статьи используйте Ubuntu VM с поддержкой Docker для доступа к реестру контейнеров Azure. Чтобы использовать проверку подлинности Active Directory Azure в реестре, также установите [Azure CLI][azure-cli] на VM. Если у вас уже есть виртуальная машина Azure, пропустите этот шаг создания.

Вы можете использовать ту же группу ресурсов для вашей виртуальной машины и реестра контейнеров. Эта настройка упрощает очистку в конце, но не требуется. Если вы решите создать отдельную группу ресурсов для виртуальной машины и виртуальной сети, запустите [группу az.][az-group-create] Следующий пример создает группу ресурсов под названием *myResourceGroup* в *западном* центре:

```azurecli
az group create --name myResourceGroup --location westus
```

Теперь развернуть по умолчанию Ubuntu Azure виртуальную машину с [az vm создать.][az-vm-create] Следующий пример создает VM под названием *myDockerVM:*

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myDockerVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

Создание виртуальной машины может занять несколько минут. После выполнения команды запишите значение `publicIpAddress`, отображаемое Azure CLI. Используйте этот адрес, чтобы сделать SSH соединения с VM, и позже для последующей настройки правил брандмауэра.

### <a name="install-docker-on-the-vm"></a>Установка Docker на виртуальной машине

После запуска виртуальной машины установите SSH-подключение к ней. Замените *publicIpAddress* общедоступным IP-адресом виртуальной машины.

```bash
ssh azureuser@publicIpAddress
```

Запустите следующую команду для установки Docker на Ubuntu VM:

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

Выполните действия, описанные в статье [Установка Azure CLI с помощью apt](/cli/azure/install-azure-cli-apt?view=azure-cli-latest), чтобы установить Azure CLI на виртуальной машине Ubuntu. Для этой статьи убедитесь, что вы установите версию 2.0.58 или позже.

Выйдите из соединения SSH.

## <a name="allow-access-from-a-virtual-network"></a>Разрешить доступ из виртуальной сети

В этом разделе назначьте реестр контейнеров, чтобы обеспечить доступ из подсети в виртуальной сети Azure. Предоставляются эквивалентные шаги с помощью портала Azure CLI и Azure.

### <a name="allow-access-from-a-virtual-network---cli"></a>Разрешить доступ из виртуальной сети - CLI

#### <a name="add-a-service-endpoint-to-a-subnet"></a>Добавление конечная точка службы в подсеть

При создании VM Azure по умолчанию создает виртуальную сеть в одной и той же группе ресурсов. Название виртуальной сети основано на названии виртуальной машины. Например, если вы называете вашу виртуальную машину *myDockerVM*, по умолчанию виртуальное название сети *myDockerVMVNET*, с подсетью под названием *myDockerVMSubnet*. Проверьте это на портале Azure или с помощью команды [списка внет сети az:][az-network-vnet-list]

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

Используйте команду [обновления подсети сети аз-сети,][az-network-vnet-subnet-update] чтобы добавить конечную точку службы **Microsoft.ContainerRegistry** в подсеть. Замените имена виртуальной сети и подсети в следующей команде:

```azurecli
az network vnet subnet update \
  --name myDockerVMSubnet \
  --vnet-name myDockerVMVNET \
  --resource-group myResourceGroup \
  --service-endpoints Microsoft.ContainerRegistry
```

Используйте [команду шоу-шоу сети az network vnet][az-network-vnet-subnet-show] для получения идентификатора ресурсов подсети. Это необходимо на более позднем этапе для настройки правила доступа к сети.

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

#### <a name="change-default-network-access-to-registry"></a>Изменение доступа к сети по умолчанию в реестр

По умолчанию реестр контейнеров Azure позволяет выполнять соединения с хостами в любой сети. Чтобы ограничить доступ к выбранной сети, измените действие по умолчанию, чтобы отказать в доступе. Заменить название вашего реестра в следующей команде [обновления az acr:][az-acr-update]

```azurecli
az acr update --name myContainerRegistry --default-action Deny
```

#### <a name="add-network-rule-to-registry"></a>Добавление сетевого правила в реестр

Используйте команду [добавления сетевого правила az acr][az-acr-network-rule-add] для добавления сетевого правила в свой реестр, который позволяет получить доступ из подсети VM. Заменить имя реестра контейнеров и идентификатор ресурсов подсети в следующей команде: 

 ```azurecli
az acr network-rule add --name mycontainerregistry --subnet <subnet-resource-id>
```

Продолжить [проверку доступа к реестру](#verify-access-to-the-registry).

### <a name="allow-access-from-a-virtual-network---portal"></a>Разрешить доступ из виртуальной сети - портал

#### <a name="add-service-endpoint-to-subnet"></a>Добавление конечная точка обслуживания в подсеть

При создании VM Azure по умолчанию создает виртуальную сеть в одной и той же группе ресурсов. Название виртуальной сети основано на названии виртуальной машины. Например, если вы называете вашу виртуальную машину *myDockerVM*, по умолчанию виртуальное название сети *myDockerVMVNET*, с подсетью под названием *myDockerVMSubnet*.

Чтобы добавить конечную точку обслуживания для реестра контейнеров Azure в подсеть:

1. В поле поиска в верхней части [портала Azure][azure-portal]введите *виртуальные сети.* Когда в результатах поиска появится пункт **Виртуальные сети**, выберите его.
1. Из списка виртуальных сетей выберите виртуальную сеть, в которой развернута виртуальная машина, например *myDockerVMVNET.*
1. Под **настройками**выберите **подсети.**
1. Выберите подсеть, в которой развернута виртуальная машина, например *myDockerVMSubnet.*
1. В **конечных точках службы**выберите **Microsoft.ContainerRegistry**.
1. Нажмите кнопку **Сохранить**.

![Добавление конечная точка обслуживания в подсеть][acr-subnet-service-endpoint] 

#### <a name="configure-network-access-for-registry"></a>Настройка сетевого доступа для реестра

По умолчанию реестр контейнеров Azure позволяет выполнять соединения с хостами в любой сети. Чтобы ограничить доступ к виртуальной сети:

1. На портале перейдите в реестр контейнеров.
1. Под **настройками**выберите **Firewall и виртуальные сети.**
1. Чтобы отказать в доступе по умолчанию, выберите разрешить доступ из **выбранных сетей.** 
1. Выберите **Добавить существующую виртуальную сеть**и выберите виртуальную сеть и подсеть, настроенную с помощью конечной точки службы. Нажмите кнопку **Добавить**.
1. Нажмите кнопку **Сохранить**.

![Настройка виртуальной сети для контейнерного реестра][acr-vnet-portal]

Продолжить [проверку доступа к реестру](#verify-access-to-the-registry).

## <a name="allow-access-from-an-ip-address"></a>Разрешить доступ с IP-адреса

В этом разделе назначьте реестр контейнеров, чтобы обеспечить доступ с определенного IP-адреса или диапазона. Предоставляются эквивалентные шаги с помощью портала Azure CLI и Azure.

### <a name="allow-access-from-an-ip-address---cli"></a>Разрешить доступ с IP-адреса - CLI

#### <a name="change-default-network-access-to-registry"></a>Изменение доступа к сети по умолчанию в реестр

Если вы еще не сделали этого, обновите конфигурацию реестра, чтобы отказать в доступе по умолчанию. Заменить название вашего реестра в следующей команде [обновления az acr:][az-acr-update]

```azurecli
az acr update --name myContainerRegistry --default-action Deny
```

#### <a name="remove-network-rule-from-registry"></a>Удаление сетевого правила из реестра

Если ранее вы добавили сетевое правило, чтобы разрешить доступ из подсети VM, удалите конечную точку службы подсети и правило сети. Заменить имя реестра контейнеров и идентификатор ресурсов подсети, извлеченной на предыдущем этапе в [сетевом правиле az acr, удалить][az-acr-network-rule-remove] команду: 

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

#### <a name="add-network-rule-to-registry"></a>Добавление сетевого правила в реестр

Используйте команду [добавления сетевого правила az acr][az-acr-network-rule-add] для добавления сетевого правила в свой реестр, который позволяет получить доступ с IP-адреса VM. Заменить имя реестра контейнеров и общедоступный IP-адрес VM в следующей команде.

```azurecli
az acr network-rule add --name mycontainerregistry --ip-address <public-IP-address>
```

Продолжить [проверку доступа к реестру](#verify-access-to-the-registry).

### <a name="allow-access-from-an-ip-address---portal"></a>Разрешить доступ с IP-адреса - портал

#### <a name="remove-existing-network-rule-from-registry"></a>Удалить существующее сетевое правило из реестра

Если ранее было добавлено сетевое правило, позволяющее получить доступ из подсети VM, удалите существующее правило. Пропустите этот раздел, если вы хотите получить доступ к реестру из другого VM.

* Обновите настройки подсети, чтобы удалить конечную точку обслуживания подсети для реестра контейнеров Azure. 

  1. На [портале Azure][azure-portal]перейдите к виртуальной сети, где развернута виртуальная машина.
  1. Под **настройками**выберите **подсети.**
  1. Выберите подсеть, где развернута виртуальная машина.
  1. В **конечных точках службы**удалите флажок для **Microsoft.ContainerRegistry**. 
  1. Нажмите кнопку **Сохранить**.

* Удалите сетевое правило, позволяющее подсети получить доступ к реестру.

  1. На портале перейдите в реестр контейнеров.
  1. Под **настройками**выберите **Firewall и виртуальные сети.**
  1. Под **виртуальными сетями**выберите название виртуальной сети, а затем выберите **Удалить.**
  1. Нажмите кнопку **Сохранить**.

#### <a name="add-network-rule-to-registry"></a>Добавление сетевого правила в реестр

1. На портале перейдите в реестр контейнеров.
1. Под **настройками**выберите **Firewall и виртуальные сети.**
1. Если вы еще не сделали этого, выберите разрешить доступ из **выбранных сетей.** 
1. В **виртуальных сетях**убедитесь, что сеть не выбрана.
1. Под **брандмауэром**введите общедоступный IP-адрес VM. Или введите диапазон адресов в обозначении CIDR, содержащем IP-адрес VM.
1. Нажмите кнопку **Сохранить**.

![Настройка правила брандмауэра для реестра контейнеров][acr-vnet-firewall-portal]

Продолжить [проверку доступа к реестру](#verify-access-to-the-registry).

## <a name="verify-access-to-the-registry"></a>Проверить доступ к реестру

Прождав несколько минут обновления конфигурации, убедитесь, что VM может получить доступ к реестру контейнеров. Сделайте соединение SSH с вашим VM и запустите команду [входа в систему az acr,][az-acr-login] чтобы войти в свой реестр. 

```bash
az acr login --name mycontainerregistry
```

Вы можете выполнять операции `docker pull` реестра, такие как запуск, чтобы вытащить образец изображения из реестра. Замените значение изображения и тега, подходящее для вашего реестра, прикреплённых к названию сервера регистрации реестра (все в нижнем регистре):

```bash
docker pull mycontainerregistry.azurecr.io/hello-world:v1
``` 

Docker успешно тянет изображение к VM.

Этот пример показывает, что вы можете получить доступ к реестру частных контейнеров через правило доступа к сети. Однако доступ к реестру не может быть получен из другого узла входа, в который нет настроенного правила доступа к сети. Если вы попытаетесь войти в `az acr login` систему из другого узла с помощью команды или `docker login` команды, вывод аналогичен следующему:

```Console
Error response from daemon: login attempt to https://xxxxxxx.azurecr.io/v2/ failed with status: 403 Forbidden
```

## <a name="restore-default-registry-access"></a>Восстановление доступа к реестру по умолчанию

Чтобы восстановить реестр, чтобы разрешить доступ по умолчанию, удалите все сетевые правила, которые настроены. Затем установите действие по умолчанию, чтобы разрешить доступ. Предоставляются эквивалентные шаги с помощью портала Azure CLI и Azure.

### <a name="restore-default-registry-access---cli"></a>Восстановление доступа к реестру по умолчанию - CLI

#### <a name="remove-network-rules"></a>Удаление сетевых правил

Чтобы просмотреть список сетевых правил, настроенных для вашего реестра, запустите следующую команду [списка сетевых правил az acr:][az-acr-network-rule-list]

```azurecli
az acr network-rule list--name mycontainerregistry 
```

Для каждого настроенного правила выполнить [правило сети acr az acr,][az-acr-network-rule-remove] чтобы удалить его. Пример:

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

Заменить название вашего реестра в следующей команде [обновления az acr:][az-acr-update]
```azurecli
az acr update --name myContainerRegistry --default-action Allow
```

### <a name="restore-default-registry-access---portal"></a>Восстановление доступа к реестру по умолчанию - портал


1. На портале перейдите в реестр контейнеров и выберите **Firewall и виртуальные сети.**
1. Под **виртуальными сетями**выберите каждую виртуальную сеть, а затем выберите **Удалить.**
1. Под **брандмауэром**выберите каждый диапазон адресов, а затем выберите значок Delete.
1. Под **Разрешить доступ от**, выберите все **сети**. 
1. Нажмите кнопку **Сохранить**.

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы создали все ресурсы Azure в одной и той же группе ресурсов и больше не нуждаетесь в них, можно дополнительно удалить ресурсы, используя одну команду [удаления группы аз:](/cli/azure/group)

```azurecli
az group delete --name myResourceGroup
```

Чтобы очистить свои ресурсы на портале, перейдите к группе ресурсов myResourceGroup. После загрузки группы ресурсов нажмите на **группу ресурсов Delete,** чтобы удалить группу ресурсов и ресурсы, хранящиеся там.

## <a name="next-steps"></a>Дальнейшие действия

В этой статье были кратко описаны несколько ресурсов и функций виртуальной сети. Более подробно эти темы рассматриваются в следующей документации по виртуальным сетям Azure:

* [Виртуальная сеть](https://docs.microsoft.com/azure/virtual-network/manage-virtual-network)
* [Подсети](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet)
* [Конечные точки обслуживания](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview)

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
