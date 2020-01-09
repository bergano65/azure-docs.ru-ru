---
title: Совместное использование Azure Dev Spaces
services: azure-dev-spaces
ms.date: 05/11/2018
ms.topic: conceptual
description: Узнайте, как использовать Azure Dev Spaces для совместного использования пространства разработки в службе Azure Kubernetes с другими пользователями вашей команды
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers, Helm, service mesh, service mesh routing, kubectl, k8s '
ms.openlocfilehash: 5e3a18ea205eda5617eab094046ec6536e82d113
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75438435"
---
# <a name="share-azure-dev-spaces"></a>Общий доступ к средам разработки в Azure Dev Spaces

С помощью службы Azure Dev Spaces вы можете предоставлять общий доступ к своей среде разработки другим участникам своей команды. Каждый разработчик может работать в своей собственной среде, не прерывая работу других пользователей. Кроме того, совместная работа в одной среде позволяет тестировать код целиком без необходимости создавать макеты или имитировать зависимости. См. [дополнительные сведения о коллективной разработке](../team-development-nodejs.md).

## <a name="set-up-a-dev-space-for-multiple-developers"></a>Настройка среды для нескольких разработчиков

1. Создайте среду разработки в Azure. Выберите [.NET Core и VS Code](../get-started-netcore.md), [.NET Core и Visual Studio](../get-started-netcore-visualstudio.md) или [Node.js и VS Code](../get-started-nodejs.md). Вы должны иметь доступ владельца или участника к выбранной подписке Azure.
1. Настройте **группу ресурсов** среды в Azure Dev Spaces, чтобы [предоставить доступ участника](/azure/active-directory/role-based-access-control-configure) для каждого члена команды. Вы можете проверить группу ресурсов среды разработки с помощью следующей команды: `azds list-up`
1. Попросите участников команды **выбрать нужную среду разработки** .
   * **Командная строка или VS Code**. Чтобы увидеть существующие среды разработки Azure, к которым у вас есть доступ, выполните команду `azds space list`. Чтобы выбрать среду разработки, выполните команду `azds space select`.
   * **Интегрированная среда разработки Visual Studio** . Откройте проект в Visual Studio, выберите **Azure Dev Spaces** из раскрывающегося списка параметров запуска. В открывшемся диалоговом окне выберите существующий кластер.

     ![Раскрывающийся список параметров запуска в Visual Studio](../media/get-started-netcore-visualstudio/LaunchSettings.png)

## <a name="next-steps"></a>Дальнейшие действия

См. [дополнительные сведения о коллективной разработке](../team-development-nodejs.md).
