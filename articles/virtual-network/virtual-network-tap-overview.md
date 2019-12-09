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
ms.openlocfilehash: f287d3782148ca48a0367fa2a6a9a346a0299684
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/08/2019
ms.locfileid: "74931338"
---
# <a name="virtual-network-tap"></a>TAP виртуальной сети

TAP (точка доступа к терминалу) виртуальной сети Azure позволяет непрерывно передавать сетевой трафик виртуальной машины в сборщик сетевых пакетов или средство аналитики. Сборщик сетевых пакетов или средство аналитики предоставляются партнером-разработчиком [сетевого виртуального устройства](https://azure.microsoft.com/solutions/network-appliances/). Список партнерских решений, работающих с TAP виртуальной сети, см. в разделе о [партнерских решениях](#virtual-network-tap-partner-solutions).

> [!IMPORTANT]
> В настоящее время TAP для виртуальной сети доступна предварительная версия во всех регионах Azure. Чтобы использовать КАСАНИе виртуальной сети, необходимо зарегистрироваться в предварительной версии, отправив сообщение электронной почты на <azurevnettap@microsoft.com> с ИДЕНТИФИКАТОРом подписки. После регистрации подписки вы получите сообщение электронной почты. Вы не сможете использовать функцию, пока не получите подтверждение по электронной почте. Эта предварительная версия предоставляется без соглашения об уровне обслуживания и не должна использоваться для рабочих нагрузок. Некоторые функции могут не поддерживаться, иметь ограничения и быть доступными не во всех расположениях Azure. Дополнительные сведения см. в [дополнительных условиях использования Microsoft Azure предварительных версиях](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) .

## <a name="virtual-network-tap-partner-solutions"></a>Партнерские решения TAP виртуальной сети

### <a name="network-packet-brokers"></a>Брокеры сетевых пакетов

- [Big Switch Big Monitoring Fabric](https://www.bigswitch.com/products/big-monitoring-fabric/public-cloud/microsoft-azure)
- [Gigamon GigaSECURE](https://blog.gigamon.com/2018/09/13/why-microsofts-new-vtap-service-works-even-better-with-gigasecure-for-azure)
- [Ixia CloudLens](https://www.ixiacom.com/cloudlens/cloudlens-azure)
- [Nubeva Prisms](https://www.nubeva.com/azurevtap)

### <a name="security-analytics-networkapplication-performance-management"></a>Аналитика безопасности и управления производительностью сети или приложений

- [Безопасность в спящем режиме](https://awakesecurity.com/technology-partners/microsoft-azure/)
- [Стеалсватчное облако Cisco](https://blogs.cisco.com/security/cisco-stealthwatch-cloud-and-microsoft-azure-reliable-cloud-infrastructure-meets-comprehensive-cloud-security)
- [дарктраце](https://www.darktrace.com/en/azure/)
- [ExtraHop Reveal(x)](https://www.extrahop.com/partners/tech-partners/microsoft/)
- [Fidelis Cybersecurity](https://www.fidelissecurity.com/technology-partners/microsoft-azure )
- [Flowmon](https://www.flowmon.com/blog/azure-vtap)
- [Нетфорт Лангуардиан](https://www.netfort.com/languardian/solutions/visibility-in-azure-network-tap/)
- [Netscout vSTREAM]( https://www.netscout.com/technology-partners/microsoft/azure-vtap)
- [Riverbed Стилцентрал Аппреспонсе]( https://www.riverbed.com/products/steelcentral/steelcentral-appresponse-11.html)
- [Платформа RSA NetWitness®](https://www.rsa.com/azure)
- [Vectra Cognito](https://vectra.ai/microsoftazure)

Ниже показано, как работает TAP виртуальной сети. Конфигурацию TAP можно добавить в [сетевой интерфейс](virtual-network-network-interface.md), присоединенный к виртуальной машине, развернутой в вашей виртуальной сети. Местом назначения станет IP-адрес виртуальной сети в той же виртуальной сети, что и отслеживаемый сетевой интерфейс или [одноранговая виртуальная сеть](virtual-network-peering-overview.md). Решение сборщика виртуальной сети можно развернуть за счет внутреннего балансировщика нагрузки Azure для обеспечения высокой доступности. Инструкции по оценке параметров развертывания отдельного решения см. в разделе о [партнерских решениях](#virtual-network-tap-partner-solutions).

![Как работает TAP виртуальной сети](./media/virtual-network-tap/architecture.png)

## <a name="prerequisites"></a>Технические условия

Прежде чем приступить к созданию КАСАНИя виртуальной сети, необходимо получить сообщение с подтверждением, зарегистрированное в предварительной версии, и создать одну или несколько виртуальных машин, созданных с помощью [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) модели развертывания, и партнерское решение для агрегирования трафика TAP в том же регионе Azure. Если в вашей виртуальной сети нет ни одного из партнерских решений, разверните одно из них, как указано в разделе о [партнерских решениях](#virtual-network-tap-partner-solutions). Один и тот же ресурс TAP виртуальной сети позволяет объединять трафик из разных сетевых интерфейсов в одной и той же или разных подписках. Если отслеживаемые сетевые интерфейсы находятся в разных подписках, обе подписки должны быть связаны с одним клиентом Azure Active Directory. Кроме того, отслеживаемые сетевые интерфейсы и конечная точка назначения для объединения трафика TAP могут находиться в одноранговых виртуальных сетях в одном и том же регионе. Если вы используете эту модель развертывания, перед настройкой TAP виртуальной сети убедитесь, что [пиринг виртуальных сетей](virtual-network-peering-overview.md) включен.

## <a name="permissions"></a>Разрешения

Учетные записи, используемые для применения конфигурации TAP к сетевым интерфейсам, должны быть назначены роли [участник сети](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) или [пользовательской роли](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json), которой назначены необходимые действия, перечисленные в следующей таблице:

| Действия | Name |
|---|---|
| Microsoft.Network/virtualNetworkTaps/* | Требуется для создания, обновления, чтения и удаления ресурса TAP виртуальной сети |
| Microsoft.Network/networkInterfaces/read | Требуется для чтения ресурса сетевого интерфейса, в котором будет настраиваться TAP |
| Microsoft.Network/tapConfigurations/* | Требуется для создания, обновления, чтения и удаления конфигурации TAP в сетевом интерфейсе |

## <a name="next-steps"></a>Дальнейшие действия

- Дополнительные сведения о [создании TAP виртуальной сети](tutorial-tap-virtual-network-cli.md).
