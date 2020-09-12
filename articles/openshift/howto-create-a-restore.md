---
title: Создание восстановления приложения кластера Azure Red Hat OpenShift 4 с помощью Велеро
description: Узнайте, как создать восстановление кластерных приложений Azure Red Hat OpenShift с помощью Велеро.
ms.service: container-service
ms.topic: article
ms.date: 06/22/2020
author: troy0820
ms.author: b-trconn
keywords: aro, openshift, az aro, red hat, cli
ms.custom: mvc
ms.openlocfilehash: 0cd6797bcdfadca807e25f8b3decf34bd553fc56
ms.sourcegitcommit: 4feb198becb7a6ff9e6b42be9185e07539022f17
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/04/2020
ms.locfileid: "89470057"
---
# <a name="create-an-azure-red-hat-openshift-4-cluster-application-restore"></a>Создание восстановления приложения кластера Azure Red Hat OpenShift 4

В этой статье вы подготовите среду для создания восстановления приложения кластера Azure Red Hat OpenShift 4. Вы узнаете, как:

> [!div class="checklist"]
> * Настройка необходимых компонентов и установка необходимых средств
> * Создание приложения Azure Red Hat OpenShift 4 для восстановления

Если вы решили установить и использовать интерфейс командной строки локально, то для работы с этим руководством вам понадобится Azure CLI 2.6.0 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0](/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="before-you-begin"></a>Перед началом

### <a name="create-an-azure-red-hat-openshift-4-application-backup"></a>Создание резервной копии приложений Azure Red Hat OpenShift 4

Сведения о создании резервной копии приложений Azure Red Hat OpenShift 4 см. в статье [Создание резервной копии Azure Red Hat OpenShift 4](./howto-create-a-backup.md) .

## <a name="restore-an-azure-red-hat-openshift-4-application"></a>Восстановление приложения Azure Red Hat OpenShift 4

Эти действия позволяют восстановить приложение, для которого ранее была создана резервная копия с помощью Велеро.
Чтобы узнать, какие резервные копии доступны для восстановления, можно проверить список резервных копий, распознаваемых кластером в настоящее время.  Для выполнения этого шага необходимо выполнить следующую команду:

_(Для этого шага предполагается, что вы установили Велеро в проекте с именем "велеро")._

```bash
oc get backups -n velero
```

После создания резервной копии, которую вы хотите восстановить, необходимо выполнить восстановление с помощью следующей команды:

```bash
velero restore create --from-backup <name of backup from above output list>
```

На этом шаге будут созданы объекты Kubernetes, которые были созданы из предыдущего шага при создании резервной копии.

Чтобы просмотреть состояние восстановления, выполните следующий шаг:

```bash
oc get restore -n velero <name of restore created previously> -o yaml
```
На этом этапе `Completed` необходимо восстановить приложение Azure Red Hat 4.

## <a name="next-steps"></a>Дальнейшие действия

В этой статье было восстановлено приложение кластера Azure Red Hat OpenShift 4. Вы ознакомились с выполнением следующих задач:

> [!div class="checklist"]
> * Создание восстановления приложения кластера OpenShift v4 с помощью Велеро


Перейдите к следующей статье, чтобы узнать о поддерживаемых ресурсах Azure Red Hat OpenShift 4.

* [Поддерживаемые ресурсы Azure Red Hat OpenShift v4](supported-resources.md)