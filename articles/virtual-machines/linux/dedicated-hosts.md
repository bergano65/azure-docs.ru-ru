---
title: Общие сведения о выделенных узлах Azure для виртуальных машин
description: Узнайте больше о том, как можно использовать выделенные узлы Azure для развертывания виртуальных машин.
author: cynthn
ms.service: virtual-machines
ms.topic: article
ms.date: 01/09/2020
ms.author: cynthn
ms.openlocfilehash: 7e19f74c40bbeb83c9230e620c4488778823ef99
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86510809"
---
# <a name="azure-dedicated-hosts-for-virtual-machines"></a>Выделенные узлы Azure для виртуальных машин

Выделенный узел Azure — это служба, предоставляющая выделенные для одной подписки Azure физические серверы, способные вместить одну или несколько виртуальных машин. Выделенные узлы — это те же физические серверы, которые используются в наших центрах обработки данных, предоставляемые в качестве ресурса. Вы можете подготавливать выделенные узлы в пределах региона, зоны доступности и домена сбоя. Затем вы можете разместить в подготовленных узлах виртуальные машины с любой конфигурацией, которая соответствует вашим потребностям.



[!INCLUDE [virtual-machines-common-dedicated-hosts](../../../includes/virtual-machines-common-dedicated-hosts.md)]


## <a name="next-steps"></a>Дальнейшие действия

- Выделенный узел можно развернуть с помощью [Azure CLI](dedicated-hosts-cli.md), [портала](dedicated-hosts-portal.md)и [PowerShell](../windows/dedicated-hosts-powershell.md).

- Дополнительные сведения см. в [обзоре выделенных узлов](dedicated-hosts.md).

- [Здесь](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md) можно найти пример шаблона, в котором используются как зоны, так и домены сбоя для обеспечения максимальной устойчивости в регионе.

- Вы также можете сэкономить на затратах с помощью [зарезервированного экземпляра выделенных узлов Azure](../prepay-dedicated-hosts-reserved-instances.md).
