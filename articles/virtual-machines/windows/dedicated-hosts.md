---
title: Общие сведения о выделенных узлах Azure для виртуальных машин | Документация Майкрософт
description: Узнайте больше о том, как можно использовать выделенные узлы Azure для развертывания виртуальных машин.
services: virtual-machines-windows
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 07/25/2019
ms.author: cynthn
ms.openlocfilehash: 011bfeb337f3c04b2d9041abedac50affe1f86b0
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/13/2019
ms.locfileid: "68977265"
---
# <a name="preview-azure-dedicated-hosts"></a>Предварительный просмотр: Выделенные узлы Azure

Выделенный узел Azure — это служба, которая предоставляет физические серверы — возможность размещения одной или нескольких виртуальных машин, выделенных для одной подписки Azure. Выделенные узлы — это те же физические серверы, которые используются в наших центрах обработки данных, предоставляемые в качестве ресурса. Вы можете подготавливать выделенные узлы в регионе, зоне доступности и домене сбоя. Затем можно разместить виртуальные машины непосредственно в подготовленных узлах в любой конфигурации, которая лучше всего соответствует вашим потребностям.

[!INCLUDE [virtual-machines-common-dedicated-hosts-preview](../../../includes/virtual-machines-common-dedicated-hosts-preview.md)]

[!INCLUDE [virtual-machines-common-dedicated-hosts](../../../includes/virtual-machines-common-dedicated-hosts.md)]

## <a name="next-steps"></a>Следующие шаги

- Выделенный узел можно развернуть с помощью [Azure PowerShell](dedicated-hosts-powershell.md), [портала](dedicated-hosts-portal.md)и [Azure CLI](../linux/dedicated-hosts-cli.md).

- [Здесь](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md)приведен пример шаблона, который использует зоны и домены сбоя для максимальной устойчивости в регионе.
