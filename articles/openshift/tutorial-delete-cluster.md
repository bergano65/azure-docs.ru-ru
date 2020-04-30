---
title: Руководство. Удаление кластера Azure Red Hat OpenShift
description: В этом руководстве содержатся сведения об удалении кластера Azure Red Hat OpenShift с помощью Azure CLI.
author: sakthi-vetrivel
ms.author: suvetriv
ms.topic: tutorial
ms.service: container-service
ms.date: 04/24/2020
ms.openlocfilehash: 2de60b90eb6fb75ef013a2fd8785f1b8b616fba6
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82232144"
---
# <a name="tutorial-delete-an-azure-red-hat-openshift-4-cluster"></a>Руководство. Удаление кластера Azure Red Hat OpenShift 4

В этом руководстве, часть 3 из трех, был удален кластер Azure Red Hat OpenShift с OpenShift 4. Вы научитесь:

> [!div class="checklist"]
> * удаление кластера Azure Red Hat OpenShift.


## <a name="before-you-begin"></a>Подготовка к работе

В предыдущих руководствах был создан кластер Azure Red Hat OpenShift и подключен с помощью веб-консоли OpenShift. Если вы не выполнили эти действия и хотите следовать инструкциям, начните с [учебника 1. Создание кластера Azure Red Hat Openshift 4.](tutorial-create-cluster.md)

Если вы решили установить и использовать CLI локально, для работы с этим руководством вам потребуется Azure CLI версии 2.0.75 или более поздней. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="sign-in-to-azure"></a>Вход в Azure

Если интерфейс Azure CLI запущен локально, выполните командлет `az login`, чтобы войти в Azure.

```bash
az login
```

Если у вас есть доступ к нескольким подпискам, выполните команду `az account set -s {subscription ID}`, заменив `{subscription ID}` необходимой подпиской.

## <a name="delete-the-cluster"></a>Удаление кластера

В предыдущих руководствах были заданы следующие переменные. 

```bash
CLUSTER=yourclustername
RESOURCE_GROUP=yourresourcegroup
```

Используя эти значения, удалите кластер:

```bash
az aro delete --resource-group $RESOURCEGROUP --name $CLUSTER
```

После этого вам будет предложено подтвердить удаление кластера. Подтвердите, введя `y`. Удаление кластера займет несколько минут. По завершении выполнения команды вся группа ресурсов и все ресурсы внутри нее (включая кластер) будут удалены.

## <a name="next-steps"></a>Дальнейшие действия

В этой части руководства вы узнали, как выполнить следующие действия:
> [!div class="checklist"]
> * Удаление кластера Azure Red Hat OpenShift 4

Ознакомьтесь с дополнительными сведениями об использовании OpenShift в [документации по Red Hat OpenShift](https://www.openshift.com/try).
