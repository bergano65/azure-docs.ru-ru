---
title: Обзор TAP виртуальной сети Azure | Документы Майкрософт
description: Подробная информация о TAP виртуальной сети. TAP виртуальной сети предоставляет вам более подробную копию сетевого трафика виртуальной машины, который можно передавать в сборщик пакетов.
services: virtual-network
documentationcenter: na
author: karthikananth
manager: ganesr
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/14/2019
ms.author: kaanan
ms.openlocfilehash: 47db03460ad3c5194a5445f0b25cb8e742e60c21
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79279615"
---
# <a name="virtual-network-tap"></a>TAP виртуальной сети

TAP (точка доступа к терминалу) виртуальной сети Azure позволяет непрерывно передавать сетевой трафик виртуальной машины в сборщик сетевых пакетов или средство аналитики. Инструмент для коллектора или аналитики предоставляется [партнером по виртуальному прибору сети.](https://azure.microsoft.com/solutions/network-appliances/) Список партнерских решений, работающих с TAP виртуальной сети, см. в разделе о [партнерских решениях](#virtual-network-tap-partner-solutions).

> [!IMPORTANT]
> Виртуальная сеть TAP в настоящее время находится в предварительном просмотре во всех регионах Azure. Чтобы использовать виртуальную сеть TAP, необходимо зарегистрироваться <azurevnettap@microsoft.com> в предварительном просмотре, отправив электронное письмо с идентификатором подписки. После активации подписки на электронную почту будет отправлено письмо. Вы не сможете использовать функцию, пока не получите подтверждение по электронной почте. Этот предварительный просмотр предоставляется без соглашения об уровне обслуживания и не должен использоваться для производственных нагрузок. Некоторые функции могут не поддерживаться, иметь ограничения и быть доступными не во всех расположениях Azure. Дополнительные [условия использования для предварительных](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) просмотров Microsoft Azure можно узнать подробнее.

## <a name="virtual-network-tap-partner-solutions"></a>Партнерские решения TAP виртуальной сети

### <a name="network-packet-brokers"></a>Брокеры сетевых пакетов

- [Big Switch Big Monitoring Fabric](https://www.bigswitch.com/products/big-monitoring-fabric/public-cloud/microsoft-azure)
- [Gigamon GigaSECURE](https://blog.gigamon.com/2018/09/13/why-microsofts-new-vtap-service-works-even-better-with-gigasecure-for-azure)
- [Ixia CloudLens](https://www.ixiacom.com/cloudlens/cloudlens-azure)
- [Nubeva Prisms](https://www.nubeva.com/azurevtap)

### <a name="security-analytics-networkapplication-performance-management"></a>Аналитика безопасности и управления производительностью сети или приложений

- [Пробуждение безопасности](https://awakesecurity.com/technology-partners/microsoft-azure/)
- [Облако Стелстечасов](https://blogs.cisco.com/security/cisco-stealthwatch-cloud-and-microsoft-azure-reliable-cloud-infrastructure-meets-comprehensive-cloud-security)
- [Темный след](https://www.darktrace.com/en/azure/)
- [ExtraHop Reveal(x)](https://www.extrahop.com/partners/tech-partners/microsoft/)
- [Fidelis Cybersecurity](https://www.fidelissecurity.com/technology-partners/microsoft-azure )
- [Flowmon](https://www.flowmon.com/blog/azure-vtap)
- [NetFort LANGuardian](https://www.netfort.com/languardian/solutions/visibility-in-azure-network-tap/)
- [Netscout vSTREAM]( https://www.netscout.com/technology-partners/microsoft/azure-vtap)
- [Речное стальноеЦентральное приложениеОтвет]( https://www.riverbed.com/products/steelcentral/steelcentral-appresponse-11.html)
- [Платформа RSA NetWitness®](https://www.rsa.com/azure)
- [Vectra Cognito](https://vectra.ai/microsoftazure)

Ниже показано, как работает TAP виртуальной сети. Конфигурацию TAP можно добавить в [сетевой интерфейс](virtual-network-network-interface.md), присоединенный к виртуальной машине, развернутой в вашей виртуальной сети. Местом назначения станет IP-адрес виртуальной сети в той же виртуальной сети, что и отслеживаемый сетевой интерфейс или [одноранговая виртуальная сеть](virtual-network-peering-overview.md). Для обеспечения высокой доступности решение сборщика для TAP виртуальной сети можно развернуть за внутренней подсистемой балансировки нагрузки Azure. Инструкции по оценке параметров развертывания отдельного решения см. в разделе о [партнерских решениях](#virtual-network-tap-partner-solutions).

![Как работает TAP виртуальной сети](./media/virtual-network-tap/architecture.png)

## <a name="prerequisites"></a>Предварительные требования

Прежде чем создать виртуальную сеть TAP, вы должны получить письмо с подтверждением, которое вы зачисляете в предварительный просмотр, и создать одну или несколько виртуальных машин, созданных с помощью модели развертывания [Azure Resource Manager](../azure-resource-manager/management/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) и партнерского решения для агрегирования трафика TAP в той же azure области. Если в вашей виртуальной сети нет ни одного из партнерских решений, разверните одно из них, как указано в разделе о [партнерских решениях](#virtual-network-tap-partner-solutions). Один и тот же ресурс TAP виртуальной сети позволяет объединять трафик из разных сетевых интерфейсов в одной и той же или разных подписках. Если отслеживаемые сетевые интерфейсы находятся в разных подписках, обе подписки должны быть связаны с одним клиентом Azure Active Directory. Кроме того, отслеживаемые сетевые интерфейсы и конечная точка назначения для объединения трафика TAP могут находиться в одноранговых виртуальных сетях в одном и том же регионе. Если вы используете эту модель развертывания, перед настройкой TAP виртуальной сети убедитесь, что [пиринг виртуальных сетей](virtual-network-peering-overview.md) включен.

## <a name="permissions"></a>Разрешения

Учетные записи, используемые для применения конфигурации TAP к сетевым интерфейсам, должны быть назначены роли [участник сети](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) или [пользовательской роли](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json), которой назначены необходимые действия, перечисленные в следующей таблице:

| Действие | name |
|---|---|
| Microsoft.Network/virtualNetworkTaps/* | Требуется для создания, обновления, чтения и удаления ресурса TAP виртуальной сети |
| Microsoft.Network/networkInterfaces/read | Требуется для чтения ресурса сетевого интерфейса, в котором будет настраиваться TAP |
| Microsoft.Network/tapConfigurations/* | Требуется для создания, обновления, чтения и удаления конфигурации TAP в сетевом интерфейсе |

## <a name="next-steps"></a>Дальнейшие действия

- Дополнительные сведения о [создании TAP виртуальной сети](tutorial-tap-virtual-network-cli.md).
