---
title: Настройка приватной ссылки
description: Настройка частной конечной точки в реестре контейнеров и включение частной ссылки в локальной виртуальной сети
ms.topic: article
ms.date: 03/10/2020
ms.openlocfilehash: de8228d84497e71f24dba3dd4e6162cb6735a8c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79498917"
---
# <a name="configure-azure-private-link-for-an-azure-container-registry"></a>Наверстойная ссылка Azure Для реестра контейнеров Azure 

Можно настроить [частную конечную точку](../private-link/private-endpoint-overview.md) для контейнерного реестра Azure, чтобы клиенты виртуальной сети Azure могли получить доступ к реестру по [частной ссылке.](../private-link/private-link-overview.md) Частная конечная точка использует IP-адрес из виртуального сетевого адреса для вашего реестра. Сетевой трафик между клиентами в виртуальной сети и реестра пересекает виртуальную сеть и частную ссылку на магистральную сеть Microsoft, исключая воздействие из публичного интернета.

Настройку [DNS](../private-link/private-endpoint-overview.md#dns-configuration) можно настроить для вашей частной конечной точки, чтобы настройки разрешались с выделенным частным IP-адресом реестра. С конфигурацией DNS клиенты и службы в сети могут продолжать получать доступ к реестру в полностью квалифицированном доменном имени реестра, например, *myregistry.azurecr.io*.

Эта функция доступна в сервисе реестра контейнеров **Premium.** Для получения информации о уровнях [Azure Container Registry SKUs](container-registry-skus.md)и ограничениях службы реестра см.

> [!IMPORTANT]
> Эта функция в настоящее время находится в предварительном просмотре, и некоторые [ограничения](#preview-limitations) применяются. Предварительные версии предоставляются при условии, что вы принимаете [дополнительные условия использования][terms-of-use]. Некоторые аспекты этой функции могут быть изменены до выхода общедоступной версии.

## <a name="preview-limitations"></a>Ограничения предварительной версии

* В настоящее время вы не можете настроить частную связь с частной конечной точкой в [гео-реплицируемом реестре.](container-registry-geo-replication.md) 

## <a name="prerequisites"></a>Предварительные требования

* Для использования шагов Azure CLI в этой статье рекомендуется версия Azure CLI 2.2.0 или позже. Если вам нужно установить или обновить, [см.][azure-cli] Или запускать в [Облачной оболочке Azure](../cloud-shell/quickstart.md).
* Если у вас еще нет реестра контейнеров, создайте один (требуется `hello-world` уровень Premium) и нажмите образец изображения, например, из Docker Hub. Например, используйте [портал Azure][quickstart-portal] или [Azure CLI][quickstart-cli] для создания реестра.
* Если вы хотите настроить доступ к реестру с помощью частной ссылки в другой подписке Azure, вам необходимо зарегистрировать поставщика ресурсов для реестра контейнеров Azure в этой подписке. Пример:

  ```azurecli
  az account set --subscription <Name or ID of subscription of private link>

  az provider register --namespace Microsoft.ContainerRegistry
  ``` 

На примерах Azure CLI в этой статье используются следующие переменные среды. Замените значения, подходящие для среды. Все примеры отформатированы для оболочки Bash:

```bash
registryName=<container-registry-name>
registryLocation=<container-registry-location> # Azure region such as westeurope where registry created
resourceGroup=<resource-group-name>
vmName=<virtual-machine-name>
```

## <a name="create-a-docker-enabled-virtual-machine"></a>Создание виртуальной машины с поддержкой Docker

Для целей тестирования используйте Ubuntu VM с поддержкой Docker для доступа к реестру контейнеров Azure. Чтобы использовать проверку подлинности Active Directory Azure в реестре, также установите [Azure CLI][azure-cli] на VM. Если у вас уже есть виртуальная машина Azure, пропустите этот шаг создания.

Вы можете использовать ту же группу ресурсов для вашей виртуальной машины и реестра контейнеров. Эта настройка упрощает очистку в конце, но не требуется. Если вы решите создать отдельную группу ресурсов для виртуальной машины и виртуальной сети, запустите [группу az create:][az-group-create]

```azurecli
az group create --name $resourceGroup --location $registryLocation
```

Теперь развернуть по умолчанию Ubuntu Azure виртуальную машину с [az vm создать.][az-vm-create] Следующий пример создает VM под названием *myDockerVM*.

```azurecli
az vm create \
  --resource-group $resourceGroup \
  --name $vmName \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

Создание виртуальной машины может занять несколько минут. После выполнения команды запишите значение `publicIpAddress`, отображаемое Azure CLI. Этот адрес используется для установления SSH-подключений к виртуальной машине.

### <a name="install-docker-on-the-vm"></a>Установка Docker на виртуальной машине

После запуска виртуальной машины установите SSH-подключение к ней. Замените *publicIpAddress* общедоступным IP-адресом виртуальной машины.

```bash
ssh azureuser@publicIpAddress
```

Запустите следующие команды для установки Docker на Ubuntu VM:

```bash
sudo apt-get update
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

Выполните действия, описанные в статье [Установка Azure CLI с помощью apt](/cli/azure/install-azure-cli-apt?view=azure-cli-latest), чтобы установить Azure CLI на виртуальной машине Ubuntu. Пример:

```bash
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
```

Выйдите из соединения SSH.

## <a name="set-up-private-link---cli"></a>Настройка частной ссылки - CLI

### <a name="get-network-and-subnet-names"></a>Получить имена сетей и подсетей

Если у вас их уже нет, вам понадобятся имена виртуальной сети и подсети для настройки частной ссылки. В этом примере используется та же подсеть для VM и частной конечной точки реестра. Однако во многих сценариях вы бы создали конечную точку в отдельной подсети. 

При создании VM Azure по умолчанию создает виртуальную сеть в одной и той же группе ресурсов. Название виртуальной сети основано на названии виртуальной машины. Например, если вы называете вашу виртуальную машину *myDockerVM*, по умолчанию виртуальное название сети *myDockerVMVNET*, с подсетью под названием *myDockerVMSubnet*. Установите эти значения в переменных среды, запустив команду [списка всетий аз:][az-network-vnet-list]

```azurecli
networkName=$(az network vnet list \
  --resource-group $resourceGroup \
  --query '[].{Name: name}' --output tsv)

subnetName=$(az network vnet list \
  --resource-group $resourceGroup \
  --query '[].{Subnet: subnets[0].name}' --output tsv)

echo networkName=$networkName
echo subnetName=$subnetName
```

### <a name="disable-network-policies-in-subnet"></a>Отключить сетевые политики в подсети

[Отключить сетевые политики,](../private-link/disable-private-endpoint-network-policy.md) такие как группы сетевой безопасности в подсети для частной конечных точек. Обновление конфигурации подсети с [обновлением подсети сети az:][az-network-vnet-subnet-update]

```azurecli
az network vnet subnet update \
 --name $subnetName \
 --vnet-name $networkName \
 --resource-group $resourceGroup \
 --disable-private-endpoint-network-policies
```

### <a name="configure-the-private-dns-zone"></a>Настройка частной зоны DNS

Создайте частную зону DNS для домена реестра контейнеров Azure. В последующих шагах вы создаете записи DNS для вашего домена реестра в этой зоне DNS.

Чтобы использовать частную зону для отмены разрешения DNS по умолчанию для контейнерного реестра Azure, зона должна быть названа **privatelink.azurecr.io.** Выполнить следующую [зону частных зон az для создания][az-network-private-dns-zone-create] команды для создания частной зоны:

```azurecli
az network private-dns zone create \
  --resource-group $resourceGroup \
  --name "privatelink.azurecr.io"
```

### <a name="create-an-association-link"></a>Создание связующее звено

Выполнить [аз сети частных-Dns ссылка vpn создать][az-network-private-dns-link-vnet-create] связать вашу частную зону с виртуальной сети. Этот пример создает ссылку под названием *myDNSLink*.

```azurecli
az network private-dns link vnet create \
  --resource-group $resourceGroup \
  --zone-name "privatelink.azurecr.io" \
  --name MyDNSLink \
  --virtual-network $networkName \
  --registration-enabled false
```

### <a name="create-a-private-registry-endpoint"></a>Создание частной конечной точки реестра

В этом разделе создайте частную конечную точку реестра в виртуальной сети. Во-первых, получить идентификатор ресурса вашего реестра:

```azurecli
registryID=$(az acr show --name $registryName \
  --query 'id' --output tsv)
```

Выполнить частную [конечную точку сети az][az-network-private-endpoint-create] для создания частной конечной точки реестра.

Следующий пример создает конечную точку *myPrivateEndpoint* и подключение к сервису *myConnection*. Чтобы указать ресурс реестра контейнеров `--group-ids registry`для конечных точек, пройдите:

```azurecli
az network private-endpoint create \
    --name myPrivateEndpoint \
    --resource-group $resourceGroup \
    --vnet-name $networkName \
    --subnet $subnetName \
    --private-connection-resource-id $registryID \
    --group-ids registry \
    --connection-name myConnection
```

### <a name="get-private-ip-addresses"></a>Получить частные IP-адреса

Выполнить [атакжес сети частной точки конца шоу][az-network-private-endpoint-show] для запроса конечная точка для идентификатора сетевого интерфейса:

```azurecli
networkInterfaceID=$(az network private-endpoint show \
  --name myPrivateEndpoint \
  --resource-group $resourceGroup \
  --query 'networkInterfaces[0].id' \
  --output tsv)
```

С сетевым интерфейсом связаны два частных IP-адреса для реестра контейнеров: один для самого реестра и один для конечных точек данных реестра. Выполнить следующие команды [анализа ресурсов az,][az-resource-show] чтобы получить частные IP-адреса для реестра контейнеров и конечную точку данных реестра:

```azurecli
privateIP=$(az resource show \
  --ids $networkInterfaceID \
  --api-version 2019-04-01 --query 'properties.ipConfigurations[1].properties.privateIPAddress' \
  --output tsv)

dataEndpointPrivateIP=$(az resource show \
  --ids $networkInterfaceID \
  --api-version 2019-04-01 \
  --query 'properties.ipConfigurations[0].properties.privateIPAddress' \
  --output tsv)
```

### <a name="create-dns-records-in-the-private-zone"></a>Создание записей DNS в частной зоне

Следующие команды создают записи DNS в частной зоне для конечной точки реестра и точки ее данных. Например, если у вас есть реестр под названием *myregistry* `myregistry.azurecr.io` в `myregistry.westeurope.data.azurecr.io`регионе *Западной Европы,* конечные имена и . 

Первый запуск [сети частных-DNs запись создания][az-network-private-dns-record-set-a-create] для создания пустых наборов записей для конечных точек реестра и данных конечная точка:

```azurecli
az network private-dns record-set a create \
  --name $registryName \
  --zone-name privatelink.azurecr.io \
  --resource-group $resourceGroup

# Specify registry region in data endpoint name
az network private-dns record-set a create \
  --name ${registryName}.${registryLocation}.data \
  --zone-name privatelink.azurecr.io \
  --resource-group $resourceGroup
```

Запустите систему [аз-конфиденциально-запись, устанавливаемую команду надписей][az-network-private-dns-record-set-a-add-record] для создания записей A для конечных точек реестра и конечных точек данных:

```azurecli
az network private-dns record-set a add-record \
  --record-set-name $registryName \
  --zone-name privatelink.azurecr.io \
  --resource-group $resourceGroup \
  --ipv4-address $privateIP

# Specify registry region in data endpoint name
az network private-dns record-set a add-record \
  --record-set-name ${registryName}.${registryLocation}.data \
  --zone-name privatelink.azurecr.io \
  --resource-group $resourceGroup \
  --ipv4-address $dataEndpointPrivateIP
```

Частная ссылка теперь настроена и готова к использованию.

## <a name="set-up-private-link---portal"></a>Настройка частной ссылки - портал

Следующие шаги предполагают, что у вас уже есть виртуальная сеть и подсеть, настроенная с VM для тестирования. Вы также можете [создать новую виртуальную сеть и подсеть.](../virtual-network/quick-create-portal.md)

### <a name="create-a-private-endpoint"></a>Создание частной конечной точки

1. На портале перейдите в реестр контейнеров.
1. В **настройках**выберите **приватные соединения конечных точек (Preview).**
1. Выберите **частную конечную точку**.
1. Во вкладке **«Основы»** введите или выберите следующую информацию:

    | Параметр | Значение |
    | ------- | ----- |
    | **Детали проекта** | |
    | Подписка | Выберите свою подписку. |
    | Группа ресурсов | Введите имя существующей группы или создайте новую.|
    | **Детали инстанции** |  |
    | name | Укажите уникальное имя. |
    |Регион|Выберите регион.|
    |||
5. Выберите **далее: Ресурс**.
6. Введите или выберите следующую информацию:

    | Параметр | Значение |
    | ------- | ----- |
    |Метод подключения  | Выберите **Подключение к ресурсу Azure в моем каталоге.**|
    | Подписка| Выберите свою подписку. |
    | Тип ресурса | Выберите **Microsoft.ContainerRegistry/регистраторы.** |
    | Ресурс |Выберите название реестра|
    |Целевой субресурс |Выберите **реестр**|
    |||
7. Выберите **далее: Конфигурация**.
8. Введите или выберите информацию:

    | Параметр | Значение |
    | ------- | ----- |
    |**Сеть**| |
    | Виртуальная сеть| Выберите виртуальную сеть, в которой развернута виртуальная машина, например *myDockerVMVNET.* |
    | Подсеть | Выберите подсеть, например *myDockerVMSubnet,* где развернута виртуальная машина. |
    |**Частная интеграция DNS**||
    |Интеграция с частной зоной DNS |Нажмите кнопку **Да**. |
    |Частная зона DNS |Выберите *(Новый) privatelink.azurecr.io* |
    |||

1. Выберите **Review + create** (Просмотреть и создать). Вы будете перенаправлены на страницу **Просмотр и создание**, где Azure проверит вашу конфигурацию. 
2. При походе пройденое сообщение **«Проверка»** выберите **«Создать».**

После создания частной конечный точки настройки DNS в частной зоне отображаются на странице **обзора** конечных точек.

![Настройки конечных точек DNS](./media/container-registry-private-link/private-endpoint-overview.png)

Ваша приватная ссылка теперь настроена и готова к использованию.

## <a name="validate-private-link-connection"></a>Проверка частного соединения ссылки

Необходимо проверить, что ресурсы в подсети частной конечной точки подключаются к вашему реестру через частный IP-адрес, и иметь правильную частную интеграцию зоны DNS.

Для проверки частного соединения ссылки SSH на виртуальную машину, настроенную в виртуальной сети.

Запустите `nslookup` команду для решения IP-адреса вашего реестра по частной ссылке:

```bash
nslookup $registryName.azurecr.io
```

Пример вывода показывает IP-адрес реестра в адресном пространстве подсети:

```console
[...]
myregistry.azurecr.io       canonical name = myregistry.privatelink.azurecr.io.
Name:   myregistry.privatelink.azurecr.io
Address: 10.0.0.6
```

Сравните этот результат с общедоступным IP-адресом в `nslookup` выходе для одного и того же реестра через публичную конечную точку:

```console
[...]
Non-authoritative answer:
Name:   myregistry.westeurope.cloudapp.azure.com
Address: 40.78.103.41
```

### <a name="registry-operations-over-private-link"></a>Операции по реестру по частной ссылке

Также убедитесь, что вы можете выполнять операции реестра с виртуальной машины в подсети. Сделайте SSH соединение с вашей виртуальной машиной, и запустите [ваз acr логин][az-acr-login] для входа в свой реестр. В зависимости от конфигурации VM может потребоваться префиксировать следующие команды с `sudo`помощью.

```bash
az acr login --name $registryName
```

Выполняйте операции `docker pull` реестра, такие как извлечение образец изображения из реестра. Замените `hello-world:v1` изображение и тег, подходящий для вашего реестра, прикреплённых с именем сервера входа в реестр (все в нижнем регистре):

```bash
docker pull myregistry.azurecr.io/hello-world:v1
``` 

Docker успешно тянет изображение к VM.

## <a name="manage-private-endpoint-connections"></a>Управление частными соединениями конечных точек

Управление соединениями частной конечных точек реестра с помощью портала Azure или с помощью команд в группе командных данных [az acr private-end-point-connection.][az-acr-private-endpoint-connection] Операции включают утверждение, удаление, список, отклонение или отображение сведений о личных соединениях конечной точки реестра.

Например, чтобы перечислить приватные соединения конечной точки реестра, запустите команду списка закрытых конечных [соединений az acr.][az-acr-private-endpoint-connection-list] Пример:

```azurecli
az acr private-endpoint-connection list \
  --registry-name $registryName 
```

При настройке частного соединения конечных точек с помощью шагов в этой статье реестр автоматически принимает соединения от клиентов и служб, которые имеют разрешения RBAC в реестре. Можно настроить конечную точку, требуя ручного утверждения соединений. Для получения информации о том, как утвердить и отклонить частные соединения конечных точек, [см.](../private-link/manage-private-endpoint.md)

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы создали все ресурсы Azure в одной и той же группе ресурсов и больше не нуждаетесь в них, можно дополнительно удалить ресурсы, используя одну команду [удаления группы аз:](/cli/azure/group)

```azurecli
az group delete --name $resourceGroup
```

Чтобы очистить свои ресурсы на портале, перейдите к группе ресурсов. После загрузки группы ресурсов нажмите на **группу ресурсов Delete,** чтобы удалить группу ресурсов и ресурсы, хранящиеся там.

## <a name="next-steps"></a>Дальнейшие действия

* Чтобы узнать больше о Private Link, смотрите документацию [Azure Private Link.](../private-link/private-link-overview.md)
* Альтернативой частной ссылке является создание правил доступа к сети для ограничения доступа в реестр. Чтобы узнать больше, [см. Ограничить доступ к реестру контейнеров Azure с помощью виртуальной сети Azure или правил брандмауэра.](container-registry-vnet.md)

<!-- LINKS - external -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms
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
[az-acr-private-endpoint-connection]: /cli/azure/acr/private-endpoint-connection
[az-acr-private-endpoint-connection-list]: /cli/azure/acr/private-endpoint-connection#az-acr-private-endpoint-connection-list
[az-acr-private-endpoint-connection-approve]: /cli/azure/acr/private-endpoint-connection#az-acr-private-endpoint-connection-approve
[az-group-create]: /cli/azure/group
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-vm-create]: /cli/azure/vm#az-vm-create
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet/#az-network-vnet-subnet-show
[az-network-vnet-subnet-update]: /cli/azure/network/vnet/subnet/#az-network-vnet-subnet-update
[az-network-vnet-list]: /cli/azure/network/vnet/#az-network-vnet-list
[az-network-private-endpoint-create]: /cli/azure/network/private-endpoint#az-network-private-endpoint-create
[az-network-private-endpoint-show]: /cli/azure/network/private-endpoint#az-network-private-endpoint-show
[az-network-private-dns-zone-create]: /cli/azure/network/private-dns/zone#az-network-private-dns-zone-create
[az-network-private-dns-link-vnet-create]: /cli/azure/network/private-dns/link/vnet#az-network-private-dns-link-vnet-create
[az-network-private-dns-record-set-a-create]: /cli/azure/network/private-dns/record-set/a#az-network-private-dns-record-set-a-create
[az-network-private-dns-record-set-a-add-record]: /cli/azure/network/private-dns/record-set/a#az-network-private-dns-record-set-a-add-record
[az-resource-show]: /cli/azure/resource#az-resource-show
[quickstart-portal]: container-registry-get-started-portal.md
[quickstart-cli]: container-registry-get-started-azure-cli.md
[azure-portal]: https://portal.azure.com
