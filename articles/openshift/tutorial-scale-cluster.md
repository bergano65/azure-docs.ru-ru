---
title: Руководство. Масштабирование кластера Azure Red Hat OpenShift
description: Узнайте, как выполнить масштабирование кластера Microsoft Azure Red Hat OpenShift с помощью Azure CLI.
author: jimzim
ms.author: jzim
ms.topic: tutorial
ms.service: container-service
ms.date: 05/06/2019
ms.openlocfilehash: bf9172f0c84834c951446520ff0bfcc3ef756c9c
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/19/2020
ms.locfileid: "76278310"
---
# <a name="tutorial-scale-an-azure-red-hat-openshift-cluster"></a>Руководство. масштабированию кластера Azure Red Hat OpenShift

Это руководство представляет собой вторую часть цикла. Из него вы узнаете, как создать кластер Microsoft Azure Red Hat OpenShift с помощью Azure CLI, выполнить его масштабирование, а затем удалить, чтобы очистить ресурсы.

Из второй части цикла вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * Масштабирование кластера Red Hat OpenShift

Из этого цикла руководств вы узнаете, как выполнять следующие задачи:
> [!div class="checklist"]
> * [Создание кластера Azure Red Hat OpenShift](tutorial-create-cluster.md)
> * масштабированию кластера Azure Red Hat OpenShift
> * [Удаление кластера Azure Red Hat OpenShift](tutorial-delete-cluster.md)

## <a name="prerequisites"></a>Предварительные требования

Перед началом работы с этим руководством выполните следующие действия:

* Создайте кластер Azure Red Hat OpenShift, следуя инструкциям из [этого руководства](tutorial-create-cluster.md).

## <a name="step-1-sign-in-to-azure"></a>Шаг 1. Вход в Azure

Если интерфейс Azure CLI запущен локально, выполните командлет `az login`, чтобы войти в Azure.

```bash
az login
```

Если у вас есть доступ к нескольким подпискам, выполните команду `az account set -s {subscription ID}`, заменив `{subscription ID}` необходимой подпиской.

## <a name="step-2-scale-the-cluster-with-additional-nodes"></a>Шаг 2. Масштабирование кластера с добавлением дополнительных узлов

В окне терминала Bash присвойте переменной CLUSTER_NAME имя своего кластера:

```bash
CLUSTER_NAME=yourclustername
```

Теперь с помощью Azure CLI давайте выполним масштабирование кластера до пяти узлов:

```bash
az openshift scale --resource-group $CLUSTER_NAME --name $CLUSTER_NAME --compute-count 5
```

Через несколько минут выполнение командлета `az openshift scale` завершится и возвратится документ JSON, содержащий сведения о масштабируемом кластере.

## <a name="next-steps"></a>Дальнейшие действия

В этой части руководства вы узнали, как выполнить следующие действия:

> [!div class="checklist"]
> * масштабированию кластера Azure Red Hat OpenShift

Перейдите к следующему руководству:
> [!div class="nextstepaction"]
> [Удаление кластера Azure Red Hat OpenShift](tutorial-delete-cluster.md)
