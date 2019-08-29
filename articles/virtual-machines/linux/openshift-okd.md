---
title: Развертывание OKD в Azure | Документация Майкрософт
description: Развертывание OKD в Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: joraio
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/02/2019
ms.author: haroldw
ms.openlocfilehash: fccb77110eafa131733ecea70fb209b2a168436c
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70082501"
---
# <a name="deploy-okd-in-azure"></a>Развертывание OKD в Azure

Существует два способа развертывания OKD (ранее OpenShift Origin) в Azure.

- Вы можете вручную развернуть все необходимые компоненты инфраструктуры Azure, а затем следовать инструкциям из [документации](https://docs.okd.io) по источнику OKD.
- Вы можете воспользоваться [шаблоном Resource Manager](https://github.com/Microsoft/openshift-origin), который упрощает развертывание кластера OKD.

## <a name="deploy-using-the-okd-template"></a>Развертывание с помощью шаблона OKD

Для развертывания с использованием шаблона Resource Manager используется файл параметров, в котором указываются входные параметры. Для дополнительной настройки развертывания создайте вилку репозитория GitHub и измените нужные элементы.

Например, достаточно часто возникает потребность изменить следующие параметры:

- размер виртуальной машины-бастиона (переменная в azuredeploy.json);
- соглашения об именовании (переменные в azuredeploy.json);
- особенности кластера OpenShift, изменяемые в файле hosts (deployOpenShift.sh).

[Шаблон OKD](https://github.com/Microsoft/openshift-origin) содержит несколько ветвей, доступных для разных версий OKD.  В соответствии с потребностями вы можете выполнять развертывание непосредственно из репозитория или создать вилку репозитория, чтобы вносить изменения перед развертыванием.

Используйте для параметра `aadClientId` значение `appId` из созданного ранее субъекта-службы.

Следующий примере создает файл параметров с именем azuredeploy.parameters.json, который содержит все необходимые входные данные.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "masterVmSize": {
            "value": "Standard_E2s_v3"
        },
        "infraVmSize": {
            "value": "Standard_E2s_v3"
        },
        "nodeVmSize": {
            "value": "Standard_E2s_v3"
        },
        "storageKind": {
            "value": "managed"
        },
        "openshiftClusterPrefix": {
            "value": "mycluster"
        },
        "masterInstanceCount": {
            "value": 3
        },
        "infraInstanceCount": {
            "value": 2
        },
        "nodeInstanceCount": {
            "value": 2
        },
        "dataDiskSize": {
            "value": 128
        },
        "adminUsername": {
            "value": "clusteradmin"
        },
        "openshiftPassword": {
            "value": "{Strong Password}"
        },
        "sshPublicKey": {
            "value": "{SSH Public Key}"
        },
        "enableMetrics": {
            "value": "true"
        },
        "enableLogging": {
            "value": "false"
        },
        "keyVaultResourceGroup": {
            "value": "keyvaultrg"
        },
        "keyVaultName": {
            "value": "keyvault"
        },
        "keyVaultSecret": {
            "value": "keysecret"
        },
        "enableAzure": {
            "value": "true"
        },
        "aadClientId": {
            "value": "11111111-abcd-1234-efgh-111111111111"
        },
        "aadClientSecret": {
            "value": "{Strong Password}"
        },
        "defaultSubDomainType": {
            "value": "nipio"
        }
    }
}
```

Замените указанные параметры соответствующими данными для своей системы.

Разные выпуски могут иметь разные параметры, поэтому обязательно проверьте необходимые параметры для используемой ветви.

### <a name="deploy-using-azure-cli"></a>Развертывание с помощью Azure CLI


> [!NOTE] 
> Для выполнения следующей команды необходим интерфейс командной строки версии не ниже 2.0.8. Узнать свою версию CLI можно с помощью команды `az --version`. Чтобы обновить версию CLI, ознакомьтесь со статьей [Установка Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

Следующий пример развертывает кластер OKD и все связанные ресурсы в группу ресурсов с именем openshiftrg, созданную в развертывании с именем myOpenShiftCluster. Этот шаблон применяется непосредственно из репозитория GitHub через ссылку в локальном файле параметров azuredeploy.parameters.json.

```azurecli 
az group deployment create -g openshiftrg --name myOpenShiftCluster \
      --template-uri https://raw.githubusercontent.com/Microsoft/openshift-origin/master/azuredeploy.json \
      --parameters @./azuredeploy.parameters.json
```

Развертывание длится не менее 30 минут в зависимости от общего количества развертываемых узлов. После завершения развертывания в терминале отобразятся URL-адрес консоли OpenShift и DNS-имя главного узла OpenShift. Также можно просмотреть раздел выходных данных для развертывания на портале Azure.

```json
{
  "OpenShift Console Url": "http://openshiftlb.cloudapp.azure.com/console",
  "OpenShift Master SSH": "ssh -p 2200 clusteradmin@myopenshiftmaster.cloudapp.azure.com"
}
```

Если вы не хотите, чтобы окно командной строки оставалось ожидать завершения развертывания, добавьте `--no-wait` в число параметров при развертывании группы. Выходные данные из развертывания можно получить на портале Azure в разделе развертывания для группы ресурсов.

## <a name="connect-to-the-okd-cluster"></a>Подключение к кластеру OKD

После завершения развертывания подключитесь к консоли OpenShift через браузер, используя `OpenShift Console Url`. Кроме того вы можете создать SSH-подключение к главному узлу OKD. Ниже приведен пример, использующий выходные данные из развертывания.

```bash
$ ssh -p 2200 clusteradmin@myopenshiftmaster.cloudapp.azure.com
```

## <a name="clean-up-resources"></a>Очистка ресурсов

Если группа ресурсов, кластер OpenShift и все связанные ресурсы больше не нужны, их можно удалить с помощью команды [az group delete](/cli/azure/group).

```azurecli 
az group delete --name openshiftrg
```

## <a name="next-steps"></a>Следующие шаги

- [Задачи, выполняемые после развертывания](./openshift-post-deployment.md)
- [Устранение неполадок с развертыванием OpenShift](./openshift-troubleshooting.md)
- [Начало работы с OKD](https://docs.okd.io)
