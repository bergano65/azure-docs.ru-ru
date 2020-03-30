---
author: genlin
ms.service: virtual-network
ms.topic: include
ms.date: 02/14/2020
ms.author: genli
ms.openlocfilehash: 280943bd965c4799ce294321129d1088be9c0caf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060471"
---
## <a name="scenario"></a>Сценарий

Чтобы лучше проиллюстрировать, как настроить статический IP-адрес для VM, этот документ использует этот сценарий:

![Виртуальный сетевой сценарий: фронтовые и бэк-энд-подсети, со статическим IP-адресом для подсети front-end](./media/virtual-networks-static-ip-scenario-include/static-ip-scenario.png)

В этом сценарии вы создаете VM подсети *FrontEnd* с названием *DNS01,* а затем устанавливаете его для использования статического IP-адреса *192.168.1.101*.
