---
title: Как отключить мониторинг кластера Azure и Red Hat OpenShift v4 | Документация Майкрософт
description: В этой статье описывается, как можно отключить мониторинг кластера OpenShift для Azure Red Hat и Red Hat OpenShift версии 4 с Azure Monitor для контейнеров.
ms.topic: conceptual
ms.date: 04/24/2020
ms.openlocfilehash: bf61457b9c8cff40eb3fee2c93c7184fbaae6db5
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100625977"
---
# <a name="how-to-stop-monitoring-your-azure-and-red-hat-openshift-v4-cluster"></a>Как отключить мониторинг кластера Azure и Red Hat OpenShift v4

После включения мониторинга в кластере Azure Red Hat OpenShift и Red Hat OpenShift версии 4. x можно отключить мониторинг кластера с Azure Monitor для контейнеров, если вы решите, что вы больше не хотите его отслеживать. В этой статье показано, как это сделать.  

## <a name="how-to-stop-monitoring-using-helm"></a>Как прерывать мониторинг с помощью Helm

1. Чтобы сначала найти Azure Monitor для контейнеров Helm, установленных в кластере, выполните следующую команду Helm.

    ```
    helm list
    ```

    Результат должен выглядеть так:

    ```
    NAME                            NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                           APP VERSION
    azmon-containers-release-1      default         3               2020-04-21 15:27:24.1201959 -0700 PDT   deployed        azuremonitor-containers-2.7.0   7.0.0-1
    ```

    *Азмон-Containers-Release-1* представляет выпуск Helm chart для Azure Monitor для контейнеров.

2. Чтобы удалить выпуск диаграммы, выполните следующую команду Helm.

    `helm delete <releaseName>`

    Пример

    `helm delete azmon-containers-release-1`

    Выпуск будет удален из кластера. Проверить можно, выполнив `helm list` команду:

    ```
    NAME                            NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                           APP VERSION
    ```

Изменение конфигурации может занять несколько минут. Так как Helm отслеживает выпуски даже после их удаления, можно выполнить аудит журнала кластера и даже отменить удаление выпуска с помощью `helm rollback` .

## <a name="next-steps"></a>Дальнейшие шаги

Если Рабочая область Log Analytics создана только для поддержки мониторинга кластера и больше не нужна, ее необходимо удалить вручную. Если вы не знакомы с удалением рабочей области, см. статью [удаление log Analytics рабочей области Azure](../platform/delete-workspace.md).
