---
title: Общие сведения о выделенных узлах Azure для виртуальных машин
description: Узнайте больше о том, как можно использовать выделенные узлы Azure для развертывания виртуальных машин.
author: cynthn
ms.service: virtual-machines-windows
ms.topic: conceptual
ms.workload: infrastructure
ms.date: 01/09/2020
ms.author: cynthn
ms.reviewer: zivr
ms.openlocfilehash: 327eebb4ea1804fc19c8f1b82a57dd0a9a994803
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87284580"
---
# <a name="azure-dedicated-hosts"></a>Выделенные узлы Azure

Выделенный узел Azure — это служба, предоставляющая выделенные для одной подписки Azure физические серверы, способные вместить одну или несколько виртуальных машин. Выделенные узлы — это те же физические серверы, которые используются в наших центрах обработки данных, предоставляемые в качестве ресурса. Вы можете подготавливать выделенные узлы в пределах региона, зоны доступности и домена сбоя. Затем вы можете разместить в подготовленных узлах виртуальные машины с любой конфигурацией, которая соответствует вашим потребностям.


[!INCLUDE [virtual-machines-common-dedicated-hosts](../../../includes/virtual-machines-common-dedicated-hosts.md)]

## <a name="next-steps"></a>Дальнейшие шаги

- Выделенный узел можно развернуть с помощью [Azure PowerShell](dedicated-hosts-powershell.md), [портала](dedicated-hosts-portal.md)и [Azure CLI](../linux/dedicated-hosts-cli.md).

- [Здесь](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md) можно найти пример шаблона, в котором используются как зоны, так и домены сбоя для обеспечения максимальной устойчивости в регионе.

- Вы также можете сэкономить на затратах с помощью [зарезервированного экземпляра выделенных узлов Azure](../prepay-dedicated-hosts-reserved-instances.md).
