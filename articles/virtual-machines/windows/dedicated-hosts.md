---
title: Общие сведения о выделенных узлах Azure для виртуальных машин
description: Узнайте больше о том, как можно использовать выделенные узлы Azure для развертывания виртуальных машин.
services: virtual-machines-windows
author: cynthn
manager: gwallace
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 01/09/2020
ms.author: cynthn
ms.openlocfilehash: 39d1c0ddb4961800e889346ec110ca629ae73546
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/03/2020
ms.locfileid: "78251567"
---
# <a name="azure-dedicated-hosts"></a>Выделенные узлы Azure

Выделенный узел Azure — это служба, которая предоставляет физические серверы — возможность размещения одной или нескольких виртуальных машин, выделенных для одной подписки Azure. Выделенные узлы — это те же физические серверы, которые используются в наших центрах обработки данных, предоставляемые в качестве ресурса. Вы можете подготавливать выделенные узлы в регионе, зоне доступности и домене сбоя. Затем можно разместить виртуальные машины непосредственно в подготовленных узлах в любой конфигурации, которая лучше всего соответствует вашим потребностям.


[!INCLUDE [virtual-machines-common-dedicated-hosts](../../../includes/virtual-machines-common-dedicated-hosts.md)]

## <a name="next-steps"></a>Дальнейшие действия

- Выделенный узел можно развернуть с помощью [Azure PowerShell](dedicated-hosts-powershell.md), [портала](dedicated-hosts-portal.md)и [Azure CLI](../linux/dedicated-hosts-cli.md).

- [Здесь](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md)приведен пример шаблона, который использует зоны и домены сбоя для максимальной устойчивости в регионе.

- Вы также можете сэкономить на затратах с помощью [зарезервированного экземпляра выделенных узлов Azure](../prepay-dedicated-hosts-reserved-instances.md).
