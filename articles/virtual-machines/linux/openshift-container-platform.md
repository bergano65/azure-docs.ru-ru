---
title: Развертывание платформы контейнеров OpenShift в Azure | Документация Майкрософт
description: Развертывание платформы контейнеров OpenShift в Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: mdotson
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/18/2019
ms.author: haroldw
ms.openlocfilehash: 296bc42313ef80425004d3c9b43c6792cbaf97f4
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/17/2019
ms.locfileid: "65411561"
---
# <a name="deploy-openshift-container-platform-in-azure"></a>Развертывание платформы контейнеров OpenShift в Azure

Для развертывания платформы OpenShift Container Platform в Azure можно использовать один из нескольких способов:

- Вы можете вручную развернуть необходимые компоненты инфраструктуры Azure, а затем следовать инструкциям из [документации](https://docs.openshift.com/container-platform) по платформе OpenShift Container Platform.
- Также можно использовать существующий [шаблон Resource Manager](https://github.com/Microsoft/openshift-container-platform/), упрощающий развертывание кластера платформы OpenShift Container Platform.
- Другой вариант — использовать [предложение Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/redhat.openshift-container-platform?tab=Overview).

Для всех вариантов подписка Red Hat является обязательной. Во время развертывания экземпляр Red Hat Enterprise Linux регистрируется в подписке Red Hat и связывается с идентификатором пула, который содержит права доступа к платформе OpenShift Container Platform.
Убедитесь, что у вас есть действительное имя пользователя, пароль и идентификатор пула диспетчера подписки Red Hat (RHSM). Можно также использовать ключ активации, идентификатор организации и идентификатор пула. Данные можно проверить, войдя на сайт https://access.redhat.com.


## <a name="deploy-using-the-openshift-container-platform-resource-manager-template"></a>Развертывание платформы контейнеров OpenShift с помощью шаблона Resource Manager

### <a name="private-clusters"></a>Частные кластеров

Развертывание частного OpenShift кластеров, необходимо более просто не иметь общедоступный IP-адрес для подсистемы балансировки нагрузки master (веб-консоли) или к внутренней подсистемы балансировки (маршрутизатор) нагрузки.  Частный кластер обычно используется пользовательский DNS-сервер (не по умолчанию Azure DNS), имя личного домена (например, contoso.com) и предварительно определенных виртуальных сетей.  Для частных кластеров необходимо заранее настроить виртуальную сеть с все соответствующие подсети и параметры DNS-сервера.  Затем с помощью **existingMasterSubnetReference**, **existingInfraSubnetReference**, **existingCnsSubnetReference**, и  **existingNodeSubnetReference** для указания существующей подсети для использования в кластере.

Если установлен флажок закрытый мастер (**masterClusterType**= закрытый), статический частный IP-адрес должен быть указан для **masterPrivateClusterIp**.  Этот IP-адрес будет назначен внешний интерфейс подсистемы балансировки нагрузки master.  IP-адрес должен находиться в пределах CIDR для подсети, master и не используется.  **masterClusterDnsType** должно иметь значение «custom», так и к хозяину, должно быть указано имя DNS для **masterClusterDns**.  DNS-имя должно быть сопоставлено статического частного IP-адреса и будет использоваться для доступа к консоли на главных узлах.

Если выбран закрытый маршрутизатора (**routerClusterType**= закрытый), статический частный IP-адрес должен быть указан для **routerPrivateClusterIp**.  Этот IP-адрес будет назначен интерфейсной части внутренней подсистемы балансировки нагрузки.  IP-адрес должен быть в пределах CIDR для внутренней подсети и не используется.  **routingSubDomainType** должно иметь значение «custom» и имени DNS с подстановочными знаками, для маршрутизации необходимо указать для **routingSubDomain**.  

Если выбрано закрытый образцов и частных маршрутизатор, имя личного домена также должны указываться для **domainName**

После успешного развертывания узел-Бастион это единственный узел с общедоступным IP-адресом, который вы можете ssh в.  Даже если главные узлы настроены для открытого доступа, они не предоставляются для ssh доступа.

Для развертывания с использованием шаблона Resource Manager используется файл параметров, в котором указываются входные параметры. Для дополнительной настройки развертывания создайте вилку репозитория GitHub и измените нужные элементы.

Например, достаточно часто возникает потребность изменить следующие параметры:

- размер виртуальной машины-бастиона (переменная в azuredeploy.json);
- соглашения об именовании (переменные в azuredeploy.json);
- особенности кластера OpenShift, изменяемые в файле hosts (deployOpenShift.sh).

### <a name="configure-the-parameters-file"></a>Настройка файла параметров

[Шаблон платформы контейнеров OpenShift](https://github.com/Microsoft/openshift-container-platform) содержит несколько ветвей для разных версий платформы контейнеров OpenShift.  В соответствии с потребностями вы можете выполнять развертывание непосредственно из репозитория или создать вилку репозитория, чтобы вносить нужные изменения в шаблоны и скрипты перед развертыванием.

Используйте значение `appId` созданной ранее субъект-службы для параметра `aadClientId`.

В примере ниже представлен файл параметров с именем azuredeploy.parameters.json, который содержит все необходимые входные данные.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "_artifactsLocation": {
            "value": "https://raw.githubusercontent.com/Microsoft/openshift-container-platform/master"
        },
        "location": {
            "value": "eastus"
        },
        "masterVmSize": {
            "value": "Standard_E2s_v3"
        },
        "infraVmSize": {
            "value": "Standard_D4s_v3"
        },
        "nodeVmSize": {
            "value": "Standard_D4s_v3"
        },
        "cnsVmSize": {
            "value": "Standard_E4s_v3"
        },
        "osImageType": {
            "value": "defaultgallery"
        },
        "marketplaceOsImage": {
            "value": {
                "publisher": "RedHat",
                "offer": "RHEL",
                "sku": "7-RAW",
                "version": "latest"
            }
        },
        "storageKind": {
            "value": "changeme"
        },
        "openshiftClusterPrefix": {
            "value": "changeme"
        },
        "minorVersion": {
            "value": "69"
        },
        "masterInstanceCount": {
            "value": 3
        },
        "infraInstanceCount": {
            "value": 3
        },
        "nodeInstanceCount": {
            "value": 3
        },
        "cnsInstanceCount": {
            "value": 3
        },
        "osDiskSize": {
            "value": 64
        },
        "dataDiskSize": {
            "value": 64
        },
        "cnsGlusterDiskSize": {
            "value": 128
        },
        "adminUsername": {
            "value": "changeme"
        },
        "enableMetrics": {
            "value": "false"
        },
        "enableLogging": {
            "value": "false"
        },
        "enableCNS": {
            "value": "false"
        },
        "rhsmUsernameOrOrgId": {
            "value": "changeme"
        },
        "rhsmPoolId": {
            "value": "changeme"
        },
        "rhsmBrokerPoolId": {
            "value": "changeme"
        },
        "sshPublicKey": {
            "value": "GEN-SSH-PUB-KEY"
        },
        "keyVaultSubscriptionId": {
            "value": "255a325e-8276-4ada-af8f-33af5658eb34"
        },
        "keyVaultResourceGroup": {
            "value": "changeme"
        },
        "keyVaultName": {
            "value": "changeme"
        },
        "enableAzure": {
            "value": "true"
        },
        "aadClientId": {
            "value": "changeme"
        },
        "domainName": {
            "value": "contoso.com"
        },
        "masterClusterDnsType": {
            "value": "default"
        },
        "masterClusterDns": {
            "value": "console.contoso.com"
        },
        "routingSubDomainType": {
            "value": "nipio"
        },
        "routingSubDomain": {
            "value": "apps.contoso.com"
        },
        "virtualNetworkNewOrExisting": {
            "value": "new"
        },
        "virtualNetworkName": {
            "value": "changeme"
        },
        "addressPrefixes": {
            "value": "10.0.0.0/14"
        },
        "masterSubnetName": {
            "value": "changeme"
        },
        "masterSubnetPrefix": {
            "value": "10.1.0.0/16"
        },
        "infraSubnetName": {
            "value": "changeme"
        },
        "infraSubnetPrefix": {
            "value": "10.2.0.0/16"
        },
        "nodeSubnetName": {
            "value": "changeme"
        },
        "nodeSubnetPrefix": {
            "value": "10.3.0.0/16"
        },
        "existingMasterSubnetReference": {
            "value": "/subscriptions/abc686f6-963b-4e64-bff4-99dc369ab1cd/resourceGroups/vnetresourcegroup/providers/Microsoft.Network/virtualNetworks/openshiftvnet/subnets/mastersubnet"
        },
        "existingInfraSubnetReference": {
            "value": "/subscriptions/abc686f6-963b-4e64-bff4-99dc369ab1cd/resourceGroups/vnetresourcegroup/providers/Microsoft.Network/virtualNetworks/openshiftvnet/subnets/infrasubnet"
        },
        "existingCnsSubnetReference": {
            "value": "/subscriptions/abc686f6-963b-4e64-bff4-99dc369ab1cd/resourceGroups/vnetresourcegroup/providers/Microsoft.Network/virtualNetworks/openshiftvnet/subnets/cnssubnet"
        },
        "existingNodeSubnetReference": {
            "value": "/subscriptions/abc686f6-963b-4e64-bff4-99dc369ab1cd/resourceGroups/vnetresourcegroup/providers/Microsoft.Network/virtualNetworks/openshiftvnet/subnets/nodesubnet"
        },
        "masterClusterType": {
            "value": "public"
        },
        "masterPrivateClusterIp": {
            "value": "10.1.0.200"
        },
        "routerClusterType": {
            "value": "public"
        },
        "routerPrivateClusterIp": {
            "value": "10.2.0.200"
        },
        "routingCertType": {
            "value": "selfsigned"
        },
        "masterCertType": {
            "value": "selfsigned"
        }
    }
}
```

Замените указанные параметры соответствующими данными для своей системы.

Разные выпуски могут иметь разные параметры, поэтому проверьте необходимые параметры для используемой ветви.

### <a name="azuredeployparametersjson-file-explained"></a>azuredeploy. Описание файла Parameters.JSON

| Свойство | Описание | Допустимые значения | Значение по умолчанию |
|----------|-------------|---------------|---------------|
| `_artifactsLocation`  | URL-адрес для артефактов (json, сценарии, и т.д.) |  |  https:\//raw.githubusercontent.com/Microsoft/openshift-container-platform/master  |
| `location` | Регион Azure, следует развернуть ресурсы |  |  |
| `masterVmSize` | Размер главной виртуальной Машине. Выберите один из допустимых размеров виртуальных Машин, перечисленных в файле azuredeploy.json |  | Standard_E2s_v3 |
| `infraVmSize` | Размер инфраструктуры виртуальной Машины. Выберите один из допустимых размеров виртуальных Машин, перечисленных в файле azuredeploy.json |  | Standard_D4s_v3 |
| `nodeVmSize` | Размер виртуальной Машины узла приложения. Выберите один из допустимых размеров виртуальных Машин, перечисленных в файле azuredeploy.json |  | Standard_D4s_v3 |
| `cnsVmSize` | Размер узла контейнера собственный формат хранения (CNS) виртуальной Машины. Выберите один из допустимых размеров виртуальных Машин, перечисленных в файле azuredeploy.json |  | Standard_E4s_v3 |
| `osImageType` | Чтобы использовать образ RHEL. defaultgallery: По запросу; Marketplace: стороннее | defaultgallery <br> marketplace | defaultgallery |
| `marketplaceOsImage` | Если `osImageType` — магазин, а затем введите соответствующие значения для «publisher», «предложения», «sku», «version» предложения marketplace. Этот параметр не является типом объекта |  |  |
| `storageKind` | Тип хранилища для использования  | Управляемые<br> неуправляемые | Управляемые |
| `openshiftClusterPrefix` | Кластера префикс, используемый для настройки имен узлов для всех узлов.  От 1 до 20 символов |  | mycluster |
| `minoVersion` | Дополнительный номер версии 3.11 платформы контейнеров OpenShift для развертывания |  | 69 |
| `masterInstanceCount` | Количество главных серверов узлов для развертывания | 1, 3, 5 | 3 |
| `infraInstanceCount` | Число инфраструктуры узлов для развертывания | 1, 2, 3 | 3 |
| `nodeInstanceCount` | Количество узлов для развертывания | 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 24, 25, 26, 27, 28, 29, 30 | 2 |
| `cnsInstanceCount` | Количество узлов CNS для развертывания | 3, 4 | 3 |
| `osDiskSize` | Размер диска операционной системы для виртуальной Машины (в ГБ) | 64, 128, 256, 512, 1024, 2048 | 64 |
| `dataDiskSize` | Размер диска данных для подключения к узлам для тома Docker (в ГБ) | 32, 64, 128, 256, 512, 1024, 2048 | 64 |
| `cnsGlusterDiskSize` | Размер диска данных, присоединение к узлам CNS для использования с glusterfs (в ГБ | 32, 64, 128, 256, 512, 1024, 2048 | 128 |
| `adminUsername` | Имя пользователя администратора для входа в ОС (VM) и начальное пользователя OpenShift |  | ocpadmin |
| `enableMetrics` | Включите метрики. Метрики требуют больше ресурсов, поэтому выберите нужный размер для инфраструктуры виртуальной Машины | Да <br> false | false |
| `enableLogging` | Включение ведения журнала. elasticsearch pod требуется 8 ГБ ОЗУ, поэтому выберите нужный размер для инфраструктуры виртуальной Машины | Да <br> false | false |
| `enableCNS` | Включить собственный формат хранения контейнера | Да <br> false | false |
| `rhsmUsernameOrOrgId` | Red Hat Subscription Manager имя пользователя или организации идентификатор |  |  |
| `rhsmPoolId` | Идентификатор пула Red Hat Subscription Manager, содержащий на правах OpenShift для вычислительных узлов |  |  |
| `rhsmBrokerPoolId` | Идентификатор пула Red Hat Subscription Manager, содержащего узлы на правах OpenShift для главных серверов и инфраструктуры. Если у вас нет другой пул идентификаторов, введите прежний идентификатор пула «rhsmPoolId» |  |
| `sshPublicKey` | Скопируйте открытого ключа SSH здесь |  |  |
| `keyVaultSubscriptionId` | Идентификатор подписки, которая содержит хранилище ключей |  |  |
| `keyVaultResourceGroup` | Имя группы ресурсов, который содержит хранилище ключей |  |  |
| `keyVaultName` | Имя созданное хранилище Key Vault |  |  |
| `enableAzure` | Включить поставщик облака Azure | Да <br> false | Да |
| `aadClientId` | Azure идентификатор клиента Active Directory также известный как идентификатор приложения для субъекта-службы |  |  |
| `domainName` | Имя пользовательского доменного имени (если применимо). Значение «none» в противном случае развертывание полностью частный кластер. |  | нет |
| `masterClusterDnsType` | Тип домена для веб-консоли OpenShift. «default» будет использовать метку DNS хозяина инфраструктуры общедоступный IP-адрес. «Пользовательская», можно задать собственное имя | значение по умолчанию <br> Custom | значение по умолчанию |
| `masterClusterDns` | Пользовательский DNS-имя для доступа к веб-консоли OpenShift, если выбран «специальный» для `masterClusterDnsType` |  | Console.contoso.com |
| `routingSubDomainType` | Если значение «nipio», `routingSubDomain` будет использовать nip.io.  Используйте «custom», если у вас есть собственный домен, который вы хотите использовать для маршрутизации | nipio <br> Custom | nipio |
| `routingSubDomain` | DNS-имя подстановочный знак, который вы хотите использовать для маршрутизации, если выбран «специальный» для `routingSubDomainType` |  | Apps.contoso.com |
| `virtualNetworkNewOrExisting` | Выберите, следует ли использовать существующую виртуальную сеть или создать новую виртуальную сеть | Существующие <br> new | new |
| `virtualNetworkResourceGroupName` | Имя группы ресурсов для новой виртуальной сети, если вы выбрали «new» для `virtualNetworkNewOrExisting` |  | .name resourceGroup) |
| `virtualNetworkName` | Имя создаваемого, если вы выбрали «new» для новой виртуальной сети `virtualNetworkNewOrExisting` |  | openshiftvnet |
| `addressPrefixes` | Префикс адреса для новой виртуальной сети |  | 10.0.0.0/14 |
| `masterSubnetName` | Имя основной подсети. |  | mastersubnet |
| `masterSubnetPrefix` | CIDR для подсети master - должен представлять собой подмножество addressPrefix |  | 10.1.0.0/16 |
| `infraSubnetName` | Имя внутренней подсети |  | infrasubnet |
| `infraSubnetPrefix` | CIDR используется для внутренней подсети - должно быть подмножество addressPrefix |  | 10.2.0.0/16 |
| `nodeSubnetName` | Имя узла подсети, выполните следующие действия. |  | nodesubnet |
| `nodeSubnetPrefix` | CIDR для подсети узла - должен представлять собой подмножество addressPrefix |  | 10.3.0.0/16. |
| `existingMasterSubnetReference` | Полная ссылка на существующую подсеть для основных узлов. Не требуется, если создание новой виртуальной сети и подсети |  |  |
| `existingInfraSubnetReference` | Инфраструктуры полную ссылку на существующую подсеть для узлов. Не требуется, если создание новой виртуальной сети и подсети |  |  |
| `existingCnsSubnetReference` | Полная ссылка на существующую подсеть для узлов CNS. Не требуется, если создание новой виртуальной сети и подсети |  |  |
| `existingNodeSubnetReference` | Полная ссылка на существующую подсеть для вычислительных узлов. Не требуется, если создание новой виртуальной сети и подсети |  |  |
| `masterClusterType` | Укажите, использует ли кластер частного или общедоступного главных узлов. Если вы выбрали private, главные узлы не будут предоставляться к Интернету через общедоступный IP-адрес. Вместо этого он будет использовать частный IP-адрес, указанный в `masterPrivateClusterIp` | таверна <br> Частный пиринг | таверна |
| `masterPrivateClusterIp` | Если выбраны закрытый главными узлами, затем частный IP-адрес необходимо указать для использования с внутреннего балансировщика нагрузки для главных узлов. Этот статический IP-адрес должен быть в пределах блока CIDR для подсети, основной и еще не используется. Если выбраны открытый главными узлами, это значение не будет использоваться, но по-прежнему должен быть указан |  | 10.1.0.200 |
| `routerClusterType` | Укажите ли кластер использует общедоступный или частный инфраструктуры узлов. Если вы выбрали private, инфраструктуры узлов не будут предоставляться к Интернету через общедоступный IP-адрес. Вместо этого он будет использовать частный IP-адрес, указанный в `routerPrivateClusterIp` | таверна <br> Частный пиринг | таверна |
| `routerPrivateClusterIp` | Если оно является закрытым, инфраструктуры выбираются узлы, а затем частный IP-адрес должен быть указан для используется внутренний балансировщик нагрузки для инфраструктуры узлов. Этот статический IP-адрес должен быть в пределах блока CIDR для подсети, основной и еще не используется. Если открытые инфраструктуры выбираются узлы, это значение не будет использоваться, но по-прежнему должен быть указан |  | 10.2.0.200 |
| `routingCertType` | Использовать пользовательский сертификат для домена маршрутизации или самозаверяющий сертификат по умолчанию — следуйте инструкциям в **пользовательские сертификаты** раздел | selfsigned <br> Custom | selfsigned |
| `masterCertType` | Использовать пользовательский сертификат для главного домена или самозаверяющий сертификат по умолчанию — следуйте инструкциям в **пользовательские сертификаты** раздел | selfsigned <br> Custom | selfsigned |

<br>

### <a name="deploy-using-azure-cli"></a>Развертывание с помощью Azure CLI

> [!NOTE] 
> Для выполнения следующей команды необходим интерфейс командной строки версии не ниже 2.0.8. Узнать свою версию CLI можно с помощью команды `az --version`. Чтобы обновить версию CLI, ознакомьтесь со статьей [Установка Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latesti).

В примере ниже кластер OpenShift и все связанные ресурсы развертываются в группу ресурсов openshiftrg с именем развертывания myOpenShiftCluster. Репозиторий GitHub ссылается непосредственно на шаблон, при этом используется локальный файл параметров с именем azuredeploy.parameters.json.

```azurecli 
az group deployment create -g openshiftrg --name myOpenShiftCluster \
      --template-uri https://raw.githubusercontent.com/Microsoft/openshift-container-platform/master/azuredeploy.json \
      --parameters @./azuredeploy.parameters.json
```

Развертывания требуется по крайней мере 60 минут, в зависимости от общего числа развертываемых узлов и настраиваемые параметры. После завершения развертывания в терминале отобразятся полное доменное имя DNS узла-бастиона и URL-адрес консоли OpenShift.

```json
{
  "Bastion DNS FQDN": "bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com",
  "OpenShift Console URL": "http://openshiftlb.eastus.cloudapp.azure.com/console"
}
```

Если вы не хотите, чтобы окно командной строки оставалось ожидать завершения развертывания, добавьте `--no-wait` в число параметров при развертывании группы. Выходные данные из развертывания можно получить на портале Azure в разделе развертывания для группы ресурсов.

## <a name="connect-to-the-openshift-cluster"></a>Подключение к кластеру OpenShift

Когда завершится развертывание, получите данные для подключения из раздела выходных данных развертывания. Подключение к консоли OpenShift с помощью обозревателя с помощью **URL-адрес консоли OpenShift**. Вы также можете SSH на узел-Бастион. В следующем примере в качестве имени администратора указано clusteradmin, а для полного доменного имени DNS с общедоступным IP-адресом узла-бастиона — bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com.

```bash
$ ssh clusteradmin@bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com
```

## <a name="clean-up-resources"></a>Очистка ресурсов

Если группа ресурсов, кластер OpenShift и все связанные ресурсы больше не нужны, их можно удалить с помощью команды [az group delete](/cli/azure/group).

```azurecli 
az group delete --name openshiftrg
```

## <a name="next-steps"></a>Дальнейшие действия

- [Задачи, выполняемые после развертывания](./openshift-post-deployment.md)
- [Устранение неполадок с развертыванием OpenShift в Azure](./openshift-troubleshooting.md)
- [Overview](https://docs.openshift.com/container-platform) (Обзор)
