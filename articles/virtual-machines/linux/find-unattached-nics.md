---
title: Поиск и удаление неподключенных сетевых карт в Azure | Документация Майкрософт
description: Как найти и удалить не подключенные к виртуальным машинам сетевые адаптеры Azure с помощью Azure CLI.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 04/10/2018
ms.author: cynthn
ms.openlocfilehash: dd4fcfe80818bd8e1e87851f4b5131aac73ceeb5
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67671523"
---
# <a name="how-to-find-and-delete-unattached-network-interface-cards-nics-for-azure-vms"></a>Как найти и удалить неподключенные сетевые карты (NIC) на виртуальных машинах Azure
Когда вы удаляете виртуальную машину в Azure, ее сетевые карты не удаляются по умолчанию. При создании и удалении нескольких виртуальных машин неиспользуемые сетевые карты продолжают занимать выделенные им внутренние IP-адреса. Когда вы будете создавать другие сетевые карты виртуальной машины, они, возможно, не смогут получить IP-адрес в адресном пространстве подсети. В этой статье показано, как найти и удалить неподключенные сетевые карты.

## <a name="find-and-delete-unattached-nics"></a>Поиск и удаление неподключенных сетевых адаптеров

В свойстве *virtualMachine* сетевой карты хранится идентификатор и группа ресурсов виртуальной машины, к которой подключена данная сетевая карта. Приведенный ниже скрипт просматривает все сетевые карты в подписке и проверяет, имеет ли свойство *virtualMachine* значение null. Если это свойство имеет значение null, сетевая карта не подключена к виртуальной машине.

Чтобы просмотреть все неподключенные сетевые карты, мы настоятельно рекомендуем сначала выполнить скрипт, задав для переменной *deleteUnattachedNics* значение *0*. Чтобы удалить все неподключенные сетевые карты после просмотра списка всех найденных, запустите скрипт со значением *deleteUnattachedNics* равным *1*.

```azurecli
# Set deleteUnattachedNics=1 if you want to delete unattached NICs
# Set deleteUnattachedNics=0 if you want to see the Id(s) of the unattached NICs
deleteUnattachedNics=0

unattachedNicsIds=$(az network nic list --query '[?virtualMachine==`null`].[id]' -o tsv)
for id in ${unattachedNicsIds[@]}
do
   if (( $deleteUnattachedNics == 1 ))
   then

       echo "Deleting unattached NIC with Id: "$id
       az network nic delete --ids $id
       echo "Deleted unattached NIC with Id: "$id
   else
       echo $id
   fi
done
```

## <a name="next-steps"></a>Следующие шаги

Дополнительные сведения о создании виртуальных сетей и управлении ими в Azure см. в статье [Управление виртуальными сетями Azure и виртуальными машинами Linux с помощью Azure CLI](tutorial-virtual-network.md).
