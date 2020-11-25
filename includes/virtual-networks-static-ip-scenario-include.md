---
author: genlin
ms.service: virtual-network
ms.topic: include
ms.date: 02/14/2020
ms.author: genli
ms.openlocfilehash: 280943bd965c4799ce294321129d1088be9c0caf
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/25/2020
ms.locfileid: "96015936"
---
## <a name="scenario"></a>Сценарий

Чтобы лучше продемонстрировать, как настроить статический IP-адрес для виртуальной машины, в этом документе используется следующий сценарий:

![Сценарий виртуальной сети: клиентские и фоновые подсети со статическим IP-адресом для интерфейсной подсети.](./media/virtual-networks-static-ip-scenario-include/static-ip-scenario.png)

В этом сценарии вы создадите виртуальную машину с именем *DNS01* в *интерфейсной* подсети, а затем установите для нее статический IP-адрес *192.168.1.101*.
