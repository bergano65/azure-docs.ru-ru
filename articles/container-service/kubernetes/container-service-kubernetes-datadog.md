---
title: (УСТАРЕЛО) Мониторинг кластера Kubernetes в Azure с помощью DataDog
description: Мониторинг кластера Kubernetes в Службе контейнеров Azure с помощью DataDog.
services: container-service
author: bburns
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 12/09/2016
ms.author: bburns
ms.custom: mvc
ms.openlocfilehash: 6a682c199b40035bfd44fc5611a7d44b49f7b3ab
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/06/2018
ms.locfileid: "52995351"
---
# <a name="deprecated-monitor-an-azure-container-service-cluster-with-datadog"></a>(УСТАРЕЛО) Мониторинг кластера Службы контейнеров Azure с помощью DataDog

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

## <a name="prerequisites"></a>Предварительные требования
В этом пошаговом руководстве предполагается, что вы [создали кластер Kubernetes с помощью службы контейнеров Azure](container-service-kubernetes-walkthrough.md).

Также предполагается, что у вас установлен интерфейс командной строки Azure `az` и инструменты `kubectl`.

Чтобы проверить наличие средства `az`, выполните такую команду:

```console
$ az --version
```

Если средство `az` не установлено, следуйте инструкциям, приведенным [здесь](https://github.com/azure/azure-cli#installation).

Чтобы проверить наличие средства `kubectl`, выполните такую команду:

```console
$ kubectl version
```

Если средство `kubectl` не установлено, выполните команду:

```console
$ az acs kubernetes install-cli
```

## <a name="datadog"></a>Datadog
Datadog представляет собой службу мониторинга, которая собирает данные мониторинга из контейнеров в кластере службы контейнеров Azure. Datadog имеет панель мониторинга интеграции с Docker, в которой вы можете увидеть некоторые метрики своих контейнеров. Метрики контейнеров собраны в несколько групп: ЦП, память, сеть и ввод-вывод. Datadog разделяет метрики по контейнерам и образам.

Необходимо сначала [создать учетную запись](https://www.datadoghq.com/lpg/).

## <a name="installing-the-datadog-agent-with-a-daemonset"></a>Установка агента DataDog с помощью DaemonSet
Kubernetes использует наборы DaemonSet для выполнения отдельного экземпляра контейнера на каждом узле в кластере.
Они идеально подходят для выполнения агентов мониторинга.

После входа в DataDog можно следовать [инструкциям к DataDog](https://app.datadoghq.com/account/settings#agent/kubernetes), чтобы установить на кластер агенты DataDog с помощью DaemonSet.

## <a name="conclusion"></a>Заключение
Вот и все! Через несколько минут после того, как агенты будут запущены и начнут работать, вы увидите данные в консоли. Вы можете посетить интегрированную [панель мониторинга Kubernetes](https://app.datadoghq.com/screen/integration/kubernetes), чтобы просмотреть сводку по кластеру.
