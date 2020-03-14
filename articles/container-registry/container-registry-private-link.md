---
title: Настройка частной ссылки
description: Настройка частной конечной точки в реестре контейнеров и включение закрытой ссылки в локальной виртуальной сети
ms.topic: article
ms.date: 03/10/2020
ms.openlocfilehash: 57c2a59ad8b16c39c7c577173feae68dcb263277
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79203361"
---
# <a name="configure-azure-private-link-for-an-azure-container-registry"></a>Настройка частной ссылки Azure для реестра контейнеров Azure 

Вы можете настроить [частную конечную точку](../private-link/private-endpoint-overview.md) для реестра контейнеров Azure, чтобы клиенты в виртуальной сети Azure безопасно получать доступ к реестру по [частной ссылке](../private-link/private-link-overview.md). Частная конечная точка использует IP-адрес из адресного пространства виртуальной сети для реестра. Сетевой трафик между клиентами в виртуальной сети и реестре проходит по виртуальной сети и частному каналу в магистральной сети Майкрософт, что устраняет уязвимость в общедоступном Интернете.

Вы можете [настроить параметры DNS](../private-link/private-endpoint-overview.md#dns-configuration) для частной конечной точки, чтобы параметры разрешили доступ к выделенному ЧАСТНОМУ IP-адресу реестра. С помощью конфигурации DNS клиенты и службы в сети могут по-прежнему обращаться к реестру в полном доменном имени реестра, например *myregistry.azurecr.IO*.

Эта функция доступна на уровне службы реестра " **премиум** ". Сведения об уровнях и ограничениях служб реестра см. в статье [номера SKU реестра контейнеров Azure](container-registry-skus.md).

> [!IMPORTANT]
> Эта функция сейчас доступна в предварительной версии, и некоторые [ограничения](#preview-limitations) применяются. Предварительные версии предоставляются при условии, что вы принимаете [дополнительные условия использования][terms-of-use]. Некоторые аспекты этой функции могут быть изменены до выхода общедоступной версии.

## <a name="preview-limitations"></a>Ограничения предварительной версии

* В настоящее время нельзя настроить частную связь с частной конечной точкой в [геореплицированном реестре](container-registry-geo-replication.md). 

## <a name="prerequisites"></a>предварительные требования

* Чтобы использовать Azure CLI действия, описанные в этой статье, рекомендуется Azure CLI версии 2.2.0 или более поздней. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0][azure-cli]. Или запустите в [Azure Cloud Shell](../cloud-shell/quickstart.md).
* Если у вас еще нет реестра контейнеров, создайте его (требуемый уровень Premium) и отправьте пример изображения, например `hello-world` из DOCKER Hub. Например, для создания реестра используйте [портал Azure][quickstart-portal] или [Azure CLI][quickstart-cli] .
* Если вы хотите настроить доступ к реестру с помощью частной ссылки в другой подписке Azure, необходимо зарегистрировать поставщик ресурсов для реестра контейнеров Azure в этой подписке. Пример:

  ```azurecli
  az account set --subscription <Name or ID of subscription of private link>

  az provider register --namespace Microsoft.ContainerRegistry
  ``` 

В Azure CLI примерах в этой статье используются следующие переменные среды. Замените значения, соответствующие вашей среде. Все примеры отформатированы для оболочки bash:

```bash
registryName=<container-registry-name>
registryLocation=<container-registry-location> # Azure region such as westeurope where registry created
resourceGroup=<resource-group-name>
vmName=<virtual-machine-name>
```

## <a name="create-a-docker-enabled-virtual-machine"></a>Создание виртуальной машины с поддержкой DOCKER

Для целей тестирования используйте виртуальную машину Ubuntu с поддержкой DOCKER для доступа к реестру контейнеров Azure. Чтобы использовать Azure Active Directoryную проверку подлинности в реестре, также установите [Azure CLI][azure-cli] на виртуальной машине. Если у вас уже есть виртуальная машина Azure, пропустите этот шаг создания.

Вы можете использовать одну и ту же группу ресурсов для виртуальной машины и реестра контейнеров. Эта настройка упрощает очистку в конце, но не является обязательной. Если вы решили создать отдельную группу ресурсов для виртуальной машины и виртуальной сети, выполните команду [AZ Group Create][az-group-create]:

```azurecli
az group create --name $resourceGroup --location $registryLocation
```

Теперь разверните виртуальную машину Ubuntu Azure по умолчанию с помощью команды [AZ VM Create][az-vm-create]. В следующем примере создается виртуальная машина с именем *myDockerVM*.

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

Выполните следующие команды, чтобы установить DOCKER на виртуальной машине Ubuntu:

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

Выйдите из SSH-подключения.

## <a name="set-up-private-link---cli"></a>Настройка закрытой ссылки — CLI

### <a name="get-network-and-subnet-names"></a>Получение имен сети и подсетей

Если вы еще не сделали этого, вам понадобятся имена виртуальной сети и подсети, чтобы настроить частную ссылку. В этом примере используется одна и та же подсеть для виртуальной машины и закрытой конечной точки реестра. Однако во многих сценариях конечная точка настраивается в отдельной подсети. 

При создании виртуальной машины Azure по умолчанию создает виртуальную сеть в той же группе ресурсов. Имя виртуальной сети зависит от имени виртуальной машины. Например, если вы назначите имя виртуальной машины *myDockerVM*, имя виртуальной сети по умолчанию — *мидоккервмвнет*с подсетью с именем *мидоккервмсубнет*. Задайте эти значения в переменных среды, выполнив команду [AZ Network vnet List][az-network-vnet-list] :

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

### <a name="disable-network-policies-in-subnet"></a>Отключение сетевых политик в подсети

[Отключите сетевые политики](../private-link/disable-private-endpoint-network-policy.md) , такие как группы безопасности сети, в подсети для частной конечной точки. Обновите конфигурацию подсети с помощью команды [AZ Network vnet подсети Update][az-network-vnet-subnet-update]:

```azurecli
az network vnet subnet update \
 --name $subnetName \
 --vnet-name $networkName \
 --resource-group $resourceGroup \
 --disable-private-endpoint-network-policies
```

### <a name="configure-the-private-dns-zone"></a>Настройка частной зоны DNS

Создайте частную зону DNS для домена реестра контейнеров Azure закрытый. На последующих шагах вы создадите записи DNS для домена реестра в этой зоне DNS.

Чтобы использовать частную зону для переопределения разрешения DNS для реестра контейнеров Azure по умолчанию, зона должна называться **privatelink.azurecr.IO**. Выполните команду [AZ Network частный-DNS Zone Create][az-network-private-dns-zone-create] , чтобы создать частную зону:

```azurecli
az network private-dns zone create \
  --resource-group $resourceGroup \
  --name "privatelink.azurecr.io"
```

### <a name="create-an-association-link"></a>Создание связи связи

Выполните команду [AZ Network частный-DNS Link vnet Create][az-network-private-dns-link-vnet-create] , чтобы связать частную зону с виртуальной сетью. В этом примере создается ссылка с именем *миднслинк*.

```azurecli
az network private-dns link vnet create \
  --resource-group $resourceGroup \
  --zone-name "privatelink.azurecr.io" \
  --name MyDNSLink \
  --virtual-network $networkName \
  --registration-enabled false
```

### <a name="create-a-private-registry-endpoint"></a>Создание частной конечной точки реестра

В этом разделе вы создадите закрытую конечную точку реестра в виртуальной сети. Сначала получите идентификатор ресурса реестра:

```azurecli
registryID=$(az acr show --name $registryName \
  --query 'id' --output tsv)
```

Чтобы создать закрытую конечную точку реестра, выполните команду [AZ Network Private-Endpoint Create][az-network-private-endpoint-create] .

В следующем примере создается конечная точка *миприватиндпоинт* и *myConnection*подключения службы. Чтобы указать ресурс реестра контейнеров для конечной точки, передайте `--group-ids registry`:

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

### <a name="get-private-ip-addresses"></a>Получение частных IP-адресов

Выполните команду [AZ Network Private-Endpoint демонстрация][az-network-private-endpoint-show] , чтобы запросить конечную точку для идентификатора сетевого интерфейса:

```azurecli
networkInterfaceID=$(az network private-endpoint show \
  --name myPrivateEndpoint \
  --resource-group $resourceGroup \
  --query 'networkInterfaces[0].id' \
  --output tsv)
```

Связанное с сетевым интерфейсом — два частных IP-адреса для реестра контейнеров: один для самого реестра, а другой для конечной точки данных реестра. Выполните следующую команду [AZ Resource-шоу][az-resource-show] , чтобы получить частные IP-адреса для реестра контейнеров и конечной точки данных реестра.

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

Следующие команды создают записи DNS в частной зоне для конечной точки реестра и ее конечной точки данных. Например, если у вас есть реестр с именем *myregistry* в регионе *westeurope* , то имена конечных точек будут `myregistry.azurecr.io` и `myregistry.westeurope.data.azurecr.io`. 

Сначала выполните команду [AZ Network частный-DNS Record — задайте создание][az-network-private-dns-record-set-a-create] , чтобы создать пустой набор записей для конечной точки реестра и конечной точки данных:

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

Чтобы создать записи A для конечной точки реестра и конечной точки данных, выполните команду [AZ Network частный-DNS Record-Set-Record][az-network-private-dns-record-set-a-add-record] .

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

Теперь частная связь настроена и готова к использованию.

## <a name="set-up-private-link---portal"></a>Настройка закрытой ссылки на портале

В следующих шагах предполагается, что виртуальная сеть и подсеть уже настроены с виртуальной машиной для тестирования. Можно также [создать новую виртуальную сеть и подсеть](../virtual-network/quick-create-portal.md).

### <a name="create-a-private-endpoint"></a>Создание частной конечной точки

1. На портале перейдите к реестру контейнеров.
1. В разделе **Параметры**выберите **частные конечные точки подключения (Предварительная версия)** .
1. Выберите **+ Частная конечная точка**.
1. На вкладке **основные** сведения введите или выберите следующую информацию.

    | Параметр | Значение |
    | ------- | ----- |
    | **Сведения о проекте** | |
    | Subscription | Выберите свою подписку. |
    | группа ресурсов. | Введите имя существующей группы или создайте новую.|
    | **Сведения об экземпляре** |  |
    | Имя | Укажите уникальное имя. |
    |Регион|Выберите регион.|
    |||
5. Выберите **Далее: ресурс**.
6. Введите или выберите следующие сведения:

    | Параметр | Значение |
    | ------- | ----- |
    |Метод подключения  | Выберите **подключиться к ресурсу Azure в моем каталоге**.|
    | Subscription| Выберите свою подписку. |
    | Тип ресурса | Выберите **Microsoft. ContainerRegistry/реестров**. |
    | Ресурс |Выберите имя реестра|
    |Целевой подресурс |Выбор **реестра**|
    |||
7. Выберите **Далее: Конфигурация**.
8. Введите или выберите сведения:

    | Параметр | Значение |
    | ------- | ----- |
    |**Сеть**| |
    | Виртуальная сеть| Выберите виртуальную сеть, в которой развернута виртуальная машина, например *мидоккервмвнет*. |
    | Подсеть | Выберите подсеть, например *мидоккервмсубнет* , где развернута виртуальная машина. |
    |**Интеграция Частная зона DNS**||
    |Интеграция с частной зоной DNS |Выберите **Да**. |
    |Частная зона DNS |Выбор *(новое) privatelink.azurecr.IO* |
    |||

1. Выберите **Review + create** (Просмотреть и создать). Вы будете перенаправлены на страницу **Просмотр и создание**, где Azure проверит вашу конфигурацию. 
2. При появлении сообщения **Проверка пройдена** нажмите кнопку **Создать**.

После создания частной конечной точки параметры DNS в частной зоне отображаются на странице **обзора** конечной точки.

![Параметры DNS конечной точки](./media/container-registry-private-link/private-endpoint-overview.png)

Теперь ваша частная ссылка настроена и готова к использованию.

## <a name="validate-private-link-connection"></a>Проверить подключение к частному каналу

Следует проверить, что ресурсы в подсети частной конечной точки подключаются к реестру по частному IP-адресу и имеют правильную интеграцию частной зоны DNS.

Чтобы проверить подключение к частной ссылке, подключитесь к виртуальной машине по протоколу SSH, которую вы настроили в виртуальной сети.

Выполните команду `nslookup`, чтобы разрешить IP-адрес реестра по частной ссылке:

```bash
nslookup $registryName.azurecr.io
```

Пример выходных данных показывает IP-адрес реестра в адресном пространстве подсети:

```console
[...]
myregistry.azurecr.io       canonical name = myregistry.privatelink.azurecr.io.
Name:   myregistry.privatelink.azurecr.io
Address: 10.0.0.6
```

Сравните этот результат с общедоступным IP-адресом в `nslookup` выходных данных для одного и того же реестра в общедоступной конечной точке:

```console
[...]
Non-authoritative answer:
Name:   myregistry.westeurope.cloudapp.azure.com
Address: 40.78.103.41
```

### <a name="registry-operations-over-private-link"></a>Операции с реестром по закрытой ссылке

Также убедитесь, что вы можете выполнять операции с реестром из виртуальной машины в подсети. Установите SSH-подключение к виртуальной машине и выполните команду [AZ контроля учетных записей][az-acr-login] для входа в реестр. В зависимости от конфигурации виртуальной машины может потребоваться добавить в `sudo`следующие команды.

```bash
az acr login --name $registryName
```

Выполните операции с реестром, например `docker pull`, чтобы извлечь образец образа из реестра. Замените `hello-world:v1` изображением и тегом, соответствующим реестру, с префиксом имени сервера входа в реестр (все строчные):

```bash
docker pull myregistry.azurecr.io/hello-world:v1
``` 

DOCKER успешно запрашивает образ на виртуальной машине.

## <a name="manage-private-endpoint-connections"></a>Управление подключениями к частным конечным точкам

Управление подключениями к частным конечным точкам реестра с помощью портал Azure или с помощью команд в группе команд [AZ контроля доступа Private-Endpoint-Connection][az-acr-private-endpoint-connection] . К операциям относятся утверждение, удаление, перечисление, отклонение или отображение сведений о подключениях к частным конечным точкам реестра.

Например, чтобы получить список подключений к частным конечным точкам реестра, выполните команду [AZ запись закрытая конечная точка-подключение список][az-acr-private-endpoint-connection-list] . Пример:

```azurecli
az acr private-endpoint-connection list \
  --registry-name $registryName 
```

При настройке подключения к частной конечной точке с помощью действий, описанных в этой статье, реестр автоматически принимает подключения от клиентов и служб, имеющих разрешения RBAC для реестра. Можно настроить конечную точку для обязательного утверждения подключений вручную. Сведения о том, как утвердить и отклонять подключения к частным конечным точкам, см. [в разделе Управление подключением к частной конечной точке](../private-link/manage-private-endpoint.md).

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы создали все ресурсы Azure в одной группе ресурсов и больше не нуждаются в них, вы можете при необходимости удалить ресурсы с помощью одной команды [AZ Group Delete](/cli/azure/group) :

```azurecli
az group delete --name $resourceGroup
```

Чтобы очистить ресурсы на портале, перейдите к группе ресурсов. После загрузки группы ресурсов щелкните **Удалить группу ресурсов** , чтобы удалить группу ресурсов и ресурсы, хранящиеся в ней.

## <a name="next-steps"></a>Дальнейшие действия

* Дополнительные сведения о закрытой ссылке см. в документации по [частной ссылке Azure](../private-link/private-link-overview.md) .
* Альтернативой закрытой ссылке является настройка правил доступа к сети для ограничения доступа к реестру. Дополнительные сведения см. в статье [ограничение доступа к реестру контейнеров Azure с помощью виртуальной сети Azure или правил брандмауэра](container-registry-vnet.md).

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
[az-network-private-dns-zone-create]: /cli/azure/network/private-dns-zone/create#az-network-private-dns-zone-create
[az-network-private-dns-link-vnet-create]: /cli/azure/network/private-dns-link/vnet#az-network-private-dns-link-vnet-create
[az-network-private-dns-record-set-a-create]: /cli/azure/network/private-dns-record/set/a#az-network-private-dns-record-set-a-create
[az-network-private-dns-record-set-a-add-record]: /cli/azure/network/private-dns-record/set/a#az-network-private-dns-record-set-a-add-record
[az-resource-show]: /cli/azure/resource#az-resource-show
[quickstart-portal]: container-registry-get-started-portal.md
[quickstart-cli]: container-registry-get-started-azure-cli.md
[azure-portal]: https://portal.azure.com
