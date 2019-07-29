---
title: Пример развертывания скрипта Azure CLI
description: Создание в облаке Azure защищенного кластера Service Fabric под управлением Linux с помощью Azure CLI.
services: service-fabric
documentationcenter: ''
author: athinanthny
manager: chackdan
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 01/18/2018
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: 397ae7ee3b5accbbac1bc17020e6c32397c105a2
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68592389"
---
# <a name="create-a-secure-service-fabric-linux-cluster-in-azure"></a>Создание защищенного кластера Service Fabric под управлением Linux в Azure

Эта команда создает самозаверяющий сертификат, добавляет его в хранилище ключей и скачивает сертификат на локальный компьютер.  Новый сертификат используется для защиты кластера во время развертывания.  Вы также можете использовать уже существующий сертификат.  В любом случае имя субъекта сертификата должно совпадать с доменным именем, которое используется для обращения к кластеру Service Fabric. Это необходимо, чтобы предоставить SSL-протокол конечным точкам управления HTTPS в кластере и Service Fabric Explorer. Не удается получить SSL-сертификат из ЦС для домена `.cloudapp.azure.com`. Необходимо получить имя личного домена для кластера. При запросе сертификата из ЦС имя субъекта сертификата должно совпадать с именем личного домена, используемого для кластера.

При необходимости установите [Azure CLI](/en-us/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="sample-script"></a>Пример скрипта

[!code-sh[main](../../../cli_scripts/service-fabric/create-cluster/create-cluster.sh "Deploy an application to a cluster")]

## <a name="clean-up-deployment"></a>Очистка развертывания

После запуска сценария вы можете удалить группу ресурсов, кластер и все связанные ресурсы, выполнив следующую команду.

```azurecli
ResourceGroupName = "aztestclustergroup"
az group delete --name $ResourceGroupName
```

## <a name="script-explanation"></a>Описание скрипта

Этот скрипт использует следующие команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Команда | Примечания |
|---|---|
| [az sf cluster create](https://docs.microsoft.com/cli/azure/sf/cluster?view=azure-cli-latest) | Создает кластер Service Fabric.  |

## <a name="next-steps"></a>Дополнительная информация

Дополнительные примеры сценариев интерфейса командной строки Service Fabric для Azure Service Fabric см. в статье [Примеры сценариев Azure PowerShell](../samples-cli.md).
