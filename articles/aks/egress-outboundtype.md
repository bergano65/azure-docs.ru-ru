---
title: Настройка определяемых пользователем маршрутов (UDR) в службе Kubernetes Azure (AKS)
description: Узнайте, как определить пользовательский маршрут исходящего трафика в службе Kubernetes Azure (AKS).
services: container-service
ms.topic: article
ms.date: 03/16/2020
ms.openlocfilehash: 3780680c485aebf1ffc654d31c577821a9b96fff
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80676502"
---
# <a name="customize-cluster-egress-with-a-user-defined-route-preview"></a>Настройка исходящего трафика кластера с помощью определяемого пользователем маршрута (Предварительная версия)

Исходящий трафик из кластера AKS можно настроить в соответствии с конкретными сценариями. По умолчанию AKS будет подготавливать стандартную SKU Load Balancer для настройки и использования для исходящего трафика. Однако настройка по умолчанию может не соответствовать требованиям всех сценариев, если общедоступные IP-адреса запрещены или для исходящего трафика требуются дополнительные прыжки.

В этой статье описывается настройка маршрута исходящего трафика кластера для поддержки пользовательских сетевых сценариев, например тех, которые запрещают использование общедоступных IP-адресов и требуют наличия кластера за сетевым виртуальным устройством (NVA).

> [!IMPORTANT]
> Функции предварительной версии AKS являются самостоятельными и предоставляются на основе согласия. Предварительные версии предоставляются *как есть* и *доступны* и исключаются из соглашения об уровне обслуживания (SLA) и ограниченной гарантии. Предварительные версии AKS частично охвачены службой поддержки клиентов. *best effort* Поэтому функции не предназначены для использования в рабочей среде. Дополнительные сведения см. в следующих статьях поддержки:
>
> * [Политики поддержки AKS](support-policies.md)
> * [Часто задаваемые вопросы о поддержке Azure](faq.md)

## <a name="prerequisites"></a>Предварительные условия
* Azure CLI версии 2.0.81 или выше
* Azure CLI предварительной версии расширения 0.4.28 или выше
* Версия API `2020-01-01` или более поздняя

## <a name="install-the-latest-azure-cli-aks-preview-extension"></a>Установите последнюю версию расширения Azure CLI AKS Preview
Чтобы задать тип исходящего трафика кластера, требуется расширение Azure CLI AKS Preview версии 0.4.18 или более поздней. Установите расширение Azure CLI AKS Preview с помощью команды AZ Extension Add, а затем проверьте наличие доступных обновлений, выполнив следующую команду AZ Extension Update:

```azure-cli
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

## <a name="limitations"></a>Ограничения
* Во время предварительной версии `outboundType` можно определить только во время создания кластера и обновить позже.
* На этапе предварительной версии кластеры `outboundType` AKS должны использовать Azure CNI. Кубенет является настраиваемым, для использования необходимо вручную сопоставить таблицу маршрутов с подсетью AKS.
* Для `outboundType` параметра требуется кластеры AKS `vm-set-type` с `VirtualMachineScaleSets` `load-balancer-sku` параметром `Standard`и из.
* Для `outboundType` установки значения `UDR` требуется определенный пользователем маршрут с допустимыми исходящими подключениями для кластера.
* Если `outboundType` задать значение `UDR` , то IP-адрес входящего источника, направляемый в подсистему балансировки нагрузки, может **не соответствовать** исходящему исходящего адреса кластера.

## <a name="overview-of-outbound-types-in-aks"></a>Общие сведения о типах исходящих подключений в AKS

Кластер AKS можно настроить с помощью уникального `outboundType` подсистемы балансировки нагрузки типа или определяемой пользователем маршрутизации.

> [!IMPORTANT]
> Тип исходящего трафика влияет только на исходящий трафик кластера. Дополнительные сведения см. в разделе [Настройка контроллеров](ingress-basic.md) входящего трафика.

### <a name="outbound-type-of-loadbalancer"></a>Тип исходящего трафика подсистемы балансировки нагрузки

Если `loadBalancer` задано значение, AKS автоматически завершает следующую установку. Балансировщик нагрузки используется для исходящего трафика через назначенный общедоступный IP-адрес AKS. Тип исходящего трафика `loadBalancer` поддерживает службы Kubernetes типа `loadBalancer`, которые предполагают исходящий трафик из подсистемы балансировки нагрузки, созданной поставщиком ресурсов AKS.

Следующая настройка выполняется с помощью AKS.
   * Общедоступный IP-адрес подготавливается для исходящего трафика кластера.
   * Общедоступный IP-адрес назначается ресурсу балансировщика нагрузки.
   * Серверные пулы для подсистемы балансировки нагрузки — это настройка для узлов агентов в кластере.

Ниже приведена топология сети, развернутая в кластерах AKS по `outboundType` умолчанию, которая использует `loadBalancer`.

![аутбаундтипе](media/egress-outboundtype/outboundtype-lb.png)

### <a name="outbound-type-of-userdefinedrouting"></a>Тип исходящего трафика Усердефинедраутинг

> [!NOTE]
> Использование типа исходящего трафика является расширенным сетевым сценарием и требует правильной настройки сети.

Если `userDefinedRouting` задано значение, AKS не будет автоматически настраивать пути выхода. Ожидается, что **пользователь**должен выполнить следующие действия.

Кластер должен быть развернут в существующей виртуальной сети с настроенной подсетью. Допустимый определяемый пользователем маршрут (UDR) должен существовать в подсети с исходящим подключением.

Поставщик ресурсов AKS будет развертывать Стандартный балансировщик нагрузки (SLB). В подсистеме балансировки нагрузки не настроены никакие правила и [плата не взимается до тех пор, пока не будет помещено правило](https://azure.microsoft.com/pricing/details/load-balancer/). AKS **не** будет автоматически подготавливать общедоступный IP-адрес для внешнего интерфейса SLB. AKS **не** будет автоматически настраивать внутренний пул подсистемы балансировки нагрузки.

## <a name="deploy-a-cluster-with-outbound-type-of-udr-and-azure-firewall"></a>Развертывание кластера с типом исходящего трафика UDR и брандмауэром Azure

Чтобы проиллюстрировать применение кластера с типом исходящего трафика с помощью определяемого пользователем маршрута, можно настроить кластер в виртуальной сети, подключенной к брандмауэру Azure.

![Заблокированная топология](media/egress-outboundtype/outboundtype-udr.png)

* Входящий трафик принудительно проходит через фильтры брандмауэра
   * Изолированная подсеть содержит внутреннюю подсистему балансировки нагрузки для маршрутизации узлов агентов.
   * Узлы агента изолированы в выделенной подсети.
* Исходящие запросы запускаются с узлов агентов на внутренний IP-адрес брандмауэра Azure с помощью определяемого пользователем маршрута.
   * Запросы от узлов агента AKS следуют UDR, который был помещен в подсеть, в которую был развернут кластер AKS.
   * Брандмауэр Azure егрессес из виртуальной сети через общедоступный интерфейс IP-адреса
   * Доступ к плоскости управления AKS защищается с помощью NSG, который включил интерфейсный IP-адрес брандмауэра.
   * Доступ к общедоступному Интернету или другим службам Azure передается на интерфейсный IP-адрес брандмауэра и из него.

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

Затем задайте идентификаторы подписок.

```azure-cli
# Get ARM Access Token and Subscription ID - This will be used for AuthN later.

ACCESS_TOKEN=$(az account get-access-token -o tsv --query 'accessToken')

# NOTE: Update Subscription Name
# Set Default Azure Subscription to be Used via Subscription ID

az account set -s <SUBSCRIPTION_ID_GOES_HERE>

# NOTE: Update Subscription Name for setting SUBID

SUBID=$(az account show -s '<SUBSCRIPTION_NAME_GOES_HERE>' -o tsv --query 'id')
```

## <a name="create-a-virtual-network-with-multiple-subnets"></a>Создание виртуальной сети с несколькими подсетями

Подготавливает виртуальную сеть с тремя отдельными подсетями, одним для кластера, одним для брандмауэра и одним для входящих в службу.

![Пустая топология сети](media/egress-outboundtype/empty-network.png)

Создайте группу ресурсов для хранения всех ресурсов.

```azure-cli
# Create Resource Group

az group create --name $RG --location $LOC
```

Создайте две виртуальные сети для размещения кластера AKS и брандмауэра Azure. Каждый из них будет иметь собственную подсеть. Начнем с сети AKS.

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

## <a name="create-and-setup-an-azure-firewall-with-a-udr"></a>Создание и настройка брандмауэра Azure с помощью UDR

Необходимо настроить правила входящего и исходящего трафика брандмауэра Azure. Основная цель брандмауэра заключается в том, чтобы позволить организациям настраивать детализированные входящие и исходящие правила трафика в кластере AKS.

![Брандмауэр и UDR](media/egress-outboundtype/firewall-udr.png)

Создайте ресурс общедоступного IP-адреса стандартного SKU, который будет использоваться в качестве интерфейса брандмауэра Azure.

```azure-cli
az network public-ip create -g $RG -n $FWPUBLICIP_NAME -l $LOC --sku "Standard"
```

Зарегистрируйте предварительную версию CLI-расширения, чтобы создать брандмауэр Azure.
```azure-cli
# Install Azure Firewall preview CLI extension

az extension add --name azure-firewall

# Deploy Azure Firewall

az network firewall create -g $RG -n $FWNAME -l $LOC
```

Созданный ранее IP-адрес теперь можно назначить интерфейсу брандмауэра.
> [!NOTE]
> Установка общедоступного IP-адреса для брандмауэра Azure может занять несколько минут.
> 
> Если ошибки повторяются в приведенной ниже команде, удалите существующий брандмауэр и общедоступный IP-адрес и подготавливает общедоступный IP-адрес и брандмауэр Azure через портал одновременно.

```azure-cli
# Configure Firewall IP Config

az network firewall ip-config create -g $RG -f $FWNAME -n $FWIPCONFIG_NAME --public-ip-address $FWPUBLICIP_NAME --vnet-name $VNET_NAME
```

После выполнения предыдущей команды сохраните интерфейсный IP-адрес брандмауэра для настройки позже.

```bash
# Capture Firewall IP Address for Later Use

FWPUBLIC_IP=$(az network public-ip show -g $RG -n $FWPUBLICIP_NAME --query "ipAddress" -o tsv)
FWPRIVATE_IP=$(az network firewall show -g $RG -n $FWNAME --query "ipConfigurations[0].privateIpAddress" -o tsv)
```

### <a name="create-a-udr-with-a-hop-to-azure-firewall"></a>Создание UDR с прыжком к брандмауэру Azure

Azure автоматически направляет трафик между подсетями Azure, виртуальными и локальными сетями. Если нужно изменить какой-либо из стандартных маршрутов Azure, это можно сделать с помощью создания таблицы маршрутов.

Создайте пустую таблицу маршрутов, которая будет связана с заданной подсетью. В таблице маршрутов будет определен следующий прыжок по мере создания брандмауэра Azure, созданного ранее. С каждой подсетью может быть связана одна таблица маршрутов (или ноль).

```azure-cli
# Create UDR and add a route for Azure Firewall

az network route-table create -g $RG --name $FWROUTE_TABLE_NAME
az network route-table route create -g $RG --name $FWROUTE_NAME --route-table-name $FWROUTE_TABLE_NAME --address-prefix 0.0.0.0/0 --next-hop-type VirtualAppliance --next-hop-ip-address $FWPRIVATE_IP --subscription $SUBID
az network route-table route create -g $RG --name $FWROUTE_NAME_INTERNET --route-table-name $FWROUTE_TABLE_NAME --address-prefix $FWPUBLIC_IP/32 --next-hop-type Internet
```

Сведения о том, как можно переопределить системные маршруты Azure по умолчанию или добавить дополнительные маршруты в таблицу маршрутов подсети, см. в [документации по таблице маршрутов виртуальной сети](../virtual-network/virtual-networks-udr-overview.md#user-defined) .

## <a name="adding-network-firewall-rules"></a>Добавление правил сетевого брандмауэра

> [!WARNING]
> Ниже приведен один пример добавления правила брандмауэра. Все конечные точки исходящего трафика, определенные в [требуемых конечных точках](egress.md) исходящего трафика, должны быть включены правилами брандмауэра приложения для работы кластеров AKS. Если эти конечные точки не включены, кластер не сможет действовать.

Ниже приведен пример правила сети и приложения. Мы добавим сетевое правило, которое позволяет любому протоколу, адресу источника, адресу назначения и портам назначения. Мы также добавим правило приложения для **некоторых** конечных точек, необходимых для AKS.

В рабочем сценарии следует включить доступ только к необходимым конечным точкам для приложения и тем, которые определены в [AKS необходимости](egress.md)исходящего трафика.

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

Дополнительные сведения о службе брандмауэра Azure см. в [документации по брандмауэру Azure](https://docs.microsoft.com/azure/firewall/overview) .

## <a name="associate-the-route-table-to-aks"></a>Связывание таблицы маршрутов с AKS

Чтобы связать кластер с брандмауэром, выделенная выше подсеть для подсети кластера должна ссылаться на созданную выше таблицу маршрутов. Связь можно выполнить, выполнив команду виртуальной сети, в которой хранятся как кластер, так и брандмауэр, чтобы обновить таблицу маршрутов в подсети кластера.

```azure-cli
# Associate route table with next hop to Firewall to the AKS subnet

az network vnet subnet update -g $RG --vnet-name $VNET_NAME --name $AKSSUBNET_NAME --route-table $FWROUTE_TABLE_NAME
```

## <a name="deploy-aks-with-outbound-type-of-udr-to-the-existing-network"></a>Развертывание AKS с типом исходящего трафика UDR в существующую сеть

Теперь кластер AKS можно развернуть в существующей настройке виртуальной сети. Чтобы задать тип исходящего трафика кластера для определяемой пользователем маршрутизации, необходимо предоставить имеющуюся подсеть для AKS.

![AKS — развертывание](media/egress-outboundtype/outboundtype-udr.png)

### <a name="create-a-service-principal-with-access-to-provision-inside-the-existing-virtual-network"></a>Создание субъекта-службы с доступом для инициализации в существующей виртуальной сети

Субъект-служба используется AKS для создания ресурсов кластера. Субъект-служба, переданный во время создания, используется для создания базовых ресурсов AKS, таких как виртуальные машины, хранилище и подсистемы балансировки нагрузки, используемые AKS. Если предоставлено слишком мало разрешений, он не сможет подготавливать кластер AKS.

```azure-cli
# Create SP and Assign Permission to Virtual Network

az ad sp create-for-rbac -n "${PREFIX}sp" --skip-assignment
```

Теперь замените `APPID` и `PASSWORD` ниже идентификатором субъекта-службы и паролем субъекта-службы, созданным с помощью предыдущего вывода команды. Мы будем ссылаться на идентификатор ресурса виртуальной сети, чтобы предоставить разрешения субъекту-службе, чтобы AKS мог развернуть в ней ресурсы.

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

Наконец, кластер AKS можно развернуть в существующей подсети, выделенной для кластера. Целевая подсеть, в которой будет выполняться развертывание, `$SUBNETID`определяется с помощью переменной среды. Мы не определили `$SUBNETID` переменную на предыдущих шагах. Чтобы задать значение для идентификатора подсети, можно использовать следующую команду:

```azurecli
SUBNETID="/subscriptions/$SUBID/resourceGroups/$RG/providers/Microsoft.Network/virtualNetworks/$VNET_NAME/subnets/$AKSSUBNET_NAME"
```

Мы определим исходящий тип для выполнения UDR, который существует в подсети, что позволяет AKS пропустить установку и подготовку IP-адресов для балансировщика нагрузки, который теперь может быть строго внутренним.

Для ограничения доступа к серверу API только к общедоступной конечной точке брандмауэра можно добавить функцию AKS для [IP-адресов разрешенного сервера API](api-server-authorized-ip-ranges.md) . Функция "диапазоны разрешенных IP-адресов" отмечается на схеме как NSG, которую необходимо передать для доступа к плоскости управления. При включении возможности авторизации диапазона IP-адресов для ограничения доступа к серверу API средства разработчика должны использовать Jumpbox из виртуальной сети брандмауэра или добавить все конечные точки разработчика в диапазон допустимых IP-адресов.

> [!TIP]
> Дополнительные функции можно добавить в развертывание кластера, например (частный кластер) []. При использовании IP-диапазонов с авторизацией для доступа к серверу API в сети кластера потребуется Jumpbox.

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

### <a name="enable-developer-access-to-the-api-server"></a>Разрешение доступа разработчика к серверу API

Из-за настройки разрешенных IP-диапазонов для кластера необходимо добавить IP-адреса средств разработчика в список разрешенных диапазонов IP-адресов для доступа к серверу API в кластере AKS. Кроме того, можно настроить Jumpbox с необходимыми инструментами в отдельной подсети в виртуальной сети брандмауэра.

Добавьте другой IP-адрес в утвержденные диапазоны с помощью следующей команды:

```bash
# Retrieve your IP address
CURRENT_IP=$(dig @resolver1.opendns.com ANY myip.opendns.com +short)

# Add to AKS approved list
az aks update -g $RG -n $AKS_NAME --api-server-authorized-ip-ranges $CURRENT_IP/32

```

 Используйте команду [AZ AKS Get-Credential][az-aks-get-credentials] , чтобы настроить `kubectl` подключение к созданному кластеру Kubernetes. 

 ```azure-cli
 az aks get-credentials -g $RG -n $AKS_NAME
 ```

### <a name="setup-the-internal-load-balancer"></a>Настройка внутренней подсистемы балансировки нагрузки

AKS развернула подсистему балансировки нагрузки с кластером, которую можно настроить в качестве [внутренней подсистемы балансировки нагрузки](internal-lb.md).

Чтобы создать внутреннюю подсистему балансировки нагрузки, создайте манифест службы с именем Internal-фунтов. YAML с типом службы и внутренней заметкой Azure-Load-балансировщик, как показано в следующем примере:

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

Разверните внутреннюю подсистему балансировки нагрузки с помощью kubectl Apply и укажите имя манифеста YAML:

```bash
kubectl apply -f internal-lb.yaml
```

## <a name="deploy-a-kubernetes-service"></a>Развертывание службы Kubernetes

Так как Тип исходящего трафика кластера установлен как UDR, сопоставление узлов агентов в качестве внутреннего пула для балансировщика нагрузки не выполняется автоматически AKS при создании кластера. Однако Ассоциация внутреннего пула обрабатывается поставщиком облачных служб Kubernetes Azure при развертывании службы Kubernetes.

Разверните приложение Azure для голосования, скопировав YAML ниже в файл с именем `example.yaml`.

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

Разверните службу, выполнив:

```bash
kubectl apply -f example.yaml
```

## <a name="add-a-dnat-rule-to-azure-firewall"></a>Добавление правила ДНаТ в брандмауэр Azure

Чтобы настроить входящее подключение, необходимо записать правило ДНаТ в брандмауэр Azure. Чтобы проверить возможность подключения к нашему кластеру, для интерфейса общедоступного IP-адреса брандмауэра определяется правило, чтобы оно направлялось на внутренний IP-адрес, предоставляемый внутренней службой.

Адрес назначения можно настроить, так как это порт, к которому обращается брандмауэр. Адрес переведенного адреса должен быть IP-адресом внутренней подсистемы балансировки нагрузки. Переведенный порт должен быть предоставленным портом для службы Kubernetes.

Вам потребуется указать внутренний IP-адрес, назначенный подсистеме балансировки нагрузки, созданной службой Kubernetes. Получите адрес, выполнив команду:

```bash
kubectl get services
```

Требуемый IP-адрес будет указан в столбце EXTERNAL-IP, как показано ниже.

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
> При удалении внутренней службы Kubernetes, если внутренняя подсистема балансировки нагрузки больше не используется какой-либо службой, поставщик облачных служб Azure удалит внутреннюю подсистему балансировки нагрузки. При следующем развертывании службы подсистема балансировки нагрузки будет развернута, если ее не удается найти с запрошенной конфигурацией.

Чтобы очистить ресурсы Azure, удалите группу ресурсов AKS.

```azure-cli
az group delete -g $RG
```

## <a name="validate-connectivity"></a>Проверка подключения

Перейдите к интерфейсному IP-адресу брандмауэра Azure в браузере, чтобы проверить подключение.

Вы должны увидеть образ приложения для голосования Azure.

## <a name="next-steps"></a>Дальнейшие шаги

См. [Обзор сетевых UDR Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview).

См. раздел [Создание, изменение или удаление таблицы маршрутов](https://docs.microsoft.com/azure/virtual-network/manage-route-table).

<!-- LINKS - internal -->
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
