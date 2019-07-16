---
title: Введение в Azure Dev Spaces
titleSuffix: Azure Dev Spaces
author: zr-msft
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.author: zarhoads
ms.date: 05/07/2019
ms.topic: overview
description: Введение в Azure Dev Spaces
keywords: Docker, Kubernetes, Azure, AKS, Служба Azure Kubernetes, контейнеры, kubectl, k8s
manager: gwallace
ms.openlocfilehash: 33ac5a7aa6d823105b87325ba52aa77cd9b9b3a3
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67706301"
---
# <a name="azure-dev-spaces"></a>Azure Dev Spaces

Azure Dev Spaces предоставляет интерфейс быстрой последовательной разработки Kubernetes для команд, работающих с кластерами Служба Azure Kubernetes (AKS). Вы можете совместно работать с командой в общем кластере AKS. Azure Dev Spaces позволяет тестировать все компоненты приложения в AKS без репликации или макетирования зависимостей. Можно последовательно запускать контейнеры и выполнять их отладку непосредственно из AKS с минимальной настройкой компьютера для разработки.

![](media/azure-dev-spaces/collaborate-graphic.gif)


## <a name="how-azure-dev-spaces-simplifies-kubernetes-development"></a>Как служба Azure Dev Spaces упрощает разработку Kubernetes

Azure Dev Spaces помогает командам сосредоточиться на разработке и быстрой последовательной доработке приложения для микрослужб, позволяя им работать непосредственно со всей архитектурой микрослужб или приложением в AKS. Azure Dev Spaces также дает возможность независимо обновлять части архитектуры микрослужб в режиме изоляции, не влияя на остальную часть кластера AKS или работу других разработчиков. Служба Azure Dev Spaces Azure предназначена для разработки и тестирования в средах для разработки и тестирования низкого уровня, а не для кластеров AKS в рабочей среде.

Так как команды могут работать с всем приложением и совместно работать непосредственно в AKS, служба Azure Dev Spaces:

* сводит к минимуму настройку локального компьютера;
* сокращает время настройки для новых разработчиков в команде;
* увеличивает скорость работы команды благодаря ускоренной итерации;
* уменьшает число избыточных сред разработки и интеграции, так как члены команды могут совместно использовать кластер;
* избавляет от необходимости реплицировать или макетировать зависимости;
* улучшает совместную работу команд разработчиков, а также команд, с которыми они сотрудничают, например команд DevOps.

Azure Dev Spaces предоставляет набор инструментов для создания ресурсов Docker и Kubernetes для ваших проектов. Этот набор инструментов позволяет легко добавлять новые и существующие приложения как в пространство разработки, так и в другие кластеры AKS.

Дополнительные сведения о том, как работает Azure Dev Spaces, см. в разделе [Принципы работы и настройки Azure Dev Spaces][how-dev-spaces-works].

## <a name="supported-regions-and-configurations"></a>Поддерживаемые регионы и конфигурации

Azure Dev Spaces поддерживается только в кластерах AKS в следующих регионах: **восточная часть США**, **восточная часть США 2**, **центральная часть США**, **западная часть США 2**, **Северная Европа**, **Западная Европа**, **южная часть Соединенного Королевства**, **Юго-Восточная Азия**, **Восточная Австралия**, **Центральная Канада** и **Восточная Канада**. Azure Dev Spaces поддерживает [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) и [Visual Studio Code](https://code.visualstudio.com/download) с [расширением Azure Dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds) в Linux, MacOS и Windows 8 или более поздней версии для создания и запуска приложений в AKS. Эта служба также поддерживает [Visual Studio](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs) в Windows 8 или более поздней версии. Чтобы использовать Visual Studio 2019, потребуется рабочая нагрузка для разработки в Azure. Чтобы использовать Visual Studio 2017, потребуется рабочая нагрузка для разработки веб-приложений и [Средства Visual Studio для Kubernetes](https://aka.ms/get-vsk8stools).

## <a name="next-steps"></a>Дополнительная информация

Узнайте больше о быстрой последовательной разработке для команд с помощью Azure Dev Spaces, ознакомившись с кратким руководством по командной разработке.

> [!div class="nextstepaction"]
> [Краткое руководство по командной разработке](quickstart-team-development.md)


[how-dev-spaces-works]: how-dev-spaces-works.md
[team-development-quickstart]: quickstart-team-development.md
