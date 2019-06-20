---
title: включение файла
description: включение файла
services: virtual-network
author: genlin
ms.service: virtual-network
ms.topic: include
ms.date: 04/13/2018
ms.author: genli
ms.custom: include file
ms.openlocfilehash: 2b1f9990985951a4e6ef260954968c0e1466c298
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67185203"
---
## <a name="scenario"></a>Сценарий

Чтобы проиллюстрировать процесс создания виртуальной сети и подсетей, в этом документе будет использоваться представленный ниже сценарий.

![Сценарий виртуальной сети](./media/virtual-networks-create-vnet-scenario-include/vnet-scenario.png)

В этом сценарии вы создадите виртуальную сеть с именем **TestVNet** и зарезервированным блоком CIDR **192.168.0.0./16**. Виртуальная сеть содержит следующие подсети: 

* **FrontEnd** с блоком **192.168.1.0/24** в качестве блока CIDR.
* **BackEnd** с блоком **192.168.2.0/24** в качестве блока CIDR.

