---
title: Разрешения плана защиты Azure от атак DDoS
description: Узнайте, как управлять разрешением в плане защиты.
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2020
ms.author: yitoh
ms.openlocfilehash: dec2c845780b62e9a595c73504037142c511c0e6
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2020
ms.locfileid: "94989417"
---
# <a name="manage-ddos-protection-plans-permissions-and-restrictions"></a>Управление планами защиты от атак DDoS: разрешения и ограничения

План защиты от атак DDoS работает в разных регионах и подписках. Один и тот же план можно связать с виртуальными сетями из других подписок в разных регионах по всему клиенту. Для подписки, с которой связан план, выставляется ежемесячный счет за его использование, а также за избыточное использование в случае, если количество защищенных общедоступных IP-адресов превышает 100. Дополнительные сведения о ценах на защиту от атак DDoS приведены [здесь](https://azure.microsoft.com/pricing/details/ddos-protection/).

## <a name="prerequisites"></a>Предварительные требования

- Прежде чем выполнять действия, описанные в этом руководстве, необходимо создать [план защиты Azure от атак DDoS Standard](manage-ddos-protection.md).

## <a name="permissions"></a>Разрешения

Для работы с планами защиты от атак DDoS учетной записи должна быть назначена роль [Участник сетей](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) или [пользовательская](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) роль, которой назначены соответствующие разрешения, перечисленные в таблице ниже.

| Действие                                            | Имя                                     |
| ---------                                         | -------------                            |
| Microsoft.Network/ddosProtectionPlans/read        | Чтение плана защиты от атак DDoS.              |
| Microsoft.Network/ddosProtectionPlans/write       | Создание или обновление плана защиты от атак DDoS.  |
| Microsoft.Network/ddosProtectionPlans/delete      | Удаление плана защиты от атак DDoS.            |
| Microsoft.Network/ddosProtectionPlans/join/action | Присоединение плана защиты от атак DDoS.              |

Чтобы включить защиту от атак DDoS для виртуальной сети, учетной записи также должны быть назначены соответствующие [действия для виртуальных сетей](../virtual-network/manage-virtual-network.md#permissions).

## <a name="azure-policy"></a>Политика Azure

Создание нескольких планов не является обязательным для большинства организаций. План невозможно перемещать между подписками. Если вы хотите изменить подписку, в которой находится план, необходимо удалить существующий план и создать новый.

Для клиентов, имеющих различные подписки и желающих обеспечить развертывание одного плана в клиенте для контроля затрат, можно использовать политику Azure, чтобы [ограничить создание планов Azure от атак DDoS Protection Standard](https://github.com/Azure/Azure-Network-Security/tree/master/Azure%20DDoS%20Protection/Restrict%20creation%20of%20Azure%20DDoS%20Protection%20Standard%20Plans%20with%20Azure%20Policy). Эта политика будет блокировать создание планов от атак DDoS, если подписка ранее не была помечена как исключение. Эта политика также будет показывать список всех подписок, которые развернули план от атак DDoS, но не должны помечать их как несоответствие.


## <a name="next-steps"></a>Дальнейшие действия

Чтобы узнать, как просмотреть и настроить данные телеметрии для плана защиты от атак DDoS, перейдите к руководствам.

> [!div class="nextstepaction"]
> [Настройка телеметрии защиты от атак DDoS и просмотр данных телеметрии](telemetry-monitoring-alerting.md)