---
title: Ip-группы в брандмауэре Azure
description: IP-группы позволяют группировать и управлять IP-адресами для правил Azure Firewall.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 02/18/2020
ms.author: victorh
ms.openlocfilehash: 74e5a427d62d5249ffe6b0426b62a3577e43462f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77444489"
---
# <a name="ip-groups-preview-in-azure-firewall"></a>IP-группы (предварительный просмотр) в Брандмауэре Azure

> [!IMPORTANT]
> Эта общедоступная предварительная версия предоставляется без соглашения об уровне обслуживания и не должна использоваться для производственных рабочих нагрузок. Некоторые функции могут не поддерживаться, иметь ограничения и быть доступными не во всех расположениях Azure. См. [дополнительные условия использования для предварительных версий Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

IP-группы позволяют группировать и управлять IP-адресами для правил Azure Firewall следующим образом:

- Как адрес источника в правилах DNAT
- Как источник или адрес назначения в сетевых правилах
- Как исходный адрес в правилах применения


ГРУППА IP может иметь один IP-адрес, несколько IP-адресов или один или несколько диапазонов IP-адресов.

IP-группы могут быть повторно использованы в Azure Firewall DNAT, сети и приложения для нескольких брандмауэров в разных регионах и подписки в Azure. Названия групп должны быть уникальными. Вы можете настроить ГРУППу IP на портале Azure, Azure CLI или REST API. Образец шаблона предоставляется, чтобы помочь вам начать работу.

## <a name="sample-format"></a>Формат выборки

Следующие примеры формата адресов IPv4 действительны для использования в ip-группах:

- Единый адрес: 10.0.0.0
- Нотация CIDR: 10.1.0.0/32
- Адрес: 10.2.0.0-10.2.0.31

## <a name="create-an-ip-group"></a>Создание группы IP

Группа IP может быть создана с помощью портала Azure, Azure CLI или REST API. Для получения дополнительной информации [см.](create-ip-group.md)

## <a name="browse-ip-groups"></a>Просмотр IP-групп
1. В панели поиска портала Azure введите **IP-группы** и выберите ее. Вы можете увидеть список групп IP, или вы можете выбрать **Добавить** для создания новой группы IP.
2. Выберите ГРУППу IP для открытия страницы обзора. Вы можете отсеивать, добавлять или удалять IP-адреса или IP-группы.

   ![Обзор групп ИС](media/ip-groups/overview.png)

## <a name="manage-an-ip-group"></a>Управление группой ИС

Вы можете увидеть все IP-адреса в IP-группе и правила или ресурсы, которые с ней связаны. Чтобы удалить IP Group, необходимо сначала отделить IP Group от ресурса, который ее использует.

1. Для просмотра или отображания IP-адресов выберите **IP-адреса** под **настройками** на левом стеле.
2. Чтобы добавить один или несколько IP-адресов (es), выберите **Добавить IP-адреса.** Это открывает страницу **Перетащите или просмотра** для загрузки, или вы можете ввести адрес вручную.
3.  Выбор эллипсов **(...)** в право на отображение или удаление IP-адресов. Чтобы отсеять или удалять несколько IP-адресов, выберите коробки и выберите **edit** or **Delete** в верхней части.
4. Наконец, можно экспортировать файл в формате файла CSV.

> [!NOTE]
> Если вы удалите все IP-адреса в IP-группе, пока она все еще используется в правиле, это правило пропускается.


## <a name="use-an-ip-group"></a>Использование группы IP

Теперь вы можете выбрать **IP Group** в качестве **типа исходного кода** или типа **назначения** для IP-адреса (es) при создании ДНК, приложения или сетевых правил Azure Firewall.

> [!NOTE]
> IP-группы не поддерживаются в политике брандмауэра. В настоящее время он поддерживается только с традиционными правилами брандмауэра.

![IP-группы в брандмауэре](media/ip-groups/fw-ipgroup.png)

## <a name="region-availability"></a>Доступность по регионам

Группы ИС в настоящее время доступны в следующих регионах:

- западная часть США
- западная часть США 2
- Восточная часть США
- восточная часть США 2
- Центральная часть США
- Центрально-северная часть США
- центрально-западная часть США
- Центрально-южная часть США
- Центральная Канада
- Северная Европа
- Западная Европа
- Центральная Франция
- южная часть Соединенного Королевства
- Восточная Австралия
- Центральная Австралия
- Юго-Восточная часть Австралии

## <a name="related-azure-powershell-cmdlets"></a>Похожие смдлеты Azure PowerShell

Следующие cmdlets Azure PowerShell могут быть использованы для создания и управления IP-группами:

- [New-AzIpGroup](https://docs.microsoft.com/powershell/module/az.network/new-azipgroup?view=azps-3.4.0)
- [Удалить-AzIPGroup](https://docs.microsoft.com/powershell/module/az.network/remove-azipgroup?view=azps-3.4.0)
- [Get-AzIpGroup](https://docs.microsoft.com/powershell/module/az.network/get-azipgroup?view=azps-3.4.0)
- [Set-AzIpGroup](https://docs.microsoft.com/powershell/module/az.network/set-azipgroup?view=azps-3.4.0)
- [Новый-AzFirewallNetworkRule](https://docs.microsoft.com/powershell/module/az.network/new-azfirewallnetworkrule?view=azps-3.4.0)
- [New-AzFirewallApplicationRule](https://docs.microsoft.com/powershell/module/az.network/new-azfirewallapplicationrule?view=azps-3.4.0)
- [Нью-АзФайрволлНатРуле](https://docs.microsoft.com/powershell/module/az.network/new-azfirewallnatrule?view=azps-3.4.0)

## <a name="next-steps"></a>Дальнейшие действия

- См. дополнительные сведения о [развертывании и настройке Брандмауэра Azure](tutorial-firewall-deploy-portal.md).