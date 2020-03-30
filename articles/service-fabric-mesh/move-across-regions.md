---
title: Перемещение приложения Service Fabric Mesh в другой регион
description: Ресурсы Службы Fabric Mesh можно переместить, развернув копию текущего шаблона в новый регион Azure.
author: erikadoyle
ms.author: edoyle
ms.topic: how-to
ms.date: 01/14/2020
ms.custom: subject-moving-resources
ms.openlocfilehash: 376808a6d8f61d4dc03d17061323a473d48053a6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76908167"
---
# <a name="move-a-service-fabric-mesh-application-to-another-azure-region"></a>Перемещение приложения Service Fabric Mesh в другой регион Azure

В этой статье описывается, как переместить приложение Service Fabric Mesh и его ресурсы в другой регион Azure. Вы можете переместить свои ресурсы в другой регион по ряду причин. Например, в ответ на перебои, для получения функций или услуг, доступных только в конкретных регионах, для удовлетворения требований внутренней политики и управления, или в ответ на требования к планированию потенциала.

 [Служба Fabric Mesh не поддерживает](../azure-resource-manager/management/region-move-support.md#microsoftservicefabricmesh) возможность прямого перемещения ресурсов в регионах Azure. Однако можно косвенно перемещать ресурсы, развертывая копию текущего шаблона управления ресурсами Azure в новый целевой регион, а затем перенаправляя трафик и зависимости входа в недавно созданное приложение Service Fabric Mesh.

## <a name="prerequisites"></a>Предварительные требования

* Контроллер Ingress (например, [Application Gateway)](https://docs.microsoft.com/azure/application-gateway/)в качестве посредника для реаутирования трафика между клиентами и вашим приложением Service Fabric Mesh
* Доступность службы Fabric Mesh (Preview) в`westus`целевом регионе Azure (, `eastus`или `westeurope`)

## <a name="prepare"></a>Подготовка.

1. Сфотографируйте текущее состояние приложения Service Fabric Mesh, экспортируя шаблон и параметры менеджера ресурсов Azure из последнего развертывания. Для этого выполните действия в [шаблоне Экспорт после развертывания](../azure-resource-manager/templates/export-template-portal.md#export-template-after-deployment) с помощью портала Azure. Вы также можете использовать [Azure CLI,](../azure-resource-manager/management/manage-resource-groups-cli.md#export-resource-groups-to-templates) [Azure PowerShell](../azure-resource-manager/management/manage-resource-groups-powershell.md#export-resource-groups-to-templates)или [REST API.](https://docs.microsoft.com/rest/api/resources/resourcegroups/exporttemplate)

2. Если это применимо, [экспортируйте другие ресурсы в той же группе ресурсов](https://docs.microsoft.com/azure/azure-resource-manager/templates/export-template-portal#export-template-from-a-resource-group) для передислокации в целевом регионе.

3. Просмотрите (и отспособите, при необходимости) экспортированный шаблон, чтобы убедиться, что существующие значения свойств являются теми, которые вы хотите использовать в целевом регионе. Новый `location` (Azure region) — это параметр, который вы будете поставлять во время передислокации.

## <a name="move"></a>Переместить

1. Создание новой группы ресурсов (или использование существующей) в целевом регионе.

2. С помощью экспортируемого шаблона выполните шаги в [ресурсах Развертывание из пользовательского шаблона](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-portal#deploy-resources-from-custom-template) с помощью портала Azure. Вы также можете использовать [Azure CLI,](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-cli) [Azure PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-powershell)или [REST API.](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-rest)

3. Для получения рекомендаций по перемещению связанных ресурсов, таких как [учетные записи хранения azure,](../storage/common/storage-account-move.md)обратитесь к рекомендациям для отдельных служб, перечисленных в теме [Перемещение ресурсов Azure в разных регионах.](../azure-resource-manager/management/move-region.md)

## <a name="verify"></a>Проверка

1. По завершении развертывания проверьте конечную точку приложения(ы) для проверки функциональности приложения.

2. Вы также можете проверить состояние приложения, проверив состояние приложения[(приложение az mesh show)](https://docs.microsoft.com/cli/azure/ext/mesh/mesh/app?view=azure-cli-latest#ext-mesh-az-mesh-app-show)и просмотрев журналы приложений и[(az mesh code-package-log)](https://docs.microsoft.com/cli/azure/ext/mesh/mesh/code-package-log?view=azure-cli-latest)команды с помощью [Azure Service Fabric Mesh CLI.](https://docs.microsoft.com/azure/service-fabric-mesh/service-fabric-mesh-quickstart-deploy-container#set-up-service-fabric-mesh-cli)

## <a name="commit"></a>Commit

После подтверждения эквивалентной функциональности приложения Service Fabric Mesh в целевом регионе настройте контроллер входа (например, [Application Gateway),](../application-gateway/redirect-overview.md)чтобы перенаправить трафик в новое приложение.

## <a name="clean-up-source-resources"></a>Очистка исходных ресурсов

Чтобы завершить перемещение приложения Service Fabric Mesh, [удалите исходное приложение и/или группу родительских ресурсов.](../azure-resource-manager/management/delete-resource-group.md)

## <a name="next-steps"></a>Дальнейшие действия

* [Перемещение ресурсов Azure в разных регионах](../azure-resource-manager/management/move-region.md)
* [Поддержка перемещения ресурсов Azure в разных регионах](../azure-resource-manager/management/region-move-support.md)
* [Перемещение ресурсов в новую группу ресурсов или подписку](../azure-resource-manager/management/move-resource-group-and-subscription.md)
* [Поддержка операции перемещения для ресурсов](../azure-resource-manager/management/move-support-resources.md
)
