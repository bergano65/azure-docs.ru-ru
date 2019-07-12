---
title: Непрерывность бизнес-процессов и аварийное восстановление в Azure Dev Spaces
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: lisaguthrie
ms.author: lcozzens
ms.date: 01/28/2019
ms.topic: conceptual
description: Быстрая разработка в Kubernetes с использованием контейнеров и микрослужб в Azure
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers, Helm, service mesh, service mesh routing, kubectl, k8s '
manager: gwallace
ms.openlocfilehash: 2da92b4fcd98024ada8d852d65e08fe8c70e3884
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67704057"
---
# <a name="business-continuity-and-disaster-recovery-in-azure-dev-spaces"></a>Непрерывность бизнес-процессов и аварийное восстановление в Azure Dev Spaces

## <a name="review-disaster-recovery-guidance-for-azure-kubernetes-service-aks"></a>Ознакомьтесь с руководством по аварийному восстановлению для Службы Azure Kubernetes (AKS)

Azure Dev Spaces — это функция Службы Azure Kubernetes (AKS). Ознакомьтесь с рекомендациями по аварийному восстановлению в AKS и определите, можно ли их применять к кластерам AKS, используемым для Dev Spaces. Дополнительные сведения см. в статье [Рекомендации по обеспечению непрерывности бизнес-процессов и аварийного восстановления в Службе Azure Kubernetes (AKS)](https://docs.microsoft.com/azure/aks/operator-best-practices-multi-region).

## <a name="enable-dev-spaces-on-aks-clusters-in-different-regions"></a>Включение Dev Spaces в кластерах AKS в разных регионах

Если включить Dev Spaces в кластерах AKS в разных регионах, вы сможете и дальше использовать Dev Spaces сразу после сбоя в каком-либо регионе Azure.

Общие сведения о развертывании Службы Azure Kubernetes в нескольких регионах см. в разделе [Планирование развертывания в нескольких регионах](https://docs.microsoft.com/azure/aks/operator-best-practices-multi-region#plan-for-multiregion-deployment).

Сведения о развертывании кластера AKS, совместимого с Azure Dev Spaces, см. в разделе [Создание кластера Kubernetes с помощью Azure Cloud Shell](https://docs.microsoft.com/azure/dev-spaces/how-to/create-cluster-cloud-shell).

### <a name="enable-dev-spaces-via-the-azure-portal"></a>Включение Dev Spaces с помощью портала Azure

Щелкните элемент навигации **Dev Spaces** в разделе свойств нужного кластера на портале Azure. Затем включите Dev Spaces.

![Включение Dev Spaces с помощью портала Azure](../media/common/enable-dev-spaces.jpg)

Повторите эту операцию для каждого кластера.

### <a name="enable-dev-spaces-via-the-azure-cli"></a>Включение Dev Spaces с помощью интерфейса командной строки Azure

Dev Spaces можно также включить из командной строки:

```cmd
az aks use-dev-spaces -g <resource group name> -n <cluster name>
```

## <a name="deploy-your-teams-baseline-to-each-cluster"></a>Развертывание базового плана вашей команды для каждого кластера

При работе с Dev Spaces все приложение обычно развертывается в родительской среде разработки кластера Kubernetes. По умолчанию используется среда `default`. Исходное развертывание включает все службы и внешние ресурсы, от которых зависят эти службы, включая базы данных или очереди. Это называется *базовым планом*. Настроив базовый план в родительской среде разработки, вы можете переключаться между отдельными службами и выполнять их отладку в пределах дочерних сред разработки.

Вам нужно развернуть самые последние версии набора служб базового плана в кластерах в нескольких регионах. Такой способ обновления служб базового плана гарантирует бесперебойную работу Dev Spaces в случае сбоя в каком-либо регионе Azure. Например, при развертывании базового плана с помощью конвейера CI/CD, измените конвейер так, чтобы развертывание выполнялось в нескольких кластерах в разных регионах.

## <a name="select-the-correct-aks-cluster-to-use-for-dev-spaces"></a>Выбор кластера AKS для использования в Dev Spaces

Настроив резервное копирование кластера резервного копирования с запущенным базовым планом вашей команды, вы сможете быстро выбрать кластер резервного копирования в любое время. Кроме того, можно повторно запускать отдельные службы, с которыми вы работаете в Dev Spaces.

Выберите другой кластер с помощью команды CLI:

```cmd
az aks use-dev-spaces -g <new resource group name> -n <new cluster name>
```

С помощью этой команды вы можете вывести список сред разработки, доступных в новом кластере:

```cmd
azds space list
```

С помощью этой команды вы можете выбрать существующую среду разработки или создать новую:

```cmd
azds space select -n <space name>
```

После выполнения этих команд выбранный кластер и среда разработки будут использоваться для последующих операций CLI и отладки проектов с использованием расширения Visual Studio Code для Azure Dev Spaces.

Если вы используете Visual Studio, можно выбрать нужный кластер в существующем проекте, выполнив следующие действия:

1. Откройте проект в Visual Studio.
1. В обозревателе решений щелкните правой кнопкой мыши проект, затем выберите **Свойства**.
1. В области слева щелкните **Отладка**.
1. На странице свойств отладки щелкните раскрывающийся список **Профиль** и выберите **Azure Dev Spaces**.
1. Нажмите кнопку **Изменить**.
1. В появившемся диалоговом окне выберите требуемый кластер AKS. При необходимости выберите другую среду разработки или создайте новую, указав соответствующий параметр из раскрывающегося списка **Пространство**.

Выбрав нужный кластер и среду разработки, нажмите клавишу F5, чтобы запустить службу в Dev Spaces.

Повторите эти действия для других проектов, в которых также используется исходный кластер.

## <a name="access-a-service-on-a-backup-cluster"></a>Доступ к службе в кластере резервного копирования

Если вы настроили для службы общедоступное DNS-имя, при запуске в кластере резервного копирования эта служба будет иметь другой URL-адрес. Общедоступные DNS-имена всегда имеют формат `<space name>.s.<root space name>.<service name>.<cluster GUID>.<region>.azds.io`. Если переключиться на другой кластер, изменится GUID кластера и, возможно, регион Azure.

В Dev Spaces всегда отображается правильный URL-адрес службы при запуске `azds up`. Также его можно проверить в окне вывода Visual Studio в разделе **Azure Dev Spaces**.

Проверить URL-адрес можно также, выполнив команду `azds list-uris`:
```
$ azds list-uris
Uri                                                     Status
------------------------------------------------------  ---------
http://default.mywebapi.d05afe7e006a4fddb73c.eus.azds.io/  Available
```

Воспользуйтесь этим URL-адресом, чтобы получить доступ к службе.
