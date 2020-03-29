---
title: Обзор выделенных хостов Azure для виртуальных машин
description: Узнайте больше о том, как выделенные хосты Azure могут быть использованы для развертывания виртуальных машин.
author: cynthn
ms.service: virtual-machines
ms.topic: article
ms.date: 01/09/2020
ms.author: cynthn
ms.openlocfilehash: c19b3410e106aaf5fed53aba45d06eac6dd9d601
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78970725"
---
# <a name="azure-dedicated-hosts"></a>Выделенные хосты Azure

Azure Dedicated Host — это служба, которая предоставляет физические серверы, способные размещать одну или несколько виртуальных машин, предназначенную для одной подписки Azure. Выделенные хосты — это те же физические серверы, которые используются в наших центрах обработки данных, предоставляемые в качестве ресурса. Вы можете предоставить выделенные хосты в пределах области, зоны доступности и домена неисправностей. Затем вы можете размещать ВМ непосредственно в подготовленных хостах, в любой конфигурации лучше всего отвечает вашим потребностям.



[!INCLUDE [virtual-machines-common-dedicated-hosts](../../../includes/virtual-machines-common-dedicated-hosts.md)]


## <a name="next-steps"></a>Дальнейшие действия

- Вы можете развернуть специальный хост с помощью [Azure CLI,](dedicated-hosts-cli.md) [портала](dedicated-hosts-portal.md)и [PowerShell.](../windows/dedicated-hosts-powershell.md)

- Для получения дополнительной информации смотрите обзор [выделенных хостов.](dedicated-hosts.md)

- Существует образец шаблона, найденный [здесь,](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md)который использует как зоны, так и домены неисправностей для максимальной устойчивости в регионе.

- Вы также можете сэкономить на расходах с [помощью зарезервированных экземпляров выделенных хостов Azure.](../prepay-dedicated-hosts-reserved-instances.md)
