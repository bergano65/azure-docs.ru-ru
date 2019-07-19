---
title: Примеры шаблонов Azure Resource Manager для службы "Экземпляры контейнеров Azure"
description: Примеры шаблонов Azure Resource Manager для службы "Экземпляры контейнеров Azure"
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: article
ms.date: 03/07/2019
ms.author: danlep
ms.openlocfilehash: 2089f024e1de2e92f6e401549c5876e26db17ebb
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/18/2019
ms.locfileid: "68325699"
---
# <a name="azure-resource-manager-templates-for-azure-container-instances"></a>Шаблоны Azure Resource Manager для службы "Экземпляры контейнеров Azure"

Следующие примеры шаблонов развертывают экземпляры контейнеров в различных конфигурациях.

Варианты развертывания см. в разделе [Развертывание](#deployment). Если вы хотите создать собственные шаблоны, службы "экземпляры контейнеров Azure" [Диспетчер ресурсов шаблон][ref] "сведения об образце шаблона" и доступные свойства.

## <a name="sample-templates"></a>Примеры шаблонов

| | |
|-|-|
| **Приложения** ||
| [WordPress][app-wp] | Создает веб-сайт WordPress и его базу данных MySQL в группе контейнеров. Содержимое сайта WordPress и база данных MySQL сохраняются в общей папке Azure. Также создает шлюз приложений для предоставления доступа к общедоступной сети WordPress. |
| [MS NAV с SQL Server и IIS][app-nav] | Развертывает один контейнер Windows с полнофункциональной автономной средой Dynamics NAV или Dynamics 365 Business Central. |
| **Тома** ||
| [emptyDir][vol-emptydir] | Развертывает два контейнера Linux, совместно использующих том emptyDir. |
| [gitRepo][vol-gitrepo] | Развертывает контейнер Linux, который клонирует репозиторий GitHub и подключает его в качестве тома. |
| [secret][vol-secret] | Развертывает контейнер Linux с использованием сертификата PFX, подключенного в качестве секретного тома. |
| **Сеть** ||
| [Контейнер, доступный по протоколу UDP][net-udp] | Развертывает контейнер Windows или Linux, который обеспечивает доступ к UDP-порту. |
| [Контейнер Linux с общедоступным IP-адресом][net-publicip] | Развертывает один контейнер Linux, доступный через общедоступный IP-адрес. |
| [Развертывание группы контейнеров с виртуальной сетью (Предварительная версия)][net-vnet] | Развертывает новую виртуальную сеть, подсеть, сетевой профиль и группу контейнеров. |
| **Ресурсы Azure** ||
| [Создание учетной записи хранения Azure и общего файлового ресурса][az-files] | Создает учетную запись хранения и общую папку Azure в экземпляре контейнера с помощью Azure CLI.

## <a name="deployment"></a>Развертывание

Для развертывания ресурсов с помощью шаблонов Resource Manager доступны несколько вариантов:

[Интерфейс командной строки Azure][deploy-cli]

[Azure PowerShell][deploy-powershell]

[портал Azure][deploy-portal]

[REST API][deploy-rest]

<!-- LINKS - External -->
[app-nav]: https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-dynamicsnav
[app-wp]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-wordpress
[az-files]: https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-storage-file-share
[net-publicip]: https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-linuxcontainer-public-ip
[net-udp]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-udp
[net-vnet]: https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-vnet
[repo]: https://github.com/Azure/azure-quickstart-templates
[vol-emptydir]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-linuxcontainer-volume-emptydir
[vol-gitrepo]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-linuxcontainer-volume-gitrepo
[vol-secret]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-linuxcontainer-volume-secret

<!-- LINKS - Internal -->
[deploy-cli]: ../azure-resource-manager/resource-group-template-deploy-cli.md
[deploy-portal]: ../azure-resource-manager/resource-group-template-deploy-portal.md
[deploy-powershell]: ../azure-resource-manager/resource-group-template-deploy.md
[deploy-rest]: ../azure-resource-manager/resource-group-template-deploy-rest.md
[ref]: /azure/templates/microsoft.containerinstance/containergroups
