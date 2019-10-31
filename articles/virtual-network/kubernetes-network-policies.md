---
title: Политики сети Azure Kubernetes | Документация Майкрософт
description: Сведения о политиках сети Kubernetes, позволяющих защитить кластер Kubernetes.
services: virtual-network
documentationcenter: na
author: aanandr
manager: NarayanAnnamalai
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 9/25/2018
ms.author: aanandr
ms.custom: ''
ms.openlocfilehash: 5a6da7e65a9a3e962a2df37b062792fbb990d04d
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73159692"
---
# <a name="azure-kubernetes-network-policies-overview"></a>Обзор политик сети Azure Kubernetes

Политики сети обеспечивают микросегментацию для модулей pod, так же, как группы безопасности сети (NSG) предоставляют микросегментацию для виртуальных машин. Реализация политик сети Azure поддерживает стандартные спецификации политик сети Kubernetes. Вы можете использовать метки для выбора групп модулей pod и определить список правил для входящего и исходящего трафика, разрешенного для этих модулей. Дополнительные сведения о политиках сети Kubernetes см. в [документации по Kubernetes](https://kubernetes.io/docs/concepts/services-networking/network-policies/).

![Обзор политик сети Kubernetes](./media/kubernetes-network-policies/kubernetes-network-policies-overview.png)

Политики сети Azure работают в сочетании с ПО CNI Azure, которое обеспечивает интеграцию виртуальной сети для контейнеров. Сегодня эта функция поддерживается только для узлов Linux. Для обеспечения фильтрации трафика в реализациях настраиваются правила таблицы IP-адресов Linux на основе определенных политик.

## <a name="planning-security-for-your-kubernetes-cluster"></a>Планирование безопасности для кластера Kubernetes
При реализации безопасности для кластера используйте группы безопасности сети (группы безопасности сети), чтобы отфильтровать трафик с высокой налево, то есть трафик, поступающий в подсеть кластера и исходящий из нее, и использовать политики сети Kubernetes для трафика уровня "Восток-Западная", т. е. трафик между модулями Pod в ваш кластер.

## <a name="using-azure-kubernetes-network-policies"></a>Использование политик сети Azure Kubernetes
Чтобы обеспечить микросегментацию для модулей, политики сети Azure можно использовать следующими способами.

### <a name="acs-engine"></a>Обработчик ACS
Обработчик ACS является средством, которое создает шаблон Azure Resource Manager для развертывания кластера Kubernetes в Azure. Конфигурация кластера указана в файле JSON, который передается в средство при создании шаблона. Полный список поддерживаемых параметров кластера и их описание см. в разделе "Обработчик службы контейнеров Azure Microsoft — определения кластера".

Чтобы включить политики в кластерах, развернутых с помощью обработчика ACS, укажите для параметра networkPolicy в файле определения кластера значение "azure".

#### <a name="example-configuration"></a>Пример конфигурации

Ниже приведен пример конфигурации JSON, где создаются новая виртуальная сеть и подсеть, в которой с помощью Azure CNI развертывается кластер Kubernetes. Рекомендуется использовать Блокнот для изменения файла JSON. 
```json
{
  "apiVersion": "vlabs",
  "properties": {
    "orchestratorProfile": {
      "orchestratorType": "Kubernetes",
      "kubernetesConfig": {
         "networkPolicy": "azure"
       }
    },
    "masterProfile": {
      "count": 1,
      "dnsPrefix": "<specify a cluster name>",
      "vmSize": "Standard_D2s_v3"
    },
    "agentPoolProfiles": [
      {
        "name": "agentpool",
        "count": 2,
        "vmSize": "Standard_D2s_v3",
        "availabilityProfile": "AvailabilitySet"
      }
    ],
   "linuxProfile": {
      "adminUsername": "<specify admin username>",
      "ssh": {
        "publicKeys": [
          {
            "keyData": "<cut and paste your ssh key here>"
          }
        ]
      }
    },
    "servicePrincipalProfile": {
      "clientId": "<enter the client ID of your service principal here >",
      "secret": "<enter the password of your service principal here>"
    }
  }
}

```
### <a name="creating-your-own-kubernetes-cluster-in-azure"></a>Создание собственного кластера Kubernetes в Azure
Реализацию можно использовать для предоставления политик сети для модулей в кластерах Kubernetes, которые вы можете развернуть самостоятельно, не полагаясь на инструменты, такие как обработчик ACS. В этом случае сначала нужно установить подключаемый модуль CNI и включить его на каждой виртуальной машине в кластере. Подробные инструкции см. в разделе [Развертывание подключаемого модуля для кластера Kubernetes](deploy-container-networking.md#deploy-plug-in-for-a-kubernetes-cluster).

После развертывания кластера выполните следующую команду `kubectl`, чтобы загрузить и применить политику сети Azure *daemonset* в кластере.

  ```
  kubectl apply -f https://raw.githubusercontent.com/Azure/acs-engine/master/parts/k8s/addons/kubernetesmasteraddons-azure-npm-daemonset.yaml

  ```
Это решение с открытым исходным кодом, и код доступен в [репозитории работы с контейнером Azure в сети](https://github.com/Azure/azure-container-networking/tree/master/npm).



## <a name="next-steps"></a>Дальнейшие действия
- Дополнительные сведения о [Службе Azure Kubernetes](../aks/intro-kubernetes.md).
-  Дополнительные сведения о [работе с контейнерами в сети](container-networking-overview.md).
- [Развертывание подключаемого модуля](deploy-container-networking.md) для кластеров Kubernetes и контейнеров Docker.
