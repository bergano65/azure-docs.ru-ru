---
title: Настройка частной ссылки
description: Настройка частной конечной точки в реестре контейнеров и предоставление доступа по частной ссылке в локальной виртуальной сети
ms.topic: article
ms.date: 05/07/2020
ms.openlocfilehash: 46ec816d85a528fd3208026ef76dff8470154767
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/08/2020
ms.locfileid: "82982459"
---
# <a name="configure-azure-private-link-for-an-azure-container-registry"></a>Настройка частной ссылки Azure для реестра контейнеров Azure 

Ограничьте доступ к реестру, назначив частные IP-адреса виртуальной сети конечным точкам реестра, используя [частную связь Azure](../private-link/private-link-overview.md). Сетевой трафик между клиентами в виртуальной сети и реестре проходит по виртуальной сети и частному каналу в магистральной сети Майкрософт, что устраняет уязвимость в общедоступном Интернете.

Вы можете [настроить параметры DNS](../private-link/private-endpoint-overview.md#dns-configuration) для частной конечной точки, чтобы параметры разрешили доступ к выделенному ЧАСТНОМУ IP-адресу реестра. С помощью конфигурации DNS клиенты и службы в сети могут по-прежнему обращаться к реестру в полном доменном имени реестра, например *myregistry.azurecr.IO*.

Эта функция доступна на уровне службы реестра " **премиум** ". Сведения об уровнях и ограничениях служб реестра см. в статье [уровни реестра контейнеров Azure](container-registry-skus.md).

## <a name="things-to-know"></a>Полезная информация

* В настоящее время сканирование изображений с помощью центра безопасности Azure недоступно в реестре, настроенном с помощью частной конечной точки.

## <a name="prerequisites"></a>Предварительные условия

* Чтобы использовать Azure CLI действия, описанные в этой статье, рекомендуется Azure CLI версии 2.2.0 или более поздней. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0][azure-cli]. Или запустите в [Azure Cloud Shell](../cloud-shell/quickstart.md).
* Если у вас еще нет реестра контейнеров, создайте его (требуемый уровень Premium) и [импортируйте](container-registry-import-images.md) пример изображения, например `hello-world` из DOCKER Hub. Например, для создания реестра используйте [портал Azure][quickstart-portal] или [Azure CLI][quickstart-cli] .
* Чтобы настроить доступ к реестру с помощью частной ссылки в другой подписке Azure, необходимо зарегистрировать поставщик ресурсов для реестра контейнеров Azure в этой подписке. Пример:

  ```azurecli
  az account set --subscription <Name or ID of subscription of private link>

  az provider register --namespace Microsoft.ContainerRegistry
  ``` 

В Azure CLI примерах в этой статье используются следующие переменные среды. Замените значения, соответствующие вашей среде. Все примеры отформатированы для оболочки bash:

```bash
REGISTRY_NAME=<container-registry-name>
REGISTRY_LOCATION=<container-registry-location> # Azure region such as westeurope where registry created
RESOURCE_GROUP=<resource-group-name>
VM_NAME=<virtual-machine-name>
```

[!INCLUDE [Set up Docker-enabled VM](../../includes/container-registry-docker-vm-setup.md)]

## <a name="set-up-private-link---cli"></a>Настройка закрытой ссылки — CLI

### <a name="get-network-and-subnet-names"></a>Получение имен сети и подсетей

Если вы еще не сделали этого, вам понадобятся имена виртуальной сети и подсети, чтобы настроить частную ссылку. В этом примере используется одна и та же подсеть для виртуальной машины и закрытой конечной точки реестра. Однако во многих сценариях конечная точка настраивается в отдельной подсети. 

При создании виртуальной машины Azure по умолчанию создает виртуальную сеть в той же группе ресурсов. Имя виртуальной сети зависит от имени виртуальной машины. Например, если вы назначите имя виртуальной машины *myDockerVM*, имя виртуальной сети по умолчанию — *мидоккервмвнет*с подсетью с именем *мидоккервмсубнет*. Задайте эти значения в переменных среды, выполнив команду [AZ Network vnet List][az-network-vnet-list] :

```azurecli
NETWORK_NAME=$(az network vnet list \
  --resource-group $RESOURCE_GROUP \
  --query '[].{Name: name}' --output tsv)

SUBNET_NAME=$(az network vnet list \
  --resource-group $RESOURCE_GROUP \
  --query '[].{Subnet: subnets[0].name}' --output tsv)

echo NETWORK_NAME=$NETWORK_NAME
echo SUBNET_NAME=$SUBNET_NAME
```

### <a name="disable-network-policies-in-subnet"></a>Отключение сетевых политик в подсети

[Отключите сетевые политики](../private-link/disable-private-endpoint-network-policy.md) , такие как группы безопасности сети, в подсети для частной конечной точки. Обновите конфигурацию подсети с помощью команды [AZ Network vnet подсети Update][az-network-vnet-subnet-update]:

```azurecli
az network vnet subnet update \
 --name $SUBNET_NAME \
 --vnet-name $NETWORK_NAME \
 --resource-group $RESOURCE_GROUP \
 --disable-private-endpoint-network-policies
```

### <a name="configure-the-private-dns-zone"></a>Настройка частной зоны DNS

Создайте закрытую зону DNS для закрытого домена реестра контейнеров Azure. На последующих шагах вы создадите записи DNS для вашего домена реестра в этой зоне DNS.

Чтобы использовать частную зону для переопределения разрешения DNS для реестра контейнеров Azure по умолчанию, зона должна называться **privatelink.azurecr.IO**. Выполните команду [AZ Network частный-DNS Zone Create][az-network-private-dns-zone-create] , чтобы создать частную зону:

```azurecli
az network private-dns zone create \
  --resource-group $RESOURCE_GROUP \
  --name "privatelink.azurecr.io"
```

### <a name="create-an-association-link"></a>Создание связи связи

Выполните команду [AZ Network частный-DNS Link vnet Create][az-network-private-dns-link-vnet-create] , чтобы связать частную зону с виртуальной сетью. В этом примере создается ссылка с именем *миднслинк*.

```azurecli
az network private-dns link vnet create \
  --resource-group $RESOURCE_GROUP \
  --zone-name "privatelink.azurecr.io" \
  --name MyDNSLink \
  --virtual-network $NETWORK_NAME \
  --registration-enabled false
```

### <a name="create-a-private-registry-endpoint"></a>Создание частной конечной точки реестра

В этом разделе вы создадите закрытую конечную точку реестра в виртуальной сети. Сначала получите идентификатор ресурса реестра:

```azurecli
REGISTRY_ID=$(az acr show --name $REGISTRY_NAME \
  --query 'id' --output tsv)
```

Чтобы создать закрытую конечную точку реестра, выполните команду [AZ Network Private-Endpoint Create][az-network-private-endpoint-create] .

В следующем примере создается конечная точка *миприватиндпоинт* и *myConnection*подключения службы. Чтобы указать ресурс реестра контейнеров для конечной точки, передайте `--group-ids registry`:

```azurecli
az network private-endpoint create \
    --name myPrivateEndpoint \
    --resource-group $RESOURCE_GROUP \
    --vnet-name $NETWORK_NAME \
    --subnet $SUBNET_NAME \
    --private-connection-resource-id $REGISTRY_ID \
    --group-ids registry \
    --connection-name myConnection
```

### <a name="get-private-ip-addresses"></a>Получение частных IP-адресов

Выполните команду [AZ Network Private-Endpoint демонстрация][az-network-private-endpoint-show] , чтобы запросить конечную точку для идентификатора сетевого интерфейса:

```azurecli
NETWORK_INTERFACE_ID=$(az network private-endpoint show \
  --name myPrivateEndpoint \
  --resource-group $RESOURCE_GROUP \
  --query 'networkInterfaces[0].id' \
  --output tsv)
```

В этом примере, связанном с сетевым интерфейсом, есть два частных IP-адреса для реестра контейнеров: один для самого реестра, а другой для конечной точки данных реестра. Следующие команды [AZ Resource-показывать][az-resource-show] получают частные IP-адреса для реестра контейнеров и конечной точки данных реестра.

```azurecli
PRIVATE_IP=$(az resource show \
  --ids $NETWORK_INTERFACE_ID \
  --api-version 2019-04-01 \
  --query 'properties.ipConfigurations[1].properties.privateIPAddress' \
  --output tsv)

DATA_ENDPOINT_PRIVATE_IP=$(az resource show \
  --ids $NETWORK_INTERFACE_ID \
  --api-version 2019-04-01 \
  --query 'properties.ipConfigurations[0].properties.privateIPAddress' \
  --output tsv)
```

> [!NOTE]
> Если реестр является [географически реплицируемым](container-registry-geo-replication.md), запросите дополнительные конечные точки данных для каждой реплики реестра.

### <a name="create-dns-records-in-the-private-zone"></a>Создание записей DNS в частной зоне

Следующие команды создают записи DNS в частной зоне для конечной точки реестра и ее конечной точки данных. Например, если у вас есть реестр с именем *myregistry* в регионе *westeurope* , то имена конечных точек `myregistry.azurecr.io` будут `myregistry.westeurope.data.azurecr.io`иметь значение и. 

> [!NOTE]
> Если реестр является [географически реплицируемым](container-registry-geo-replication.md), создайте записи DNS дополнительные для IP-адреса конечной точки данных каждой реплики.

Сначала выполните команду [AZ Network частный-DNS Record — задайте создание][az-network-private-dns-record-set-a-create] , чтобы создать пустой набор записей для конечной точки реестра и конечной точки данных:

```azurecli
az network private-dns record-set a create \
  --name $REGISTRY_NAME \
  --zone-name privatelink.azurecr.io \
  --resource-group $RESOURCE_GROUP

# Specify registry region in data endpoint name
az network private-dns record-set a create \
  --name ${REGISTRY_NAME}.${REGISTRY_LOCATION}.data \
  --zone-name privatelink.azurecr.io \
  --resource-group $RESOURCE_GROUP
```

Чтобы создать записи A для конечной точки реестра и конечной точки данных, выполните команду [AZ Network частный-DNS Record-Set-Record][az-network-private-dns-record-set-a-add-record] .

```azurecli
az network private-dns record-set a add-record \
  --record-set-name $REGISTRY_NAME \
  --zone-name privatelink.azurecr.io \
  --resource-group $RESOURCE_GROUP \
  --ipv4-address $PRIVATE_IP

# Specify registry region in data endpoint name
az network private-dns record-set a add-record \
  --record-set-name ${REGISTRY_NAME}.${REGISTRY_LOCATION}.data \
  --zone-name privatelink.azurecr.io \
  --resource-group $RESOURCE_GROUP \
  --ipv4-address $DATA_ENDPOINT_PRIVATE_IP
```

Теперь частная связь настроена и готова к использованию.

## <a name="set-up-private-link---portal"></a>Настройка закрытой ссылки на портале

Настройте частную ссылку при создании реестра или добавьте закрытую ссылку на существующий реестр. В следующих шагах предполагается, что виртуальная сеть и подсеть уже настроены с виртуальной машиной для тестирования. Можно также [создать новую виртуальную сеть и подсеть](../virtual-network/quick-create-portal.md).

### <a name="create-a-private-endpoint---new-registry"></a>Создание частной конечной точки — новый реестр

1. При создании реестра на портале на вкладке **Основные сведения** в поле **SKU**выберите **Premium**.
1. Перейдите на вкладку **сети** .
1. В окне **сетевое подключение**выберите **Частная конечная точка** > **+ Добавить**.
1. Введите или выберите следующие сведения:

    | Параметр | Значение |
    | ------- | ----- |
    | Подписка | Выберите свою подписку. |
    | Группа ресурсов | Введите имя существующей группы или создайте новую.|
    | Название | Укажите уникальное имя. |
    | Подресурс |Выбор **реестра**|
    | **Сеть** | |
    | Виртуальная сеть| Выберите виртуальную сеть, в которой развернута виртуальная машина, например *мидоккервмвнет*. |
    | Подсеть | Выберите подсеть, например *мидоккервмсубнет* , где развернута виртуальная машина. |
    |**Интеграция Частная зона DNS**||
    |Интеграция с частной зоной DNS |Выберите **Да**. |
    |Частная зона DNS |Выбор *(новое) privatelink.azurecr.IO* |
    |||
1. Настройте оставшиеся параметры реестра и нажмите кнопку **проверить и создать**.

  ![Создание реестра с закрытой конечной точкой](./media/container-registry-private-link/private-link-create-portal.png)

### <a name="create-a-private-endpoint---existing-registry"></a>Создание частной конечной точки — существующий реестр

1. На портале перейдите к реестру контейнеров.
1. В разделе **Параметры**выберите **сеть**.
1. На вкладке **частные конечные точки** выберите **+ Частная конечная точка**.
1. На вкладке **основные** сведения введите или выберите следующую информацию.

    | Параметр | Значение |
    | ------- | ----- |
    | **Сведения о проекте** | |
    | Подписка | Выберите свою подписку. |
    | Группа ресурсов | Введите имя существующей группы или создайте новую.|
    | **Сведения об экземпляре** |  |
    | Название | Введите имя. |
    |Регион|Выберите регион.|
    |||
5. По завершении выберите **Next: Ресурс**.
6. Введите или выберите следующие сведения:

    | Параметр | Значение |
    | ------- | ----- |
    |Метод подключения  | Выберите **подключиться к ресурсу Azure в моем каталоге**.|
    | Подписка| Выберите свою подписку. |
    | Тип ресурса | Выберите **Microsoft. ContainerRegistry/реестров**. |
    | Ресурс |Выберите имя реестра|
    |Целевой подресурс |Выбор **реестра**|
    |||
7. По завершении выберите **Next: Конфигурация**.
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

1. Выберите **Review + create** (Просмотреть и создать). Вы будете перенаправлены на страницу **Просмотр и создание**, где Azure проверит вашу конфигурацию. 
2. При появлении сообщения **Проверка пройдена** нажмите кнопку **Создать**.

После создания частной конечной точки параметры DNS в частной зоне отображаются на странице **частные конечные точки** на портале:

1. На портале перейдите к реестру контейнеров и выберите **параметры > сети**.
1. На вкладке **частные конечные точки** выберите созданную закрытую конечную точку.
1. На странице **Обзор** проверьте параметры ссылки и пользовательские параметры DNS.

  ![Параметры DNS конечной точки](./media/container-registry-private-link/private-endpoint-overview.png)

Теперь ваша частная ссылка настроена и готова к использованию.

## <a name="disable-public-access"></a>Отключить общий доступ

Во многих случаях отключите доступ к реестру из общедоступных сетей. Эта конфигурация предотвращает достижение клиентами за пределами виртуальной сети конечных точек реестра. Чтобы отключить открытый доступ с помощью портала, выполните следующие действия.

1. На портале перейдите к реестру контейнеров и выберите **параметры > сети**.
1. На вкладке **общий доступ** в параметре **Разрешить общий доступ**выберите значение **отключено**. Затем нажмите кнопку **Save** (Сохранить).

## <a name="validate-private-link-connection"></a>Проверить подключение к частному каналу

Следует проверить, что ресурсы в подсети частной конечной точки подключаются к реестру по частному IP-адресу и имеют правильную интеграцию частной зоны DNS.

Чтобы проверить подключение к частной ссылке, подключитесь к виртуальной машине по протоколу SSH, которую вы настроили в виртуальной сети.

Выполните `nslookup` команду, чтобы разрешить IP-адрес реестра по частной ссылке:

```bash
nslookup $REGISTRY_NAME.azurecr.io
```

Пример выходных данных показывает IP-адрес реестра в адресном пространстве подсети:

```console
[...]
myregistry.azurecr.io       canonical name = myregistry.privatelink.azurecr.io.
Name:   myregistry.privatelink.azurecr.io
Address: 10.0.0.6
```

Сравните этот результат с общедоступным IP- `nslookup` адресом в выходных данных для одного и того же реестра в общедоступной конечной точке:

```console
[...]
Non-authoritative answer:
Name:   myregistry.westeurope.cloudapp.azure.com
Address: 40.78.103.41
```

### <a name="registry-operations-over-private-link"></a>Операции с реестром по закрытой ссылке

Также убедитесь, что вы можете выполнять операции с реестром из виртуальной машины в подсети. Установите SSH-подключение к виртуальной машине и выполните команду [AZ контроля учетных записей][az-acr-login] для входа в реестр. В зависимости от конфигурации виртуальной машины может потребоваться добавить следующие команды в префикс `sudo`.

```bash
az acr login --name $REGISTRY_NAME
```

Выполнение операций с реестром `docker pull` , например, извлечение образца образа из реестра. Замените `hello-world:v1` на изображение и тег, соответствующий реестру, с префиксом имени сервера входа в реестр (все строчные буквы):

```bash
docker pull myregistry.azurecr.io/hello-world:v1
``` 

DOCKER успешно запрашивает образ на виртуальной машине.

## <a name="manage-private-endpoint-connections"></a>Управление подключениями к частным конечным точкам

Управление подключениями к частным конечным точкам реестра с помощью портал Azure или с помощью команд в группе команд [AZ контроля доступа Private-Endpoint-Connection][az-acr-private-endpoint-connection] . К операциям относятся утверждение, удаление, перечисление, отклонение или отображение сведений о подключениях к частным конечным точкам реестра.

Например, чтобы получить список подключений к частным конечным точкам реестра, выполните команду [AZ запись закрытая конечная точка-подключение список][az-acr-private-endpoint-connection-list] . Пример:

```azurecli
az acr private-endpoint-connection list \
  --registry-name $REGISTRY_NAME 
```

При настройке подключения к частной конечной точке с помощью действий, описанных в этой статье, реестр автоматически принимает подключения от клиентов и служб, имеющих разрешения RBAC для реестра. Можно настроить конечную точку для обязательного утверждения подключений вручную. Сведения о том, как утвердить и отклонять подключения к частным конечным точкам, см. [в разделе Управление подключением к частной конечной точке](../private-link/manage-private-endpoint.md).

## <a name="add-zone-records-for-replicas"></a>Добавление записей зоны для реплик

Как показано в этой статье, при добавлении подключения к частной конечной точке в реестр записи DNS в `privatelink.azurecr.io` зоне создаются для реестра и его конечных точек данных в регионах, где выполняется [репликация](container-registry-geo-replication.md)реестра. 

Если позднее добавить новую реплику, необходимо вручную добавить новую запись зоны для конечной точки данных в этом регионе. Например, если создать реплику *myregistry* в расположении *норсеуропе* , добавьте запись зоны для `myregistry.northeurope.data.azurecr.io`. Инструкции см. в разделе [Создание записей DNS в частной зоне](#create-dns-records-in-the-private-zone) этой статьи.

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы создали все ресурсы Azure в одной группе ресурсов и больше не нуждаются в них, вы можете при необходимости удалить ресурсы с помощью одной команды [AZ Group Delete](/cli/azure/group) :

```azurecli
az group delete --name $RESOURCE_GROUP
```

Чтобы очистить ресурсы на портале, перейдите к группе ресурсов. После загрузки группы ресурсов щелкните **Удалить группу ресурсов** , чтобы удалить группу ресурсов и ресурсы, хранящиеся в ней.

## <a name="next-steps"></a>Дальнейшие действия

* Дополнительные сведения о закрытой ссылке см. в документации по [частной ссылке Azure](../private-link/private-link-overview.md) .
* Если необходимо настроить правила доступа к реестру из брандмауэра клиента, см. раздел [Настройка правил для доступа к реестру контейнеров Azure за брандмауэром](container-registry-firewall-access-rules.md).

<!-- LINKS - external -->
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
[az-acr-update]: /cli/azure/acr#az-acr-update
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
