---
title: Обзор выделенных хостов Azure для виртуальных машин
description: Узнайте больше о том, как выделенные хосты Azure могут быть использованы для развертывания виртуальных машин.
author: cynthn
ms.service: virtual-machines-windows
ms.topic: article
ms.workload: infrastructure
ms.date: 01/09/2020
ms.author: cynthn
ms.reviewer: zivr
ms.openlocfilehash: 9709408f006cbcc5786a4e5a854abcd20affd693
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2020
ms.locfileid: "82082838"
---
# <a name="azure-dedicated-hosts"></a>Выделенные хосты Azure

Azure Dedicated Host — это служба, которая предоставляет физические серверы, способные размещать одну или несколько виртуальных машин, предназначенную для одной подписки Azure. Выделенные хосты — это те же физические серверы, которые используются в наших центрах обработки данных, предоставляемые в качестве ресурса. Вы можете предоставить выделенные хосты в пределах области, зоны доступности и домена неисправностей. Затем вы можете размещать ВМ непосредственно в подготовленных хостах, в любой конфигурации лучше всего отвечает вашим потребностям.


[!INCLUDE [virtual-machines-common-dedicated-hosts](../../../includes/virtual-machines-common-dedicated-hosts.md)]

## <a name="next-steps"></a>Дальнейшие действия

- Вы можете развернуть специальный хост с помощью [Azure PowerShell,](dedicated-hosts-powershell.md) [портала](dedicated-hosts-portal.md)и [Azure CLI.](../linux/dedicated-hosts-cli.md)

- Существует образец шаблона, найденный [здесь,](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md)который использует как зоны, так и домены неисправностей для максимальной устойчивости в регионе.

- Вы также можете сэкономить на расходах с [помощью зарезервированных экземпляров выделенных хостов Azure.](../prepay-dedicated-hosts-reserved-instances.md)
