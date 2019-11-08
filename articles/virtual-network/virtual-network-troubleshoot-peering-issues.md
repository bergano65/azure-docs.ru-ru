---
title: Устранение неполадок с пирингом виртуальной сети
description: Действия по устранению большинства проблем пиринга в виртуальной сети.
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
ms.openlocfilehash: 1fddbe908ccebc1384dcccde0810366f1a6d5da7
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/07/2019
ms.locfileid: "73796237"
---
# <a name="troubleshoot-virtual-network-peering-issues"></a>Устранение неполадок с пирингом виртуальной сети

В этом руководстве по устранению неполадок приведены инструкции по устранению большинства проблем с [пирингом виртуальных сетей](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) .

![Схема пиринга между виртуальными сетями](./media/virtual-network-troubleshoot-peering-issues/4489538_en_1.png)

## <a name="configure-virtual-network-peering-between-two-virtual-networks"></a>Настройка пиринга виртуальной сети между двумя виртуальными сетями

Являются ли виртуальные сети в одной подписке или в разных подписках?

### <a name="the-virtual-networks-are-in-the-same-subscription"></a>Виртуальные сети находятся в одной подписке

Чтобы настроить пиринг виртуальной сети для виртуальных сетей, находящиеся в одной подписке, используйте методы, описанные в следующих статьях:

* Если виртуальные сети находятся в *одном регионе*, см. раздел [Создание пиринга](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering#create-a-peering).
* Если виртуальные сети находятся в *разных регионах*, см. раздел [пиринг виртуальных](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)сетей. 

> [!Note]
> Подключение не работает через пиринг глобальной виртуальной сети для следующих ресурсов: 
>
> * Виртуальные машины (VM) за базовым SKU внутренней подсистемы балансировки нагрузки (ILB)
> * Кэш Redis (использует базовый номер SKU ILB)
> * Шлюз приложений (использует базовый SKU ILB)
> * Масштабируемые наборы виртуальных машин (использует базовый номер SKU ILB)
> * Кластеры Service Fabric Azure (использует базовый номер SKU ILB)
> * SQL Server Always On (использует базовый номер SKU ILB)
> * Среда службы приложений Azure для PowerApps (использует базовый номер SKU ILB)
> * Управление API Azure (использует базовый номер SKU ILB)
> * Azure Active Directory доменных служб (Azure AD DS) (использует базовый номер SKU ILB)

Дополнительные сведения см. в разделе [требования и ограничения](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#requirements-and-constraints) глобального пиринга.

### <a name="the-virtual-networks-are-in-different-subscriptions-or-active-directory-tenants"></a>Виртуальные сети находятся в разных подписках или клиентах Active Directory

Сведения о настройке пиринга виртуальной сети для виртуальных сетей в разных подписках или клиентах Active Directory см. в разделе [Создание пиринга в разных подписках для Azure CLI](https://docs.microsoft.com/azure/virtual-network/create-peering-different-subscriptions#cli).

> [!Note]
> Чтобы настроить пиринг сети, необходимо иметь разрешения **участника сети** в обеих подписках. Дополнительные сведения см. в разделе [разрешения пиринга](virtual-network-manage-peering.md#permissions).

## <a name="configure-virtual-network-peering-with-hub-spoke-topology-that-uses-on-premises-resources"></a>Настройка пиринга виртуальных сетей с помощью топологии Hub-лучевой сети, использующей локальные ресурсы

![Схема пиринга виртуальной сети с локальным периферийным узлом](./media/virtual-network-troubleshoot-peering-issues/4488712_en_1a.png)

### <a name="for-a-site-to-site-connection-or-an-expressroute-connection"></a>Для подключения типа "сеть — сеть" или ExpressRoute

Выполните действия, описанные в разделе [Настройка транзита VPN-шлюза для пиринга виртуальных сетей](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-peering-gateway-transit?toc=/azure/virtual-network/toc.json).

### <a name="for-point-to-site-connections"></a>Для подключений "точка — сеть"

1. Выполните действия, описанные в разделе [Настройка транзита VPN-шлюза для пиринга виртуальных сетей](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-peering-gateway-transit?toc=/azure/virtual-network/toc.json).
2. После установки или изменения пиринга виртуальной сети Скачайте и переустановите пакет "точка — сеть", чтобы клиенты "точка — сеть" могли получать обновленные маршруты к виртуальной сети "звезда".

## <a name="configure-virtual-network-peering-with-hub-spoke-topology-virtual-network"></a>Настройка пиринга виртуальной сети с помощью виртуальной сети с топологией звезды

![Схема пиринга виртуальных сетей с помощью виртуальной сети](./media/virtual-network-troubleshoot-peering-issues/4488712_en_1b.png)

### <a name="the-virtual-networks-are-in-the-same-region"></a>Виртуальные сети находятся в одном регионе


1. В виртуальной сети концентратора Настройте сетевое виртуальное устройство (NVA).
1. В периферийных виртуальных сетях имеют определяемые пользователем маршруты с типом следующего прыжка "сетевой виртуальный модуль".

Дополнительные сведения см. в разделе [цепочка служб](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#service-chaining).

> [!Note]
> Если вам нужна помощь по настройке NVA, обратитесь к [поставщику NVA](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines).

Справку по устранению неполадок при настройке и маршрутизации устройств NVA см. [в статье проблемы с виртуальным сетевым устройством в Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-nva).

### <a name="the-virtual-networks-are-in-different-regions"></a>Виртуальные сети находятся в разных регионах

Теперь поддерживается транзитное подключение по глобальному пирингу виртуальных сетей. Подключение не работает через Глобальный пиринг виртуальных сетей для следующих ресурсов:

* Виртуальные машины за базовым SKU ILB
* Кэш Redis (использует базовый номер SKU ILB)
* Шлюз приложений (использует базовый SKU ILB)
* Масштабируемые наборы (использует базовый номер SKU ILB)
* Кластеры Service Fabric (использует базовый номер SKU ILB)
* SQL Server Always On (использует базовый номер SKU ILB)
* Среда службы приложений (использует базовый номер SKU ILB)
* Управление API (использует базовый номер SKU ILB)
* AD DS Azure (использует базовый SKU ILB)

Дополнительные сведения о глобальных требованиях к пиринга и ограничений см. в статье [пиринг виртуальных сетей](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#requirements-and-constraints).

## <a name="troubleshoot-a-connectivity-issue-between-two-peered-virtual-networks"></a>Устранение проблем с подключением между двумя одноранговыми виртуальными сетями

Войдите в [портал Azure](https://portal.azure.com/) с помощью учетной записи, обладающей необходимыми [ролями и разрешениями](virtual-network-manage-peering.md#permissions). Выберите виртуальную сеть, выберите **пиринг**, а затем проверьте поле **состояние** . Что такое состояние?

### <a name="the-peering-status-is-connected"></a>Состояние пиринга — "Connected" (подключено)

Чтобы устранить эту проблему, выполните следующие действия.

1. Проверьте потоки сетевого трафика.

   Чтобы определить, есть ли NSG или UDR, вызывающие помехи в потоках трафика, используйте для [устранения неполадок подключения](https://docs.microsoft.com/azure/network-watcher/network-watcher-connectivity-overview) и [IP-потока проверку](https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview) с исходной виртуальной машины на целевую виртуальную машину.

   Если вы используете брандмауэр или NVA: 
   1. Задокументируйте параметры UDR, чтобы их можно было восстановить после завершения этого шага.
   2. Удалите UDR из подсети исходной виртуальной машины или сетевой карты, которая указывает на NVA в качестве следующего прыжка. Проверьте подключение из исходной виртуальной машины непосредственно к назначению, которое обходит NVA. Если этот шаг не работает, см. [средство устранения неполадок NVA](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-nva).

2. Выполните трассировку сети. 
   1. Запустите трассировку сети на целевой виртуальной машине. Для Windows можно использовать **netsh**. Для Linux используйте **TCPDump**.
   2. Запустите **TcpPing** или **PsPing** из источника в конечный IP-адрес.

      Вот пример команды **TcpPing** : `tcping64.exe -t <destination VM address> 3389`

   3. После завершения **TcpPing** завершите трассировку сети в месте назначения.
   4. Если пакеты поступают из источника, сетевая ошибка отсутствует. Изучите брандмауэр виртуальной машины и приложение, слушающее этот порт, для выявления проблемы конфигурации.

   > [!Note]
   > Вы не можете подключиться к следующим типам ресурсов по глобальному пирингу виртуальных сетей (виртуальным сетям в разных регионах):
   >
   > * Виртуальные машины за базовым SKU ILB
   > * Кэш Redis (использует базовый номер SKU ILB)
   > * Шлюз приложений (использует базовый SKU ILB)
   > * Масштабируемые наборы (использует базовый номер SKU ILB)
   > * Кластеры Service Fabric (использует базовый номер SKU ILB)
   > * SQL Server Always On (использует базовый номер SKU ILB)
   > * Среда службы приложений (использует базовый номер SKU ILB)
   > * Управление API (использует базовый номер SKU ILB)
   > * AD DS Azure (использует базовый SKU ILB)

Дополнительные сведения см. в разделе [требования и ограничения](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#requirements-and-constraints) глобального пиринга.

### <a name="the-peering-status-is-disconnected"></a>Состояние пиринга — "disconnected"

Чтобы устранить эту проблему, удалите пиринг из обеих виртуальных сетей, а затем создайте их повторно.

## <a name="troubleshoot-a-connectivity-issue-between-a-hub-spoke-virtual-network-and-an-on-premises-resource"></a>Устранение проблем с подключением между центрально-лучевой виртуальной сетью и локальным ресурсом

Использует ли ваша сеть NVA или VPN-шлюз стороннего производителя?

### <a name="my-network-uses-a-third-party-nva-or-vpn-gateway"></a>Моя сеть использует NVA или VPN-шлюз стороннего производителя

Сведения об устранении проблем с подключением, влияющих на NVA или VPN-шлюз стороннего производителя, см. в следующих статьях:

* [Средство устранения неполадок NVA](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-nva)
* [Цепочка служб](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#service-chaining)

### <a name="my-network-does-not-use-a-third-party-nva-or-vpn-gateway"></a>Моя сеть не использует NVA или VPN-шлюз стороннего производителя

Есть ли у виртуальной сети концентратора и периферийной виртуальной сети VPN-шлюз?

#### <a name="both-the-hub-virtual-network-and-the-spoke-virtual-network-have-a-vpn-gateway"></a>Виртуальная сеть концентратора и периферийная виртуальная сеть имеют VPN-шлюз.

Использование удаленного шлюза не поддерживается.

Если у периферийной виртуальной сети уже есть VPN-шлюз, параметр **использовать удаленный шлюз** не поддерживается в периферийной виртуальной сети. Это связано с ограничением пиринга виртуальной сети.

#### <a name="both-the-hub-virtual-network-and-the-spoke-virtual-network-do-not-have-a-vpn-gateway"></a>Виртуальная сеть концентратора и периферийная виртуальная сеть не имеют VPN-шлюза.

Для подключений типа "сеть — сеть" или Azure ExpressRoute проверьте следующие основные причины проблем с подключением к удаленной виртуальной сети из локальной среды.

* Убедитесь, что в виртуальной сети с шлюзом установлен флажок **Разрешить перенаправленный трафик** .
* Убедитесь, что в виртуальной сети, в которой нет шлюза, установлен флажок **использовать удаленный шлюз** .
* Попросите администратора сети проверить свои локальные устройства, чтобы убедиться в том, что для них Добавлено адресное пространство удаленной виртуальной сети.

Для подключений "точка — сеть":

* Убедитесь, что в виртуальной сети с шлюзом установлен флажок **Разрешить перенаправленный трафик** .
* Убедитесь, что в виртуальной сети, в которой нет шлюза, установлен флажок **использовать удаленный шлюз** .
* Скачайте и переустановите пакет клиента "точка — сеть". Маршруты виртуальных сетей, которые являются новыми, не добавляют маршруты к клиентам "точка — сеть".

## <a name="troubleshoot-a-hub-spoke-network-connectivity-issue-between-spoke-virtual-networks-in-the-same-region"></a>Устранение проблем с сетевым подключением в пределах звезды между периферийными виртуальными сетями в одном регионе

Сеть-концентратор должен включать NVA. Настройте определяемые пользователем маршруты в периферийных серверах, для которых в качестве следующего прыжка задано NVA, и включите параметр **Разрешить перенаправленный трафик** в виртуальную сеть концентратора.

Дополнительные сведения см. в разделе [цепочка служб](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#service-chaining)и обсуждайте эти требования с [поставщиком NVA](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines) по своему усмотрению.

## <a name="troubleshoot-a-hub-spoke-network-connectivity-issue-between-spoke-virtual-networks-in-different-regions"></a>Устранение проблем с сетевым подключением между периферийными виртуальными сетями в разных регионах

Теперь поддерживается транзитное подключение по глобальному пирингу виртуальных сетей. Подключение не работает через пиринг глобальной виртуальной сети для следующих ресурсов:

* Виртуальные машины за базовым SKU ILB
* Кэш Redis (использует базовый номер SKU ILB)
* Шлюз приложений (использует базовый SKU ILB)
* Масштабируемые наборы (использует базовый номер SKU ILB)
* Кластеры Service Fabric (использует базовый номер SKU ILB)
* SQL Server Always On (использует базовый номер SKU ILB)
* Среда службы приложений (использует базовый номер SKU ILB)
* Управление API (использует базовый номер SKU ILB)
* AD DS Azure (использует базовый SKU ILB)

Дополнительные сведения см. в разделе [требования и ограничения](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#requirements-and-constraints) глобального пиринга и [различных топологий VPN](https://blogs.msdn.microsoft.com/igorpag/2016/02/11/hubspoke-daisy-chain-and-full-mesh-vnet-topologies-in-azure-arm-v2/).

## <a name="troubleshoot-a-hub-spoke-network-connectivity-issue-between-a-web-app-and-the-spoke-virtual-network"></a>Устранение проблем с сетевым подключением в периферийной зоне между веб-приложением и периферийной виртуальной сетью

Чтобы устранить эту проблему, выполните следующие действия.

1. Войдите на портал Azure. 
1. В веб-приложении выберите сети, а затем — **Интеграция с виртуальной** **сетью**.
1. Проверьте, видите ли вы удаленную виртуальную сеть. Вручную введите адресное пространство удаленной виртуальной сети (**синхронизация сети** и **Добавление маршрутов**).

Дополнительные сведения см. в следующих статьях:

* [Интеграция приложения с виртуальной сетью Azure](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet)
* [О маршрутизации VPN типа "точка — сеть"](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-point-to-site-routing)

## <a name="troubleshoot-a-virtual-network-peering-configuration-error-message"></a>Устранение проблемы с сообщением об ошибке конфигурации пиринга виртуальной сети 

### <a name="current-tenant-tenant-id-isnt-authorized-to-access-linked-subscription"></a>Текущий `<TENANT ID>` клиента не имеет прав доступа к связанной подписке

Чтобы устранить эту проблему, см. раздел [Создание пиринга-Azure CLI](https://docs.microsoft.com/azure/virtual-network/create-peering-different-subscriptions#cli).

### <a name="not-connected"></a>Не подключено

Чтобы устранить эту проблему, удалите пиринг из обеих виртуальных сетей, а затем создайте их заново.

### <a name="failed-to-peer-a-databricks-virtual-network"></a>Не удалось установить одноранговое виртуальную сеть кирпичей

Чтобы устранить эту проблему, настройте пиринг виртуальных сетей в разделе **Azure Databricks**, а затем укажите целевую виртуальную сеть с помощью **идентификатора ресурса**. Дополнительные сведения см. [в разделе одноранговая виртуальная сеть данных в удаленной виртуальной сети](https://docs.azuredatabricks.net/administration-guide/cloud-configurations/azure/vnet-peering.html#id2).

## <a name="next-steps"></a>Дальнейшие действия

* [Устранение проблем с подключением между виртуальными машинами Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-connectivity-problem-between-vms)
