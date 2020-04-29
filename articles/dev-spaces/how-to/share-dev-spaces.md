---
title: Совместное использование Azure Dev Spaces
services: azure-dev-spaces
ms.date: 05/11/2018
ms.topic: conceptual
description: Узнайте, как использовать Azure Dev Spaces для совместного использования пространства разработки в службе Azure Kubernetes с другими пользователями вашей команды
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers, Helm, service mesh, service mesh routing, kubectl, k8s '
ms.openlocfilehash: 0fcb8be5107c7769af7e51dece9f190c8e7e22df
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "79474413"
---
# <a name="share-azure-dev-spaces"></a>Общий доступ к средам разработки в Azure Dev Spaces

С помощью службы Azure Dev Spaces вы можете предоставлять общий доступ к своей среде разработки другим участникам своей команды. Каждый разработчик может работать в своей собственной среде, не прерывая работу других пользователей. Кроме того, совместная работа в одной среде позволяет тестировать код целиком без необходимости создавать макеты или имитировать зависимости. См. [дополнительные сведения о коллективной разработке](../team-development-nodejs.md).

## <a name="set-up-a-dev-space-for-multiple-developers"></a>Настройка среды для нескольких разработчиков

1. Создайте среду разработки в Azure. Выберите [.NET Core и VS Code](../get-started-netcore.md), [.NET Core и Visual Studio](../get-started-netcore-visualstudio.md) или [Node.js и VS Code](../get-started-nodejs.md). Вы должны иметь доступ владельца или участника к выбранной подписке Azure.
1. Убедитесь, что у каждого члена команды есть [соответствующие разрешения для доступа к контроллеру Azure dev Spaces](../troubleshooting.md#incorrect-rbac-permissions-for-calling-dev-spaces-controller-and-apis). Например, можно настроить **группу ресурсов** пространства разработки Azure, чтобы [предоставить доступ участника](/azure/active-directory/role-based-access-control-configure) для каждого участника команды. Вы можете проверить группу ресурсов среды разработки с помощью следующей команды: `azds show-context`
1. Попросите участников команды **выбрать нужную среду разработки **.
   * **Командная строка или VS Code**. Чтобы увидеть существующие среды разработки Azure, к которым у вас есть доступ, выполните команду `azds space list`. Чтобы выбрать среду разработки, выполните команду `azds space select`.
   * **Интегрированная среда разработки Visual Studio **. Откройте проект в Visual Studio, выберите **Azure Dev Spaces** из раскрывающегося списка параметров запуска. В открывшемся диалоговом окне выберите существующий кластер.

     ![Раскрывающийся список параметров запуска в Visual Studio](../media/get-started-netcore-visualstudio/LaunchSettings.png)

## <a name="next-steps"></a>Дальнейшие шаги

См. [дополнительные сведения о коллективной разработке](../team-development-nodejs.md).
