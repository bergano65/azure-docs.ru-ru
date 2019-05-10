---
title: Руководство по удалению кластера Azure Red Hat OpenShift | Документация Майкрософт
description: В этом руководстве содержатся сведения об удалении кластера Azure Red Hat OpenShift с помощью Azure CLI.
services: container-service
author: tylermsft
ms.author: twhitney
manager: jeconnoc
ms.topic: tutorial
ms.service: openshift
ms.date: 05/06/2019
ms.openlocfilehash: ce4142bdcdfb7a9ab687bb60dca91d6aab00c7bd
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65079419"
---
# <a name="tutorial-delete-an-azure-red-hat-openshift-cluster"></a>Руководство по удалению кластера Azure Red Hat OpenShift

Это последняя часть руководства. После тестирования примера кластера удалите его и связанные с ним ресурсы, чтобы за них больше не взималась плата.

В третьей части цикла вы узнаете, как выполнять такие задачи:

> [!div class="checklist"]
> * удаление кластера Azure Red Hat OpenShift.

Из этого цикла руководств вы узнаете, как выполнять следующие задачи:
> [!div class="checklist"]
> * [Создание кластера Azure Red Hat OpenShift](tutorial-create-cluster.md).
> * [Масштабирование кластера Azure Red Hat OpenShift](tutorial-scale-cluster.md).
> * Удаление кластера Azure Red Hat OpenShift.

## <a name="prerequisites"></a>Предварительные требования

Перед началом работы с этим руководством выполните следующие действия:

* Создайте кластер Azure Red Hat OpenShift, следуя инструкциям из [этого руководства](tutorial-create-cluster.md).

## <a name="step-1-sign-in-to-azure"></a>Шаг 1. Вход в Azure

Если интерфейс Azure CLI запущен локально, выполните командлет `az login`, чтобы войти в Azure.

```bash
az login
```

Если у вас есть доступ к нескольким подпискам, выполните командлет `az account set -s {subscription ID}`, заменив `{subscription ID}` необходимой подпиской.

## <a name="step-2-delete-the-cluster"></a>Шаг 2. Удаление кластера

Откройте терминал Bash и присвойте переменной CLUSTER_NAME имя своего кластера:

```bash
CLUSTER_NAME=yourclustername
```

Теперь удалите кластер.

```bash
az openshift delete --resource-group $CLUSTER_NAME --name $CLUSTER_NAME
```

Появится запрос на подтверждение удаления кластера. Подтвердите, введя `y`. Удаление кластера займет несколько минут. После завершения выполнения команды вся группа ресурсов и ресурсы внутри нее, включая кластер, будут удалены.

## <a name="deleting-a-cluster-using-the-azure-portal"></a>Удаление кластера с помощью портала Azure

Вы также можете удалить связанную группу ресурсов вашего кластера на портале Azure. Имя группы ресурсов совпадает с именем вашего кластера.

В настоящее время ресурс `Microsoft.ContainerService/openShiftManagedClusters`, созданный вместе с кластером, на портале Azure скрыт. В представлении `Resource group` установите флажок `Show hidden types`, чтобы просмотреть группу ресурсов.

![Снимок экрана: флажок для отображения скрытых типов](./media/aro-portal-hidden-type.png)

Удаление группы ресурсов приведет к удалению всех связанных ресурсов, созданных вместе с кластером Azure Red Hat OpenShift.

## <a name="next-steps"></a>Дополнительная информация

В этой части руководства вы узнали, как выполнить следующие действия:
> [!div class="checklist"]
> * Удаление кластера Azure Red Hat OpenShift.

Ознакомьтесь с дополнительными сведениями об использовании OpenShift в [документации по Red Hat OpenShift](https://access.redhat.com/documentation/openshift_dedicated/3/).