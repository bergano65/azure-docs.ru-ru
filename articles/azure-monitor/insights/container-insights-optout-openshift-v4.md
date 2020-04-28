---
title: Как отключить мониторинг кластера Azure и Red Hat OpenShift v4 | Документация Майкрософт
description: В этой статье описывается, как можно отключить мониторинг кластера OpenShift для Azure Red Hat и Red Hat OpenShift версии 4 с Azure Monitor для контейнеров.
ms.topic: conceptual
ms.date: 04/24/2020
ms.openlocfilehash: 768c4db8d72778b555a4f343cf2e23b8fa861991
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82196443"
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

    Пример.

    `helm delete azmon-containers-release-1`

    Выпуск будет удален из кластера. Проверить можно, выполнив `helm list` команду:

    ```
    NAME                            NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                           APP VERSION
    ```

Изменение конфигурации может занять несколько минут. Так как Helm отслеживает выпуски даже после их удаления, можно выполнить аудит журнала кластера и даже отменить удаление выпуска с помощью `helm rollback`.

## <a name="next-steps"></a>Следующие шаги

Если Рабочая область Log Analytics создана только для поддержки мониторинга кластера и больше не нужна, ее необходимо удалить вручную. Если вы не знакомы с удалением рабочей области, см. статью [удаление log Analytics рабочей области Azure](../../log-analytics/log-analytics-manage-del-workspace.md).
