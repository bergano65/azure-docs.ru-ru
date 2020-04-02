---
title: Устранение неполадок с пирингом виртуальной сети
description: Шаги, чтобы помочь решить большинство проблем с пирингом виртуальной сети.
services: virtual-network
documentationcenter: na
author: v-miegge
manager: dcscontentpm
editor: ''
tags: virtual-network
ms.assetid: 1a3d1e84-f793-41b4-aa04-774a7e8f7719
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/28/2019
ms.author: kaushika
ms.openlocfilehash: 662619e101b45d1dd8b34ea97e31f214b254124a
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521884"
---
# <a name="troubleshoot-virtual-network-peering-issues"></a>Устранение неполадок с пирингом виртуальной сети

Это руководство по устранению неполадок предоставляет шаги, которые помогут вам решить большинство [проблем с пирингом виртуальной сети.](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)

![Диаграмма виртуального сетевого пиринга](./media/virtual-network-troubleshoot-peering-issues/4489538_en_1.png)

## <a name="configure-virtual-network-peering-between-two-virtual-networks"></a>Настройка виртуальной сети, вглядывающейся между двумя виртуальными сетями

Являются ли виртуальные сети в одной подписке или в разных подписках?

### <a name="the-virtual-networks-are-in-the-same-subscription"></a>Виртуальные сети находятся в той же подписке

Для настройки виртуальной сети пиринга для виртуальных сетей, которые находятся в той же подписке, используйте методы в следующих статьях:

* Если виртуальные сети находятся в одном *регионе,* [см.](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering#create-a-peering)
* Если виртуальные сети находятся в *разных регионах,* см. [Virtual network peering](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) 

> [!Note]
> Связь не работает над глобальной виртуальной сетью пиринга для следующих ресурсов: 
>
> * Виртуальные машины (VMs) за основные внутренние балансиватор нагрузки (ILB) SKU
> * Redis кэш (использует основные ILB SKU)
> * Шлюз приложения (использует Базовый ILB SKU)
> * Виртуальные наборы масштабов машин (использует основные ILB SKU)
> * Кластеры Azure Service Fabric (использует базовый ILB SKU)
> * Сервер S'L Всегда на (использует основные ILB SKU)
> * Среда обслуживания приложений Azure для PowerApps (использует базовый ILB SKU)
> * Управление API Azure (использует базовый ILB SKU)
> * Службы активных доменов Azure (Azure AD DS) (использует базовый ILB SKU)

Для получения дополнительной информации ознакомьтесь [с требованиями и ограничениями](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#requirements-and-constraints) глобального пиринга.

### <a name="the-virtual-networks-are-in-different-subscriptions-or-active-directory-tenants"></a>Виртуальные сети находятся в разных подписках или нанимателях Active Directory

Для настройки виртуальной сети для виртуальных сетей в разных подписях или нанимателях Active Directory [см. Создать пиринг в различных подписках на Azure CLI.](https://docs.microsoft.com/azure/virtual-network/create-peering-different-subscriptions#cli)

> [!Note]
> Для настройки внизаний сети необходимо иметь разрешения **на сетевой вклад** в обеих подписках. Для получения дополнительной [информации](virtual-network-manage-peering.md#permissions)см.

## <a name="configure-virtual-network-peering-with-hub-spoke-topology-that-uses-on-premises-resources"></a>Настройка виртуальной сети пиринга с концентратор-спица топологии, которая использует ресурсы на месте

![Диаграмма виртуальной сети пиринг с на-предылок говорил](./media/virtual-network-troubleshoot-peering-issues/4488712_en_1a.png)

### <a name="for-a-site-to-site-connection-or-an-expressroute-connection"></a>Для подключения к сайту или подключения ExpressRoute

Выполните последующие шаги в: [Налаживать VPN шлюз транзита для виртуальной сети пиринга](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-peering-gateway-transit?toc=/azure/virtual-network/toc.json).

### <a name="for-point-to-site-connections"></a>Для подключения от точки к сайту

1. Выполните последующие шаги в: [Налаживать VPN шлюз транзита для виртуальной сети пиринга](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-peering-gateway-transit?toc=/azure/virtual-network/toc.json).
2. После того, как виртуальная сеть пиринг устанавливается или изменяется, загрузите и переустановите пакет "точка к сайту", чтобы клиенты точечной виртуальной сети получили обновленные маршруты.

## <a name="configure-virtual-network-peering-with-hub-spoke-topology-virtual-network"></a>Настройка виртуальной сети пиринг с концентратором-говорить топологии виртуальной сети

![Диаграмма виртуальной сети пиринг с виртуальной сети говорил](./media/virtual-network-troubleshoot-peering-issues/4488712_en_1b.png)

### <a name="the-virtual-networks-are-in-the-same-region"></a>Виртуальные сети находятся в одном регионе


1. В виртуальной сети концентратора наверстудиваем сетевой виртуальный прибор (NVA).
1. В смечатой виртуальных сетях применяются маршруты, определяемые пользователем, со следующим типом перехода "сетевой виртуальный прибор".

Для получения дополнительной [информации](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#service-chaining)см.

> [!Note]
> Если вам требуется помощь в настройке NVA, [свяжитесь с поставщиком NVA.](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines)

Для получения помощи в устранении неполадок настройки и реуктора NVA устройства [см.](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-nva)

### <a name="the-virtual-networks-are-in-different-regions"></a>Виртуальные сети находятся в разных регионах

Транзит через глобальную виртуальную сеть пиринга в настоящее время поддерживается. Связь не работает над глобальной виртуальной сетью пиринга для следующих ресурсов:

* VMs за основные ILB SKU
* Redis кэш (использует основные ILB SKU)
* Шлюз приложения (использует Базовый ILB SKU)
* Наборы масштаба (использует базовый ILB SKU)
* Кластеры сервисной ткани (использует Базовый ILB SKU)
* Сервер S'L Всегда на (использует основные ILB SKU)
* Среда обслуживания приложений (использует базовый ILB SKU)
* Управление API (использует базовый ILB SKU)
* Azure AD DS (использует базовый ILB SKU)

Чтобы узнать больше о глобальных требований к пирингу и ограничениях, [см.](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#requirements-and-constraints)

## <a name="troubleshoot-a-connectivity-issue-between-two-peered-virtual-networks"></a>Устранение проблемы с подключением между двумя заглядными виртуальными сетями

Войти на [портал Azure](https://portal.azure.com/) с учетной записью, которая имеет необходимые [роли и разрешения.](virtual-network-manage-peering.md#permissions) Выберите виртуальную сеть, выберите **Peering,** а затем проверьте поле **статуса.** Каков статус?

### <a name="the-peering-status-is-connected"></a>Статус пиринга — "Связь"

Чтобы устранить неполадку, выполните следующие действия.

1. Проверьте потоки сетевого трафика:

   Используйте [Connection Troubleshoot](https://docs.microsoft.com/azure/network-watcher/network-watcher-connectivity-overview) и [проверку потока IP](https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview) от источника VM до пункта назначения VM, чтобы определить, есть ли NSG или UDR, которые вызывают помехи в транспортных потоках.

   Если вы используете брандмауэр или NVA: 
   1. Документируйте параметры UDR, чтобы можно было восстановить их после завершения этого шага.
   2. Удалите UDR из подсети источника VM или NIC, которая указывает на NVA в качестве следующего перехода. Проверить подключение от источника VM непосредственно к пункту назначения, который обойдя NVA. Если этот шаг не работает, [см.](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-nva)

2. Возьмите сетевой след: 
   1. Запустите сетевой след на пункте назначения VM. Для Windows можно использовать **Netsh.** Для Linux используйте **TCPDump**.
   2. Выполнить **TcpPing** или **PsPing** от источника до IP назначения.

      Это пример команды **TcpPing:**`tcping64.exe -t <destination VM address> 3389`

   3. После завершения **TcpPing** остановите сетевой след в пункте назначения.
   4. Если пакеты поступают из источника, нет проблемы с сетью. Изучите как брандмауэр VM, так и прослушивание приложения в этом порту, чтобы найти проблему конфигурации.

   > [!Note]
   > Вы не можете подключиться к следующим типам ресурсов в глобальных виртуальных сетей (виртуальные сети в разных регионах):
   >
   > * VMs за основные ILB SKU
   > * Redis кэш (использует основные ILB SKU)
   > * Шлюз приложения (использует Базовый ILB SKU)
   > * Наборы масштаба (использует базовый ILB SKU)
   > * Кластеры сервисной ткани (использует Базовый ILB SKU)
   > * Сервер S'L Всегда на (использует основные ILB SKU)
   > * Среда обслуживания приложений (использует базовый ILB SKU)
   > * Управление API (использует базовый ILB SKU)
   > * Azure AD DS (использует базовый ILB SKU)

Для получения дополнительной информации ознакомьтесь [с требованиями и ограничениями](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#requirements-and-constraints) глобального пиринга.

### <a name="the-peering-status-is-disconnected"></a>Статус пиринга — «Отключенный»

Чтобы решить эту проблему, удалите пиринг из обеих виртуальных сетей, а затем воссоздайте их.

## <a name="troubleshoot-a-connectivity-issue-between-a-hub-spoke-virtual-network-and-an-on-premises-resource"></a>Устранение проблемы подключения между виртуальной сетью, ориентированной на концентратор, и ресурсом для новее

Использует ли ваша сеть сторонний Шлюз NVA или VPN?

### <a name="my-network-uses-a-third-party-nva-or-vpn-gateway"></a>Моя сеть использует сторонний шлюз NVA или VPN

Для устранения неполадок проблем с подключением, которые затрагивают сторонний шлюз NVA или VPN, см.

* [NVA устранение неполадок](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-nva)
* [Цепочка служб](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#service-chaining)

### <a name="my-network-does-not-use-a-third-party-nva-or-vpn-gateway"></a>Моя сеть не использует сторонний Шлюз NVA или VPN

Есть ли у виртуальной сети концентратора и виртуальной сети VPN- шлюз?

#### <a name="both-the-hub-virtual-network-and-the-spoke-virtual-network-have-a-vpn-gateway"></a>И концентратор виртуальной сети и говорил виртуальной сети имеют VPN шлюз

Использование удаленного шлюза не поддерживается.

Если в виртуальной сети уже есть VPN шлюз, опция **удаленного шлюза Use** не поддерживается в виртуальной сети. Это происходит из-за ограничения виртуальной сети пиринга.

#### <a name="both-the-hub-virtual-network-and-the-spoke-virtual-network-do-not-have-a-vpn-gateway"></a>И концентратор виртуальной сети и говорил виртуальной сети не имеют VPN шлюз

Для подключения от сайта к сайту или Azure ExpressRoute проверьте следующие основные причины проблем с подключением к удаленной виртуальной сети из наместах:

* В виртуальной сети, которая имеет шлюз, убедитесь, что **Allow пересылаемый** флажок трафика выбран.
* В виртуальной сети, которая не имеет шлюза, убедитесь, что **удаленный шлюз Use** выбран.
* Попросите, чтобы администратор сети проверил ваши устройства, чтобы убедиться, что все они имеют удаленное виртуальное пространство адреса сети.

Для подключения от точки к сайту:

* В виртуальной сети, которая имеет шлюз, убедитесь, что **Allow пересылаемый** флажок трафика выбран.
* В виртуальной сети, которая не имеет шлюза, убедитесь, что **удаленный шлюз Use** выбран.
* Скачать и переустановить клиентский пакет от точки к сайту. Маршруты виртуальной сети, которые вновь заглянули, не добавляют автоматически маршруты клиентам по точкам на место.

## <a name="troubleshoot-a-hub-spoke-network-connectivity-issue-between-spoke-virtual-networks-in-the-same-region"></a>Устранение неполадок в связи с подключением к сети между виртуальными сетями в одном регионе

Сеть концентраторов должна включать NVA. Назначайте UD-сообщения в спицы, которые имеют набор NVA в качестве следующего перехода, и **позволите разрешить переадресованный трафик** в виртуальной сети концентратора.

Для получения дополнительной информации ознакомьтесь с [цепочкой служб](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#service-chaining)и обсудите эти требования с [поставщиком NVA](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines) по вашему выбору.

## <a name="troubleshoot-a-hub-spoke-network-connectivity-issue-between-spoke-virtual-networks-in-different-regions"></a>Устранение проблем с проблемой подключения к онцентру между виртуальными сетями в разных регионах

Транзит через глобальную виртуальную сеть пиринга в настоящее время поддерживается. Связь не работает над глобальной виртуальной сетью пиринга для следующих ресурсов:

* VMs за основные ILB SKU
* Redis кэш (использует основные ILB SKU)
* Шлюз приложения (использует Базовый ILB SKU)
* Наборы масштаба (использует базовый ILB SKU)
* Кластеры сервисной ткани (использует Базовый ILB SKU)
* Сервер S'L Всегда на (использует основные ILB SKU)
* Среда обслуживания приложений (использует базовый ILB SKU)
* Управление API (использует базовый ILB SKU)
* Azure AD DS (использует базовый ILB SKU)

Для получения дополнительной информации, [Different VPN Topologies](https://blogs.msdn.microsoft.com/igorpag/2016/02/11/hubspoke-daisy-chain-and-full-mesh-vnet-topologies-in-azure-arm-v2/)см. [requirements and constraints](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#requirements-and-constraints)

## <a name="troubleshoot-a-hub-spoke-network-connectivity-issue-between-a-web-app-and-the-spoke-virtual-network"></a>Устранение неполадок в связи с подключением к онцентров у нисходного подключения между веб-приложением и виртуальной сетью

Чтобы устранить неполадку, выполните следующие действия.

1. Войдите на портал Azure. 
1. В веб-приложении выберите **сеть,** а затем выберите **Интеграцию VNet.**
1. Проверьте, можно ли увидеть удаленную виртуальную сеть. Вручную введите удаленное виртуальное пространство адресной сети **(Sync Network** и **Добавить маршруты).**

Дополнительные сведения см. в следующих статьях:

* [Интеграция приложения с виртуальной сетью Azure](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet)
* [Сведения о маршрутизации VPN-подключений "точка — сеть"](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-point-to-site-routing)

## <a name="troubleshoot-a-virtual-network-peering-configuration-error-message"></a>Устранение проблем виртуальной сети вневрангового сообщения об ошибке конфигурации 

### <a name="current-tenant-tenant-id-isnt-authorized-to-access-linked-subscription"></a>Текущий `<TENANT ID>` арендатор не имеет права на доступ к связанной подписке

Чтобы решить эту проблему, [см.](https://docs.microsoft.com/azure/virtual-network/create-peering-different-subscriptions#cli)

### <a name="not-connected"></a>Не подключено

Чтобы решить эту проблему, удалите пиринг из обеих виртуальных сетей, а затем воссоздайте их.

### <a name="failed-to-peer-a-databricks-virtual-network"></a>Не удалось заглянуть в виртуальную сеть Databricks

Чтобы решить эту проблему, назначайте виртуальную сеть, вглядывающуюся под **Azure Databricks,** а затем укажите целевую виртуальную сеть с помощью **Идентификатора ресурсов.** Для получения дополнительной информации [см.](https://docs.azuredatabricks.net/administration-guide/cloud-configurations/azure/vnet-peering.html#id2)

### <a name="the-remote-virtual-network-lacks-a-gateway"></a>Удаленной виртуальной сети не хватает шлюза

Эта проблема возникает, когда вы загоняете `Use Remote Gateways`виртуальные сети от разных арендаторов, а затем хотите настроить. Ограничение портала Azure состоит в том, что он не может проверить наличие виртуального сетевого шлюза в виртуальной сети другого клиента.

Существует два способа решения проблемы:

 * Удалите пиринги `Use Remote Gateways` и активируйте опцию при создании нового пиринга.
 * Вместо портала Azure используйте PowerShell или `Use Remote Gateways`CLI, чтобы включить.

## <a name="next-steps"></a>Дальнейшие действия

* [Устранение проблем с подключением между виртуальными машинами Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-connectivity-problem-between-vms)
