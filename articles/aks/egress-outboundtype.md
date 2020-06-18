---
title: Настройка определяемых пользователем маршрутов в службе Azure Kubernetes (AKS)
description: Узнайте, как определить пользовательский маршрут исходящего трафика в службе Azure Kubernetes (AKS)
services: container-service
ms.topic: article
ms.date: 03/16/2020
ms.openlocfilehash: babfd70a6a9732113531be13073af212a6820557
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2020
ms.locfileid: "83677885"
---
# <a name="customize-cluster-egress-with-a-user-defined-route-preview"></a>Настройка исходящего трафика кластера с помощью определяемого пользователем маршрута (предварительная версия)

Исходящий трафик из кластера AKS можно настроить в соответствии с конкретными сценариями. По умолчанию AKS будет подготавливать для настройки и использования для исходящего трафика стандартную SKU балансировщика нагрузки. Однако настройка по умолчанию может не соответствовать требованиям всех сценариев, если общедоступные IP-адреса запрещены или для исходящего трафика требуются дополнительные прыжки.

В этой статье описывается настройка маршрута исходящего трафика кластера для поддержки пользовательских сетевых сценариев, например тех, которые запрещают использование общедоступных IP-адресов и требуют наличия кластера за сетевым виртуальным устройством (NVA).

> [!IMPORTANT]
> Функции AKS в предварительной версии являются самостоятельными и предоставляются на основе согласия. Предварительные версии предоставляются *как есть* и *при наличии*. На них не распространяются соглашения об уровне обслуживания и ограниченная гарантия. Предварительные версии AKS предоставляются с частичной клиентской поддержкой (*по мере возможности*). Следовательно, эти функции не предназначены для использования в рабочей среде. Дополнительные сведения доступны в следующих статьях поддержки.
>
> * [Политики поддержки AKS](support-policies.md)
> * [Часто задаваемые вопросы о поддержке Azure](faq.md)

## <a name="prerequisites"></a>Предварительные требования
* Интерфейс командной строки Azure 2.0.81 или более поздней версии
* Расширение интерфейса командной строки Azure (предварительная версия) 0.4.28 или выше
* Версия API `2020-01-01` или выше

## <a name="install-the-latest-azure-cli-aks-preview-extension"></a>Установите последнюю версию расширения AKS интерфейса командной строки Azure (предварительная версия)
Чтобы задать тип исходящего трафика кластера, требуется версия расширения AKS интерфейса командной строки Azure 0.4.18 (предварительная версия) или более поздняя. Установите расширение AKS интерфейса командной строки Azure (предварительная версия), используя команду az extension add, затем проверьте наличие доступных обновлений, выполнив следующую команду az extension update.

```azure-cli
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

## <a name="limitations"></a>Ограничения
* В предварительной версии `outboundType` можно определить только во время создания кластера, после чего внесение изменений невозможно.
* В предварительной версии кластеры AKS `outboundType` должны использовать сетевой интерфейс контейнеров Azure. Kubenet доступен для настройки. Чтобы использовать его, необходимо вручную привязать таблицу маршрутизации к подсети AKS.
* Для настройки `outboundType` требуются кластеры AKS с `vm-set-type` `VirtualMachineScaleSets` и `load-balancer-sku` `Standard`.
* Чтобы задать для `outboundType` значение `UDR`, необходимо использовать определяемый пользователем маршрут с действительными исходящими подключениями для кластера.
* Если для `outboundType` задано значение `UDR`, это подразумевает, что исходный IP-адрес входящего трафика, маршрутизируемого на балансировщик нагрузки, может **не соответствовать** конечному адресу исходящего трафика кластера.

## <a name="overview-of-outbound-types-in-aks"></a>Общие сведения о типах исходящих подключений в AKS

Кластер AKS можно настроить с помощью уникального `outboundType` типа «балансировщик нагрузки» и определяемой пользователем маршрутизации.

> [!IMPORTANT]
> Тип исходящего трафика влияет только на исходящий трафик кластера. Дополнительные сведения см. в разделе [Настройка контроллеров входящего трафика](ingress-basic.md).

### <a name="outbound-type-of-loadbalancer"></a>Тип исходящего трафика балансировщика нагрузки

Если задано значение `loadBalancer`, AKS автоматически завершает следующую настройку. Балансировщик нагрузки используется для исходящего трафика через назначенный общедоступный IP-адрес AKS. Тип исходящего трафика `loadBalancer` поддерживает службы Kubernetes типа `loadBalancer`, которые ожидают исходящий трафик из балансировщика нагрузки, созданного поставщиком ресурсов AKS.

Следующая настройка выполняется в AKS.
   * Общедоступный IP-адрес подготавливается для исходящего трафика кластера.
   * Общедоступный IP-адрес назначается ресурсу балансировщика нагрузки.
   * Серверные пулы для балансировщика нагрузки настраиваются для агентских узлов в кластере.

Ниже приведена топология сети, развернутая в кластерах AKS по умолчанию, которая использует `outboundType` `loadBalancer`.

![outboundtype-lb](media/egress-outboundtype/outboundtype-lb.png)

### <a name="outbound-type-of-userdefinedrouting"></a>Тип исходящего трафика userDefinedRouting

> [!NOTE]
> Использование типа исходящего трафика является сложным сетевым сценарием и требует правильной настройки сети.

Если задан `userDefinedRouting`, AKS не будет автоматически настраивать пути исходящего трафика. Следующие действия должны быть выполнены **пользователем**.

Кластер AKS должен быть развернут в существующей виртуальной сети с настроенной подсетью. При использовании архитектуры «Стандартный балансировщик нагрузки» (SLB) необходимо установить явный исходящий трафик. Для этого необходимо отправить исходящие запросы на устройство, например брандмауэр, шлюз, локальную среду, или разрешить обработку исходящего трафика общедоступным IP-адресом, назначенным стандартному балансировщику нагрузки или данному узлу.

Поставщик ресурсов AKS развернет стандартный балансировщик нагрузки (SLB). В балансировщике нагрузки не настроены никакие правила, а клиент не платит за [, пока правило не будет сформулировано](https://azure.microsoft.com/pricing/details/load-balancer/). AKS **не** предоставляет общедоступный IP-адрес для клиентского компонента стандартного балансировщика нагрузки автоматически. AKS **не** настраивает серверный пул балансировщика нагрузки автоматически.

## <a name="deploy-a-cluster-with-outbound-type-of-udr-and-azure-firewall"></a>Развертывание кластера с типом исходящего трафика UDR и брандмауэром Azure

Чтобы проиллюстрировать применение кластера с типом исходящего трафика с помощью определяемого пользователем маршрута, можно настроить кластер в виртуальной сети, подключенной к брандмауэру Azure.

![Заблокированная топология](media/egress-outboundtype/outboundtype-udr.png)

* Входящий трафик принудительно проходит через фильтры брандмауэра
   * Изолированная подсеть содержит внутренний балансировщик нагрузки для маршрутизации на узлы агента
   * Узлы агента изолированы в выделенную подсеть
* Исходящие запросы запускаются с узлов агентов на внутренний IP-адрес брандмауэра Azure по определяемому пользователем маршруту
   * Запросы от узлов агента AKS следуют по UDR, который был помещен в подсеть, где развернут кластер AKS
   * Брандмауэр Azure направляет исходящий трафик из виртуальной сети с клиентского компонента общедоступного IP-адреса
   * Доступ на уровень управления AKS защищается NSG с включенным IP-адресом клиентского компонента брандмауэра
   * Доступ к общедоступному Интернету или другим службам Azure осуществляется через IP-адрес клиентского компонента брандмауэра

### <a name="set-configuration-via-environment-variables"></a>Настройка конфигурации с помощью переменных среды

Определите набор переменных среды, которые будут использоваться при создании ресурсов.

```bash
PREFIX="contosofin"
RG="${PREFIX}-rg"
LOC="eastus"
NAME="${PREFIX}outboundudr"
AKS_NAME="${PREFIX}aks"
VNET_NAME="${PREFIX}vnet"
AKSSUBNET_NAME="${PREFIX}akssubnet"
SVCSUBNET_NAME="${PREFIX}svcsubnet"
# DO NOT CHANGE FWSUBNET_NAME - This is currently a requirement for Azure Firewall.
FWSUBNET_NAME="AzureFirewallSubnet"
FWNAME="${PREFIX}fw"
FWPUBLICIP_NAME="${PREFIX}fwpublicip"
FWIPCONFIG_NAME="${PREFIX}fwconfig"
FWROUTE_TABLE_NAME="${PREFIX}fwrt"
FWROUTE_NAME="${PREFIX}fwrn"
FWROUTE_NAME_INTERNET="${PREFIX}fwinternet"
DEVSUBNET_NAME="${PREFIX}dev"
```

Затем назначьте идентификаторы подписки.

```azure-cli

# NOTE: Update Subscription Name
# Set Default Azure Subscription to be Used via Subscription ID

az account set -s <SUBSCRIPTION_ID_GOES_HERE>

# NOTE: Update Subscription Name for setting SUBID

SUBID=$(az account show -s '<SUBSCRIPTION_NAME_GOES_HERE>' -o tsv --query 'id')
```

## <a name="create-a-virtual-network-with-multiple-subnets"></a>Создание виртуальной сети с несколькими подсетями

Подготовьте виртуальную сеть с тремя отдельными подсетями: одной — для кластера, другой — для брандмауэра и третьей — для входящего трафика службы.

![Пустая топология сети](media/egress-outboundtype/empty-network.png)

Создайте группу ресурсов для хранения всех ресурсов.

```azure-cli
# Create Resource Group

az group create --name $RG --location $LOC
```

Создайте две виртуальные сети для размещения кластера AKS и брандмауэра Azure. В каждой будет собственная подсеть. Начнем с сети AKS.

```
# Dedicated virtual network with AKS subnet

az network vnet create \
    --resource-group $RG \
    --name $VNET_NAME \
    --address-prefixes 100.64.0.0/16 \
    --subnet-name $AKSSUBNET_NAME \
    --subnet-prefix 100.64.1.0/24

# Dedicated subnet for K8s services

az network vnet subnet create \
    --resource-group $RG \
    --vnet-name $VNET_NAME \
    --name $SVCSUBNET_NAME \
    --address-prefix 100.64.2.0/24

# Dedicated subnet for Azure Firewall (Firewall name cannot be changed)

az network vnet subnet create \
    --resource-group $RG \
    --vnet-name $VNET_NAME \
    --name $FWSUBNET_NAME \
    --address-prefix 100.64.3.0/24
```

## <a name="create-and-setup-an-azure-firewall-with-a-udr"></a>Создание и настройка брандмауэра Azure с использованием UDR

Необходимо настроить правила входящего и исходящего трафика брандмауэра Azure. Основное назначение брандмауэра — предоставить организациям возможность точной настройки правил входящего и исходящего трафика для кластера AKS.

![Брандмауэр и UDR](media/egress-outboundtype/firewall-udr.png)

Создайте ресурс общедоступного IP-адреса стандартного SKU, который будет использоваться в качестве клиентского адреса брандмауэра Azure.

```azure-cli
az network public-ip create -g $RG -n $FWPUBLICIP_NAME -l $LOC --sku "Standard"
```

Зарегистрируйте предварительную версию расширения интерфейса командной строки, чтобы создать брандмауэр Azure.
```azure-cli
# Install Azure Firewall preview CLI extension

az extension add --name azure-firewall

# Deploy Azure Firewall

az network firewall create -g $RG -n $FWNAME -l $LOC
```

Созданный ранее IP-адрес теперь можно назначить клиентскому компоненту брандмауэра.
> [!NOTE]
> Настройка общедоступного IP-адреса для брандмауэра Azure может занимать несколько минут.
> 
> Если при выполнении команды ниже стабильно появляются ошибки, удалите существующий брандмауэр и общедоступный IP-адрес и подготовьте общедоступный IP-адрес и брандмауэр Azure через портал.

```azure-cli
# Configure Firewall IP Config

az network firewall ip-config create -g $RG -f $FWNAME -n $FWIPCONFIG_NAME --public-ip-address $FWPUBLICIP_NAME --vnet-name $VNET_NAME
```

После выполнения предыдущей команды сохраните IP-адрес клиентского компонента брандмауэра для настройки позже.

```bash
# Capture Firewall IP Address for Later Use

FWPUBLIC_IP=$(az network public-ip show -g $RG -n $FWPUBLICIP_NAME --query "ipAddress" -o tsv)
FWPRIVATE_IP=$(az network firewall show -g $RG -n $FWNAME --query "ipConfigurations[0].privateIpAddress" -o tsv)
```

### <a name="create-a-udr-with-a-hop-to-azure-firewall"></a>Создание UDR с прыжком к брандмауэру Azure

Azure автоматически направляет трафик между подсетями Azure, виртуальными и локальными сетями. Если нужно изменить какой-либо из стандартных маршрутов Azure, это можно сделать с помощью создания таблицы маршрутов.

Создайте пустую таблицу маршрутов, которая будет связана с заданной подсетью. В таблице маршрутов будет определен следующий прыжок к созданному выше брандмауэру Azure. С каждой подсетью может быть связана одна таблица маршрутов (или ноль).

```azure-cli
# Create UDR and add a route for Azure Firewall

az network route-table create -g $RG --name $FWROUTE_TABLE_NAME
az network route-table route create -g $RG --name $FWROUTE_NAME --route-table-name $FWROUTE_TABLE_NAME --address-prefix 0.0.0.0/0 --next-hop-type VirtualAppliance --next-hop-ip-address $FWPRIVATE_IP --subscription $SUBID
az network route-table route create -g $RG --name $FWROUTE_NAME_INTERNET --route-table-name $FWROUTE_TABLE_NAME --address-prefix $FWPUBLIC_IP/32 --next-hop-type Internet
```

Сведения о том, как переопределить системные маршруты Azure по умолчанию или добавить дополнительные маршруты в таблицу маршрутов подсети, см. в разделе [Документация по таблицам маршрутизации виртуальной сети](../virtual-network/virtual-networks-udr-overview.md#user-defined).

## <a name="adding-network-firewall-rules"></a>Добавление правил брандмауэра сети

> [!WARNING]
> Ниже приведен один пример добавления правила брандмауэра. Все конечные точки исходящего трафика, определенные в разделе [обязательные конечные точки исходящего трафика](egress.md), должны быть включены правилами брандмауэра приложения — только в этом случае кластеры AKS будут работать. Если не включить эти конечные точки, кластер не сможет функционировать.

Ниже приведен пример правила сети и приложения. Мы добавляем сетевое правило, разрешающее любой протокол, исходный и целевой адреса, а также целевые порты. Мы также добавим правило приложения для **некоторых** конечных точек, необходимых для AKS.

В рабочем сценарии необходимо предоставить доступ только к обязательным конечным точкам для приложения и точкам, заданным в разделе [Обязательный исходящий трафик AKS](egress.md).

```
# Add Network FW Rules

az network firewall network-rule create -g $RG -f $FWNAME --collection-name 'aksfwnr' -n 'netrules' --protocols 'Any' --source-addresses '*' --destination-addresses '*' --destination-ports '*' --action allow --priority 100

# Add Application FW Rules
# IMPORTANT: Add AKS required egress endpoints

az network firewall application-rule create -g $RG -f $FWNAME \
    --collection-name 'AKS_Global_Required' \
    --action allow \
    --priority 100 \
    -n 'required' \
    --source-addresses '*' \
    --protocols 'http=80' 'https=443' \
    --target-fqdns \
        'aksrepos.azurecr.io' \
        '*blob.core.windows.net' \
        'mcr.microsoft.com' \
        '*cdn.mscr.io' \
        '*.data.mcr.microsoft.com' \
        'management.azure.com' \
        'login.microsoftonline.com' \
        'ntp.ubuntu.com' \
        'packages.microsoft.com' \
        'acs-mirror.azureedge.net'
```

Дополнительные сведения о службе брандмауэра Azure см. в разделе [Документация к брандмауэру Azure](https://docs.microsoft.com/azure/firewall/overview).

## <a name="associate-the-route-table-to-aks"></a>Привязка таблицы маршрутизации к AKS

Чтобы связать кластер с брандмауэром, выделенная выше подсеть для подсети кластера должна ссылаться на созданную выше таблицу маршрутизации. Привязку можно выполнить, создав команду для виртуальной сети, в которой находятся кластер и брандмауэр, чтобы обновить таблицу маршрутизации подсети кластера.

```azure-cli
# Associate route table with next hop to Firewall to the AKS subnet

az network vnet subnet update -g $RG --vnet-name $VNET_NAME --name $AKSSUBNET_NAME --route-table $FWROUTE_TABLE_NAME
```

## <a name="deploy-aks-with-outbound-type-of-udr-to-the-existing-network"></a>Развертывание AKS с типом исходящего трафика UDR в существующую сеть

Теперь кластер AKS можно развернуть в существующей виртуальной сети. Чтобы задать для типа исходящего трафика кластера определяемый пользователем маршрут, существующая подсеть должна быть предоставлена AKS.

![aks-deploy](media/egress-outboundtype/outboundtype-udr.png)

### <a name="create-a-service-principal-with-access-to-provision-inside-the-existing-virtual-network"></a>Создание субъекта-службы с доступом для подготовки ресурсов в существующей виртуальной сети

Субъект-служба используется AKS для создания ресурсов кластера. Субъект-служба, переданный во время создания, используется для создания базовых ресурсов AKS, таких как виртуальные машины, хранилище и балансировщики нагрузки, используемые AKS. Если предоставлено слишком мало разрешений, система не сможет подготовить кластер AKS.

```azure-cli
# Create SP and Assign Permission to Virtual Network

az ad sp create-for-rbac -n "${PREFIX}sp" --skip-assignment
```

Теперь замените `APPID` и `PASSWORD` ниже идентификатором appid субъекта-службы и паролем субъекта-службы, созданными автоматически в результате выполнения предыдущей команды. Мы укажем ИД ресурса виртуальной сети, чтобы предоставить субъекту-службе необходимые разрешения. Тогда AKS сможет развернуть в ней ресурсы.

```azure-cli
APPID="<SERVICE_PRINCIPAL_APPID_GOES_HERE>"
PASSWORD="<SERVICEPRINCIPAL_PASSWORD_GOES_HERE>"
VNETID=$(az network vnet show -g $RG --name $VNET_NAME --query id -o tsv)

# Assign SP Permission to VNET

az role assignment create --assignee $APPID --scope $VNETID --role Contributor

# View Role Assignment
az role assignment list --assignee $APPID --all -o table
```

### <a name="deploy-aks"></a>Развертывание AKS

Наконец, кластер AKS можно развернуть в существующей подсети, выделенной для кластера. Целевая подсеть для развертывания определяется переменной среды `$SUBNETID`. Мы не определили переменную `$SUBNETID` на предыдущих шагах. Чтобы задать значение для идентификатора подсети, можно использовать следующую команду.

```azurecli
SUBNETID="/subscriptions/$SUBID/resourceGroups/$RG/providers/Microsoft.Network/virtualNetworks/$VNET_NAME/subnets/$AKSSUBNET_NAME"
```

Мы определим тип исходящего трафика, чтобы он следовал UDR, который существует в подсети. Так AKS сможет пропустить настройку и подготовку IP-адреса для балансировщика нагрузки, который теперь может быть строго внутренним.

Можно добавить функцию AKS для [диапазонов разрешенных IP-адресов сервера API](api-server-authorized-ip-ranges.md), чтобы ограничить доступ к серверу API общедоступной конечной точкой брандмауэра. Функция «диапазон разрешенных IP-адресов» отмечается на схеме как NSG, которую необходимо передать для доступа на уровень управления. Если вы включаете функцию разрешенных IP-адресов для ограничения доступа на сервер API, в инструментах разработчика следует использовать инсталляционный сервер из виртуальной сети брандмауэра или добавить все конечные точки разработчика в диапазон разрешенных IP-адресов.

> [!TIP]
> В развертывание кластера можно добавить дополнительные функции, например частный кластер. При использовании диапазонов разрешенных IP-адресов инсталляционный сервер потребуется в сети кластера для доступа на сервер API.

```azure-cli
az aks create -g $RG -n $AKS_NAME -l $LOC \
  --node-count 3 \
  --network-plugin azure --generate-ssh-keys \
  --service-cidr 192.168.0.0/16 \
  --dns-service-ip 192.168.0.10 \
  --docker-bridge-address 172.22.0.1/29 \
  --vnet-subnet-id $SUBNETID \
  --service-principal $APPID \
  --client-secret $PASSWORD \
  --load-balancer-sku standard \
  --outbound-type userDefinedRouting \
  --api-server-authorized-ip-ranges $FWPUBLIC_IP
  ```

### <a name="enable-developer-access-to-the-api-server"></a>Разрешение доступа разработчика к серверу API

Из-за особенностей настройки диапазонов разрешенных IP-адресов для кластера необходимо добавить IP-адреса инструментария разработчика в список утвержденных диапазонов IP-адресов кластера AKS, чтобы осуществлять доступ к серверу API. Кроме того, можно настроить инсталляционный сервер с необходимым инструментарием внутри отдельной подсети в виртуальной сети брандмауэра.

Добавьте еще один IP-адрес в утвержденный диапазон, выполнив следующую команду.

```bash
# Retrieve your IP address
CURRENT_IP=$(dig @resolver1.opendns.com ANY myip.opendns.com +short)

# Add to AKS approved list
az aks update -g $RG -n $AKS_NAME --api-server-authorized-ip-ranges $CURRENT_IP/32

```

 Используйте команду [az aks get-credentials][az-aks-get-credentials], чтобы настроить `kubectl` для подключения к только что созданному кластеру Kubernetes. 

 ```azure-cli
 az aks get-credentials -g $RG -n $AKS_NAME
 ```

### <a name="setup-the-internal-load-balancer"></a>Настройка внутреннего балансировщика нагрузки

AKS развернула балансировщик нагрузки с кластером, который можно настроить как [внутренний балансировщик нагрузки](internal-lb.md).

Чтобы создать внутренний балансировщик нагрузки, создайте манифест службы internal-lb.yaml с типом службы LoadBalancer и заметкой azure-load-balancer-internal, как показано в следующем примере.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: internal-app
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-internal: "true"
    service.beta.kubernetes.io/azure-load-balancer-internal-subnet: "contosofinsvcsubnet"
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: internal-app
```

Разверните приложение с помощью команды kubectl apply и укажите имя манифеста YAML:

```bash
kubectl apply -f internal-lb.yaml
```

## <a name="deploy-a-kubernetes-service"></a>Развертывание службы Kubernetes

Поскольку задан тип исходящего трафика кластера UDR, привязка узлов агента в качестве серверного пула для балансировщика нагрузки не выполняется AKS автоматически во время создания кластера. Однако привязка серверного пула выполняется облачным поставщиком Azure Kubernetes при развертывании службы Kubernetes.

Разверните приложение Azure для голосования, cкопировав YAML ниже в файл с именем `example.yaml`.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: azure-vote-back
spec:
  replicas: 1
  selector:
    matchLabels:
      app: azure-vote-back
  template:
    metadata:
      labels:
        app: azure-vote-back
    spec:
      nodeSelector:
        "beta.kubernetes.io/os": linux
      containers:
      - name: azure-vote-back
        image: redis
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 250m
            memory: 256Mi
        ports:
        - containerPort: 6379
          name: redis
---
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-back
spec:
  ports:
  - port: 6379
  selector:
    app: azure-vote-back
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: azure-vote-front
spec:
  replicas: 1
  selector:
    matchLabels:
      app: azure-vote-front
  template:
    metadata:
      labels:
        app: azure-vote-front
    spec:
      nodeSelector:
        "beta.kubernetes.io/os": linux
      containers:
      - name: azure-vote-front
        image: microsoft/azure-vote-front:v1
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 250m
            memory: 256Mi
        ports:
        - containerPort: 80
        env:
        - name: REDIS
          value: "azure-vote-back"
---
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-internal: "true"
    service.beta.kubernetes.io/azure-load-balancer-internal-subnet: "contosofinsvcsubnet"
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-vote-front
```

Разверните службу, выполнив следующую команду.

```bash
kubectl apply -f example.yaml
```

## <a name="add-a-dnat-rule-to-azure-firewall"></a>Добавление правила DNAT в брандмауэр Azure

Чтобы настроить возможность входящего подключения, необходимо записать в брандмауэр Azure правило DNAT. Чтобы проверить возможность подключения к нашему кластеру, для общедоступного IP-адреса клиентского компонента брандмауэра определяется правило, направляющего его на внутренний IP-адрес, предоставляемый внутренней службой.

Целевой адрес можно настроить, так как это порт брандмауэра, к которому осуществляется доступ. Преобразованный адрес должен представлять собой IP-адрес внутреннего балансировщика нагрузки. Преобразованный порт должен представлять собой предоставленный порт для вашей службы Kubernetes.

Потребуется указать внутренний IP-адрес, назначенный балансировщику нагрузки, который был создан службой Kubernetes. Получите адрес, выполнив команду:

```bash
kubectl get services
```

Необходимый IP-адрес будет указан в столбце «ВНЕШНИЕ IP-АДРЕСА» следующим образом.

```bash
NAME               TYPE           CLUSTER-IP       EXTERNAL-IP   PORT(S)        AGE
azure-vote-back    ClusterIP      192.168.92.209   <none>        6379/TCP       23m
azure-vote-front   LoadBalancer   192.168.19.183   100.64.2.5    80:32106/TCP   23m
kubernetes         ClusterIP      192.168.0.1      <none>        443/TCP        4d3h
```

```azure-cli
az network firewall nat-rule create --collection-name exampleset --destination-addresses $FWPUBLIC_IP --destination-ports 80 --firewall-name $FWNAME --name inboundrule --protocols Any --resource-group $RG --source-addresses '*' --translated-port 80 --action Dnat --priority 100 --translated-address <INSERT IP OF K8s SERVICE>
```

## <a name="clean-up-resources"></a>Очистка ресурсов

> [!NOTE]
> При удалении внутренней службы Kubernetes, если внутренний балансировщик нагрузки больше не используется какой-либо службой, поставщик облачных служб Azure удалит его. При следующем развертывании службы балансировщик нагрузки будет развернут снова, если в запрошенной конфигурации не обнаружится другой балансировщик.

Чтобы очистить ресурсы Azure, удалите группу ресурсов AKS.

```azure-cli
az group delete -g $RG
```

## <a name="validate-connectivity"></a>Проверка подключения

Перейдите к IP-адресу клиентского компонента брандмауэра Azure в браузере, чтобы проверить подключение.

Вы должны увидеть образ приложения для голосования Azure.

## <a name="next-steps"></a>Дальнейшие действия

См. раздел [Обзор сетевой UDR Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview).

См. раздел [Сведения о создании, изменении и удалении таблицы маршрутизации](https://docs.microsoft.com/azure/virtual-network/manage-route-table).

<!-- LINKS - internal -->
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
