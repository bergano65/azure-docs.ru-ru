---
title: Развертывание OpenShift в Azure Stack
description: Развертывание OpenShift в Azure Stack.
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
ms.date: 10/14/2019
ms.author: haroldw
ms.openlocfilehash: d6c73b8cd33aa85793a2ce839410065e03b97be7
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74035535"
---
# <a name="deploy-openshift-container-platform-or-okd-in-azure-stack"></a>Развертывание платформы контейнеров OpenShift (OKD) в Azure Stack

Вы можете развернуть OpenShift в Azure Stack. Есть несколько важных различий между Azure и Azure Stack, из-за которых процесс развертывания и функции также будут немного отличаться.

Сейчас поставщик облачных служб Azure не работает в Azure Stack. Это означает, что вы не сможете присоединить диск для организации постоянного хранения в Azure Stack. Вместо этого можно настроить другие параметры хранилища, такие как NFS, iSCSI, Глустерфс и т. д. В качестве альтернативы можно включить CNS и использовать Глустерфс для постоянного хранения. Если включить CNS, для GlusterFS развертываются три дополнительных узла с дополнительным хранилищем.

Для развертывания платформы OpenShift Container Platform (OKD) в Azure Stack можно использовать один из нескольких способов.

- Вы можете вручную развернуть необходимые компоненты инфраструктуры Azure, а затем следовать инструкциям из документации по [платформе контейнеров OKD](https://docs.openshift.com/container-platform) или [OKD](https://docs.okd.io).
- Также можно использовать существующий [шаблон Resource Manager](https://github.com/Microsoft/openshift-container-platform/), упрощающий развертывание кластера платформы OpenShift Container Platform.
- Вы можете воспользоваться [шаблоном Resource Manager](https://github.com/Microsoft/openshift-origin), который упрощает развертывание кластера OKD.

Если вы используете шаблон Resource Manager, выберите соответствующую ветвь (azurestack-release-3.x). Шаблоны для Azure не будут работать, так как Azure и Azure Stack используют разные версии API. Ссылка на образ RHEL сейчас прописана в переменной в файле azuredeploy.json. Ее нужно изменить, чтобы использовать другой образ.

```json
"imageReference": {
    "publisher": "Redhat",
    "offer": "RHEL-OCP",
    "sku": "7-4",
    "version": "latest"
}
```

Для всех вариантов подписка Red Hat является обязательной. Во время развертывания экземпляр Red Hat Enterprise Linux регистрируется в подписке Red Hat и связывается с идентификатором пула, который содержит права доступа к платформе OpenShift Container Platform.
Убедитесь, что у вас есть действительное имя пользователя, пароль и идентификатор пула диспетчера подписки Red Hat (RHSM). Вместо этого набора можно использовать ключ активации, идентификатор организации и идентификатор пула.  Данные можно проверить, войдя на сайт https://access.redhat.com.

## <a name="azure-stack-prerequisites"></a>Необходимые компоненты для Azure Stack

Для развертывания кластера OpenShift к среде Azure Stack необходимо добавить образ RHEL (платформа контейнеров OpenShift) или образ CentOS (OKD). Обратитесь к администратору Azure Stack, чтобы добавить эти образы. Инструкции можно найти здесь:

- https://docs.microsoft.com/azure/azure-stack/azure-stack-add-vm-image
- https://docs.microsoft.com/azure/azure-stack/azure-stack-marketplace-azure-items
- https://docs.microsoft.com/azure/azure-stack/azure-stack-redhat-create-upload-vhd

## <a name="deploy-by-using-the-openshift-container-platform-or-okd-resource-manager-template"></a>Развертывание платформы OpenShift Container Platform (OKD) с помощью шаблона Resource Manager

Для развертывания с использованием шаблона Resource Manager используется файл параметров, в котором указываются входные параметры. Для дополнительной настройки развертывания создайте вилку репозитория GitHub и измените нужные элементы.

Например, достаточно часто возникает потребность изменить следующие параметры:

- размер виртуальной машины-бастиона (переменная в azuredeploy.json);
- соглашения об именовании (переменные в azuredeploy.json);
- особенности кластера OpenShift, изменяемые в файле hosts (deployOpenShift.sh).
- размер образа RHEL (переменная в azuredeploy.json).

Инструкции по развертыванию с помощью Azure CLI вы найдете в соответствующем разделе документации по [платформе контейнеров OpenShift](./openshift-container-platform-3x.md) или [OKD](./openshift-okd.md).

## <a name="next-steps"></a>Дополнительная информация

- [Задачи, выполняемые после развертывания](./openshift-container-platform-3x-post-deployment.md)
- [Устранение неполадок с развертыванием OpenShift в Azure](./openshift-container-platform-3x-troubleshooting.md)