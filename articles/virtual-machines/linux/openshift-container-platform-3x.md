---
title: Развертывание контейнерной платформы OpenShift 3.11 в Azure
description: Развертывание контейнерной платформы OpenShift 3.11 в Azure.
author: haroldwongms
manager: mdotson
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.workload: infrastructure
ms.date: 04/05/2020
ms.author: haroldw
ms.openlocfilehash: 7d6cd4c6ce7991ae83f6f4a1dd6d8b86fe7eedbc
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81757897"
---
# <a name="deploy-openshift-container-platform-311-in-azure"></a>Развертывание контейнерной платформы OpenShift 3.11 в Azure

Для развертывания контейнерной платформы OpenShift 3.11 в Azure можно использовать один из нескольких методов:

- Можно вручную развернуть необходимые компоненты инфраструктуры Azure, а затем следовать [документации Платформы контейнеров OpenShift.](https://docs.openshift.com/container-platform)
- Также можно использовать существующий [шаблон Resource Manager](https://github.com/Microsoft/openshift-container-platform/), упрощающий развертывание кластера платформы OpenShift Container Platform.
- Другим вариантом является использование [предложения Azure Marketplace.](https://azuremarketplace.microsoft.com/marketplace/apps/osatesting.open-shift-azure-proxy)

Для всех вариантов подписка Red Hat является обязательной. Во время развертывания экземпляр Red Hat Enterprise Linux регистрируется в подписке Red Hat и связывается с идентификатором пула, который содержит права доступа к платформе OpenShift Container Platform.
Убедитесь, что у вас есть действительное имя пользователя, пароль и идентификатор пула диспетчера подписки Red Hat (RHSM). Можно также использовать ключ активации, идентификатор организации и идентификатор пула. Данные можно проверить, войдя на сайт https://access.redhat.com.


## <a name="deploy-using-the-openshift-container-platform-resource-manager-311-template"></a>Развертывание с помощью шаблона OpenShift Container Platform Resource Manager 3.11

### <a name="private-clusters"></a>Частные кластеры

Развертывание частных кластеров OpenShift требует большего, чем просто отсутствие публичного IP-адреса, связанного с балансируем основной нагрузки (веб-консоль) или балансером нагрузки infra (маршрутизатор).  Частный кластер обычно использует пользовательский DNS-сервер (не DNS по умолчанию), пользовательское доменное имя (например, contoso.com) и заранее определенную виртуальную сеть (ы).  Для частных кластеров необходимо заранее настроить виртуальную сеть со всеми соответствующими подсетями и настройками DNS-сервера.  Затем используйте **существующийMasterSubnetReference**, **существующийInfraSubnetReference**, **existingCnsSubnetReference**, и **existingNodeSubnetReference,** чтобы указать существующую подсеть для использования кластером.

Если выбран частный мастер **(masterClusterType**,private), для **masterPrivateClusterIp**необходимо указать статический частный IP.  Этот IP будет назначен на переднем конце основного баланса нагрузки.  IP должен находиться в пределах CIDR для основной подсети и не использоваться.  **masterClusterDnsType** должен быть установлен на "обычай", а имя мастера DNS должно быть предоставлено для **masterClusterDns.**  Имя DNS должно отображаться на статичном Private IP и будет использоваться для доступа к консоли на главных узлах.

Если выбран частный маршрутизатор **(маршрутизаторClusterType**(частный), для **маршрутизатораPrivateClusterIp**необходимо указать статический частный IP.  Этот IP будет назначен на переднем конце баланса нагрузки infra.  IP должен быть в пределах CIDR для инфра-подсети и не использоваться.  **routingSubDomainType** должен быть установлен на "обычай", а подстановочный знак DNS имя для разгрома должно быть предоставлено для **routingSubDomain**.  

Если выбраны частные мастера и частный маршрутизатор, пользовательское доменное имя также должно быть введено для **domainName**

После успешного развертывания узла Бастиона является единственным узлам с общедоступным IP- испугом, в который можно врезаться.  Даже если мастер-узлы настроены для публичного доступа, они не будут выставлены для доступа ssh.

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
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
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

### <a name="azuredeployparametersjson-file-explained"></a>лазуриции. Параметры.json файл объяснил

| Свойство. | Описание | Допустимые параметры | Значение по умолчанию |
|----------|-------------|---------------|---------------|
| `_artifactsLocation`  | URL для артефактов (json, скрипты и т.д.) |  |  https:\//raw.githubusercontent.com/Microsoft/openshift-container-platform/master  |
| `location` | Azure для развертывания ресурсов для |  |  |
| `masterVmSize` | Размер Мастера VM. Выберите один из допустимых размеров VM, перечисленных в файле azuredeploy.json |  | Standard_E2s_v3 |
| `infraVmSize` | Размер Infra VM. Выберите один из допустимых размеров VM, перечисленных в файле azuredeploy.json |  | Standard_D4s_v3 |
| `nodeVmSize` | Размер узла приложения VM. Выберите один из допустимых размеров VM, перечисленных в файле azuredeploy.json |  | Standard_D4s_v3 |
| `cnsVmSize` | Размер контейнерного хранилища (CNS) Узла VM. Выберите один из допустимых размеров VM, перечисленных в файле azuredeploy.json |  | Standard_E4s_v3 |
| `osImageType` | Изображение RHEL для использования. галерея по умолчанию: по требованию; рынок: изображение сторонних стран | по умолчаниюгалерея <br> marketplace | по умолчаниюгалерея |
| `marketplaceOsImage` | Если `osImageType` это рынок, то введите соответствующие значения для "издателя", "предложение", "sku", "версия" рынка предложение. Этот параметр является типом объекта |  |  |
| `storageKind` | Тип хранилища, который будет использоваться  | Управляемые<br> unmanaged | Управляемые |
| `openshiftClusterPrefix` | Кластерная префикс, используемая для настройки узлов для всех узлов.  От 1 до 20 символов |  | mycluster |
| `minoVersion` | Незначительная версия контейнерной платформы OpenShift 3.11 для развертывания |  | 69 |
| `masterInstanceCount` | Количество узлов мастеров для развертывания | 1, 3, 5 | 3 |
| `infraInstanceCount` | Количество инфра-узлов для развертывания | 1, 2, 3 | 3 |
| `nodeInstanceCount` | Количество узлов для развертывания | 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 24, 25, 26, 27, 28, 29, 30 | 2 |
| `cnsInstanceCount` | Количество узлов ЦНС для развертывания | 3, 4 | 3 |
| `osDiskSize` | Размер диска ОС для VM (в ГБ) | 64, 128, 256, 512, 1024, 2048 | 64 |
| `dataDiskSize` | Размер диска данных для присоединения к узлам для громкости Docker (в ГБ) | 32, 64, 128, 256, 512, 1024, 2048 | 64 |
| `cnsGlusterDiskSize` | Размер диска данных для присоединения к узлам ЦНС для использования глухотами (в ГБ | 32, 64, 128, 256, 512, 1024, 2048 | 128 |
| `adminUsername` | Имя пользователя Admin как для входа в ОС (VM) и начального пользователя OpenShift |  | ocpadmin |
| `enableMetrics` | Включить метрики. Метрики требуют больше ресурсов, поэтому выберите правильный размер для Infra VM | Да <br> false | false |
| `enableLogging` | Включить регистрацию. elasticsearch стручок требует 8 ГБ оперативной памяти, так что выберите правильный размер для Infra VM | Да <br> false | false |
| `enableCNS` | Включить хранилище контейнеров родного | Да <br> false | false |
| `rhsmUsernameOrOrgId` | Имя пользователя или идентификатор организации Менеджера подписки Red Hat |  |  |
| `rhsmPoolId` | Идентификатор менеджера по подписке Red Hat, содержащий права OpenShift на вычислительные узлы |  |  |
| `rhsmBrokerPoolId` | Red Hat Подписка Менеджер бассейн ID, который содержит ваши права OpenShift для мастеров и инфра узлов. Если у вас нет различных идентификаторов пула, введите тот же идентификатор пула, что и 'rhsmPoolId' |  |
| `sshPublicKey` | Копировать ваш SSH Общественный ключ здесь |  |  |
| `keyVaultSubscriptionId` | Идентификатор подписки, содержащий Ключевое Убежище |  |  |
| `keyVaultResourceGroup` | Название Группы ресурсов, содержащей Ключевое Убежище |  |  |
| `keyVaultName` | Название созданного вами Ключевого Убежища |  |  |
| `enableAzure` | Включить облачный провайдер Azure | Да <br> false | Да |
| `aadClientId` | Идентификатор клиента Active Directory Azure также известен как идентификатор приложения для директора службы |  |  |
| `domainName` | Имя пользовательского доменного имени для использования (если это применимо). Установить на "нет", если не развертывание полностью частного кластера |  | none |
| `masterClusterDnsType` | Тип домена для веб-консоли OpenShift. 'по умолчанию' будет использовать DNS ярлык мастер-инфра общего IP. 'таможня' позволяет определить свое собственное имя | default <br> custom | default |
| `masterClusterDns` | Пользовательское имя DNS для доступа к веб-консоли OpenShift, если вы выбрали "обычай" для`masterClusterDnsType` |  | console.contoso.com |
| `routingSubDomainType` | Если установлен на 'nipio', `routingSubDomain` будет использовать nip.io.  Используйте 'таможня', если у вас есть свой собственный домен, который вы хотите использовать для разгрома | нипио <br> custom | нипио |
| `routingSubDomain` | Имя подстановочного знака DNS, которое вы хотите использовать для разгрома, если вы выбрали "обычай" для`routingSubDomainType` |  | apps.contoso.com |
| `virtualNetworkNewOrExisting` | Выберите, следует ли использовать существующую виртуальную сеть или создать новую виртуальную сеть | existing <br> Новые | Новые |
| `virtualNetworkResourceGroupName` | Название группы ресурсов для новой виртуальной сети, если вы выбрали «новый» для`virtualNetworkNewOrExisting` |  | resourceGroup(имя |
| `virtualNetworkName` | Название новой виртуальной сети для создания, если вы выбрали "новый" для`virtualNetworkNewOrExisting` |  | openshiftvnet |
| `addressPrefixes` | Адресная приставка новой виртуальной сети |  | 10.0.0.0/14 |
| `masterSubnetName` | Название мастер-подсети |  | mastersubnet |
| `masterSubnetPrefix` | CIDR, используемый для основной подсети - должен быть подмножеством адресаPrefix |  | 10.1.0.0/16 |
| `infraSubnetName` | Название инфра-подсети |  | инфрасубнет |
| `infraSubnetPrefix` | CIDR, используемый для инфра-подсети - должен быть подмножеством адресаPrefix |  | 10.2.0.0/16 |
| `nodeSubnetName` | Название подсети узлов |  | nodesubnet |
| `nodeSubnetPrefix` | CIDR, используемый для подсети узла - должен быть подмножеством адресаPrefix |  | 10.3.0.0/16. |
| `existingMasterSubnetReference` | Полная ссылка на существующую подсеть для мастер-узлов. Не требуется при создании новых vNet / Subnet |  |  |
| `existingInfraSubnetReference` | Полная ссылка на существующую подсеть для инфраузлов. Не требуется при создании новых vNet / Subnet |  |  |
| `existingCnsSubnetReference` | Полная ссылка на существующую подсеть для узлов ЦНС. Не требуется при создании новых vNet / Subnet |  |  |
| `existingNodeSubnetReference` | Полная ссылка на существующую подсеть для вычислительных узлов. Не требуется при создании новых vNet / Subnet |  |  |
| `masterClusterType` | Укажите, использует ли кластер частные или общедоступные узлы. Если выбран частный, мастер-узлы не будут доступны в Интернете через общественный IP. Вместо этого он будет использовать частный IP, указанный в`masterPrivateClusterIp` | public <br> private | public |
| `masterPrivateClusterIp` | Если выбраны частные узлы мастер-нагрузок, то для использования внутренним балансером нагрузки для мастер-узлов необходимо указать частный IP-адрес. Этот статический IP должен находиться в блоке CIDR для основной подсети и еще не использоваться. Если выбраны общедоступные узлы, это значение не будет использоваться, но должно быть указано |  | 10.1.0.200 |
| `routerClusterType` | Укажите, использует ли кластер частные или общедоступные инфра-узлы. Если выбран частный, инфра-узлы не будут доступны для интернета через общественный IP. Вместо этого он будет использовать частный IP, указанный в`routerPrivateClusterIp` | public <br> private | public |
| `routerPrivateClusterIp` | Если выбраны частные инфра-узлы, то для использования внутренним балансером нагрузки для инфраузлов должен быть указан частный IP-адрес. Этот статический IP должен находиться в блоке CIDR для инфра-подсети и еще не использоваться. Если выбраны общедоступные инфра-узлы, это значение не будет использоваться, но должно быть указано |  | 10.2.0.200 |
| `routingCertType` | Используйте пользовательский сертификат для домена трассировки или сертификат, подписанный по умолчанию - следуйте инструкциям в разделе **Пользовательские сертификаты** | самоподписанный <br> custom | самоподписанный |
| `masterCertType` | Используйте пользовательский сертификат для основного домена или сертификат, подписанный по умолчанию - следуйте инструкциям в разделе **Пользовательские сертификаты** | самоподписанный <br> custom | самоподписанный |

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

Развертывание занимает не менее 60 минут, исходя из общего числа развернутых узлов и настроенных вариантов. После завершения развертывания в терминале отобразятся полное доменное имя DNS узла-бастиона и URL-адрес консоли OpenShift.

```json
{
  "Bastion DNS FQDN": "bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com",
  "OpenShift Console URL": "http://openshiftlb.eastus.cloudapp.azure.com/console"
}
```

Если вы не хотите, чтобы окно командной строки оставалось ожидать завершения развертывания, добавьте `--no-wait` в число параметров при развертывании группы. Выходные данные из развертывания можно получить на портале Azure в разделе развертывания для группы ресурсов.

## <a name="connect-to-the-openshift-cluster"></a>Подключение к кластеру OpenShift

Когда завершится развертывание, получите данные для подключения из раздела выходных данных развертывания. Подключайтесь к консоли OpenShift с помощью браузера с помощью **URL-адреса OpenShift Console.** Вы также можете SSH к хозяину Бастиона. В следующем примере в качестве имени администратора указано clusteradmin, а для полного доменного имени DNS с общедоступным IP-адресом узла-бастиона — bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com.

```bash
$ ssh clusteradmin@bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com
```

## <a name="clean-up-resources"></a>Очистка ресурсов

Если группа ресурсов, кластер OpenShift и все связанные ресурсы больше не нужны, их можно удалить с помощью команды [az group delete](/cli/azure/group).

```azurecli 
az group delete --name openshiftrg
```

## <a name="next-steps"></a>Следующие шаги

- [Задачи, выполняемые после развертывания](./openshift-container-platform-3x-post-deployment.md)
- [Устранение неполадок с развертыванием OpenShift в Azure](./openshift-container-platform-3x-troubleshooting.md)
- [Overview](https://docs.openshift.com) (Обзор)
