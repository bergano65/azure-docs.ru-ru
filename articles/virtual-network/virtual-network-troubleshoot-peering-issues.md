---
title: Устранение неполадок с пирингом виртуальной сети
description: Действия по устранению большинства проблем пиринга в виртуальной сети.
services: virtual-network
documentationcenter: na
author: v-miegge
manager: dcscontentpm
editor: ''
ms.assetid: 1a3d1e84-f793-41b4-aa04-774a7e8f7719
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/28/2019
ms.author: kaushika
ms.openlocfilehash: fc01a20a077a1c624ed490600db919fe3197556c
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/11/2019
ms.locfileid: "70901767"
---
# <a name="troubleshoot-virtual-network-peering-issues"></a>Устранение неполадок с пирингом виртуальной сети

В этом руководстве по устранению неполадок приведены инструкции по устранению большинства проблем с [пирингом виртуальных сетей](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) .

![ЭСКИЗ](./media/virtual-network-troubleshoot-peering-issues/4489538_en_1.png)

## <a name="scenario-1-configure-virtual-network-peering-between-two-virtual-networks"></a>Сценарий 1. Настройка пиринга виртуальной сети между двумя виртуальными сетями

Являются ли виртуальные сети в одной подписке или в разных подписках?

### <a name="connection-type-1-the-virtual-networks-are-in-the-same-subscription"></a>Тип подключения 1: Виртуальные сети находятся в одной подписке

Чтобы настроить пиринг виртуальной сети для виртуальных сетей, находящихся в одной подписке, используйте методы, приведенные в следующих статьях, в зависимости от ситуации.

* Если виртуальные сети находятся в **одном регионе**, выполните действия по [созданию пиринга для виртуальных сетей в одной подписке](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering#create-a-peering).
* Если виртуальные сети находятся в **разных регионах**, выполните действия по настройке [пиринга глобальных виртуальных сетей](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview).  

> [!Note]
> Подключение не будет работать через пиринг глобальной виртуальной сети для следующих ресурсов: 
>
> * Виртуальные машины за базовым SKU ILB
> * Кэш Redis (использует базовый номер SKU ILB)
> * Шлюз приложений (использует базовый SKU ILB)
> * Масштабируемые наборы (использует базовый номер SKU ILB)
> * Кластеры Service Fabric (использует базовый номер SKU ILB)
> * SQL Always-On (использует базовый номер SKU ILB)
> * Среды службы приложений (ASE) (использует базовый номер SKU ILB)
> * Управление API (использует базовый номер SKU ILB)
> * Azure Active Directory служба домена (добавляет) (использует базовый номер SKU ILB)

Дополнительные сведения см. в разделе [требования и ограничения](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#requirements-and-constraints) глобального пиринга.

### <a name="connection-type-2-the-virtual-networks-are-in-different-subscriptions-or-ad-tenants"></a>Тип соединения 2: Виртуальные сети находятся в разных подписках или клиентах AD.

Чтобы настроить пиринг виртуальной сети для виртуальных сетей в разных подписках или клиентах Active Directory, выполните действия, описанные в статье [Создание пиринга в разных подписках для Azure CLI](https://docs.microsoft.com/azure/virtual-network/create-peering-different-subscriptions#cli).

> [!Note]
> Чтобы настроить пиринг сети, необходимо иметь разрешения **участника сети** в обеих подписках. Дополнительные сведения см. в разделе [разрешения пиринга](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering%23permissions).

## <a name="scenario-2-configure-virtual-network-peering-with-hub-spoke-topology-that-uses-on-premises-resources"></a>Сценарий 2. Настройка пиринга виртуальных сетей с помощью топологии Hub-лучевой сети, использующей локальные ресурсы

![ЭСКИЗ](./media/virtual-network-troubleshoot-peering-issues/4488712_en_1a.png)

### <a name="connection-type-1-for-site-to-site-connection-or-expressroute-connection"></a>Тип подключения 1: Для подключения "сеть — сеть" или ExpressRoute

Выполните действия, описанные в разделе. [Настройте транзит VPN-шлюза для пиринга виртуальной сети](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-peering-gateway-transit?toc=/azure/virtual-network/toc.json).

### <a name="connection-type-2-for-point-to-site-connections"></a>Тип соединения 2: Для подключений "точка — сеть"

1. Выполните действия, описанные в разделе. [Настройте транзит VPN-шлюза для пиринга виртуальной сети](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-peering-gateway-transit?toc=/azure/virtual-network/toc.json).
2. После того как пиринг виртуальной сети будет установлен или изменен, необходимо снова скачать и установить пакет "точка — сеть", чтобы клиенты "точка — сеть" могли получить обновленные маршруты к виртуальной сети "звезда".

## <a name="scenario-3-configure-virtual-network-peering-with-hub-spoke-topology-for-azure-virtual-network"></a>Сценарий 3. Настройка пиринга виртуальной сети с помощью топологии Hub-лучевой виртуальной сети Azure

![ЭСКИЗ](./media/virtual-network-troubleshoot-peering-issues/4488712_en_1b.png)

### <a name="connection-type-1-the-virtual-networks-are-in-the-same-region"></a>Тип подключения 1: Виртуальные сети находятся в одном регионе

Необходимо настроить сетевой виртуальный модуль (NVA) в виртуальной сети концентратора и иметь определяемые пользователем маршруты со следующим прыжком "сетевой виртуальный модуль", примененный в периферийных виртуальных сетях. Дополнительные сведения см. в разделе [цепочка служб](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#service-chaining).

> [!Note]
> Если вам нужна помощь по настройке NVA, обратитесь к [поставщику NVA](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines).

Справку по устранению неполадок при настройке и маршрутизации устройств NVA см. [в статье проблемы с виртуальным сетевым устройством в Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-nva).

### <a name="connection-type-2-the-virtual-networks-are-in-different-regions"></a>Тип соединения 2: Виртуальные сети находятся в разных регионах

Теперь поддерживается транзитный транспорт глобальной виртуальной сети. Подключение не работает через глобальную пиринг виртуальной сети для следующих ресурсов:

* Виртуальные машины за базовым SKU ILB
* Кэш Redis (использует базовый номер SKU ILB)
* Шлюз приложений (использует базовый SKU ILB)
* Масштабируемые наборы (использует базовый номер SKU ILB)
* Кластеры Service Fabric (использует базовый номер SKU ILB)
* SQL Always-On (использует базовый номер SKU ILB)
* Среды службы приложений (ASE) (использует базовый номер SKU ILB)
* Управление API (использует базовый номер SKU ILB)
* Azure Active Directory служба домена (добавляет) (использует базовый номер SKU ILB)

Дополнительные сведения о глобальных требованиях к пиринга и ограничений см. в статье [пиринг виртуальных сетей](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#requirements-and-constraints).

## <a name="scenario-4-i-have-a-connectivity-issue-between-two-peered-virtual-networks"></a>Сценарий 4. У меня есть проблемы с подключением между двумя одноранговыми виртуальными сетями

Войдите в [портал Azure](https://portal.azure.com/) с помощью учетной записи, обладающей необходимыми [ролями и разрешениями](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering#roles-permissions). Выберите виртуальную сеть, выберите **пиринг**, а затем проверьте поле **состояние** . Что такое состояние?

### <a name="connection-type-1-the-peering-status-shows-connected"></a>Тип подключения 1: Состояние пиринга отображается как "подключено"

Чтобы устранить эту проблему, выполните следующие действия.

1. Проверьте потоки сетевого трафика.

   Чтобы определить, есть ли NSG или UDR, вызывающие помехи в потоках трафика, используйте для [устранения неполадок подключения](https://docs.microsoft.com/azure/network-watcher/network-watcher-connectivity-overview) и [IP-потока проверку](https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview) с исходной виртуальной машины на целевую виртуальную машину.

   Если вы используете брандмауэр или устройство NVA, выполните следующие действия. 
   1. Задокументируйте параметры UDR, чтобы их можно было восстановить после завершения этого шага.
   2. Удалите UDR из подсети исходной виртуальной машины или сетевой карты, которая указывает на NVA в качестве следующего прыжка. Проверьте подключение из исходной виртуальной машины непосредственно к назначению, которое обходит NVA. Если этот шаг работает, обратитесь к [средству устранения неполадок NVA](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-nva).

2. Выполните трассировку сети. 
   1. Запустите трассировку сети на целевой виртуальной машине. Для Windows можно использовать **netsh**. Для Linux используйте **TCPDump**.
   2. Запустите **TcpPing** или **PsPing** из источника в конечный IP-адрес.

   * Вот пример команды **TcpPing** :`tcping64.exe -t <destination VM address> 3389`

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
   > * SQL Always-On (использует базовый номер SKU ILB)
   > * Среды службы приложений (ASE) (использует базовый номер SKU ILB)
   > * Управление API (использует базовый номер SKU ILB)
   > * Azure Active Directory служба домена (добавляет) (использует базовый номер SKU ILB)

Дополнительные сведения см. в разделе [требования и ограничения](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#requirements-and-constraints) глобального пиринга.

### <a name="connection-type-2-the-peering-status-shows-disconnected"></a>Тип соединения 2: Состояние пиринга показывает "отключено"

Необходимо удалить пиринг из виртуальных сетей и создать их повторно.

## <a name="scenario-5-i-have-a-connectivity-issue-between-a-hub-spoke-virtual-network-and-on-premises-resource"></a>Сценарий 5. У меня есть проблемы с подключением между центрально-лучевой виртуальной сетью и локальным ресурсом.

Используете ли вы сторонний NVA или VPN-шлюз?

### <a name="connection-type-1-my-network-uses-a-third-party-nva-or-vpn-gateway"></a>Тип подключения 1: Моя сеть использует NVA или VPN-шлюз стороннего производителя

Сведения об устранении проблем с подключением, влияющих на NVA или VPN-шлюз стороннего производителя, см. в следующих статьях:

* [Средство устранения неполадок NVA](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-nva)
* [Цепочка служб](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#service-chaining)

### <a name="connection-type-2-my-network-does-not-a-third-party-nva-or-vpn-gateway"></a>Тип соединения 2: Моя сеть не является сторонним NVA или VPN-шлюзом

Есть ли у концентратора и периферийных виртуальных сетей VPN-шлюз?

#### <a name="both-the-hub-and-spoke-virtual-networks-have-a-vpn-gateway"></a>И концентратор, и периферийные виртуальные сети имеют VPN-шлюз.

Использование удаленного шлюза не поддерживается.

В связи с ограничением пиринга виртуальной сети **Использование удаленного шлюза** не поддерживается в периферийной виртуальной сети, если у периферийной виртуальной сети уже есть VPN-шлюз.

#### <a name="both-the-hub-and-spoke-virtual-networks-dont-have-a-vpn-gateway"></a>У виртуальных сетей концентратора и звезды нет VPN-шлюза.

Для подключений типа "сеть — сеть" или ExpressRoute Проверьте эти основные причины проблем с подключением к удаленной виртуальной сети из локальной среды.

* Убедитесь, что на виртуальной сети с шлюзом установлен флажок **Разрешить перенаправленный трафик** .
* Убедитесь, что флажок **использовать удаленный шлюз** установлен в виртуальной сети без шлюза.
* Попросите администратора сети проверить свои локальные устройства, чтобы убедиться в том, что для них Добавлено адресное пространство удаленной виртуальной сети.

Для подключений "точка — сеть":

* Убедитесь, что на виртуальной сети с шлюзом установлен флажок **Разрешить перенаправленный трафик** .
* Убедитесь, что флажок **использовать удаленный шлюз** установлен в виртуальной сети без шлюза.
* Скачайте и установите клиентский пакет "точка — сеть" еще раз. Маршруты виртуальных сетей, которые являются новыми, не добавляют маршруты к клиентам "точка — сеть".

## <a name="scenario-6-i-have-a-hub-spoke-network-connectivity-issue-between-spoke-virtual-networks-in-the-same-region"></a>Сценарий 6. У меня есть проблемы с сетевым подключением между периферийными виртуальными сетями в одном регионе.

Необходимо иметь NVA в сети центра, настроить определяемые пользователем маршруты в периферийных серверах, где NVA установлен в качестве следующего прыжка, и разрешить **перенаправленный трафик** в виртуальной сети концентратора.

Дополнительные сведения см. в разделе [цепочка служб](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#service-chaining)и обсуждайте эти требования с [поставщиком NVA](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines) по своему усмотрению.

## <a name="scenario-7-i-have-a-hub-spoke-network-connectivity-issue-between-spoke-virtual-networks-in-different-regions"></a>Сценарий 7. У меня есть проблемы с сетевым подключением между периферийными виртуальными сетями в разных регионах.

Теперь поддерживается транзитный транспорт глобальной виртуальной сети. Подключение не будет работать через пиринг глобальной виртуальной сети для следующих ресурсов:

* Виртуальные машины за базовым SKU ILB
* Кэш Redis (использует базовый номер SKU ILB)
* Шлюз приложений (использует базовый SKU ILB)
* Масштабируемые наборы (использует базовый номер SKU ILB)
* Кластеры Service Fabric (использует базовый номер SKU ILB)
* SQL Always-On (использует базовый номер SKU ILB)
* Среды службы приложений (ASE) (использует базовый номер SKU ILB)
* Управление API (использует базовый номер SKU ILB)
* Azure Active Directory служба домена (добавляет) (использует базовый номер SKU ILB)

Дополнительные сведения см. в разделе [требования и ограничения](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#requirements-and-constraints) глобального пиринга и [различных топологий VPN](https://blogs.msdn.microsoft.com/igorpag/2016/02/11/hubspoke-daisy-chain-and-full-mesh-vnet-topologies-in-azure-arm-v2/).

## <a name="scenario-8-i-have-a-hub-spoke-network-connectivity-issue-between-a-web-app-and-the-spoke-virtual-network"></a>Сценарий 8. У меня есть проблемы с сетевым подключением между веб-приложением и периферийной виртуальной сетью.

Чтобы устранить эту проблему, выполните следующие действия.

1. Войдите в портал Azure. Перейдите к веб-приложению, выберите **сети**, а затем — **Интеграция с виртуальной**сетью.
2. Проверьте, видите ли вы удаленную виртуальную сеть. Вручную введите адресное пространство удаленной виртуальной сети (**синхронизация сети** и **Добавление маршрутов**).

Дополнительные сведения см. в следующих статьях:

* [Интеграция приложения с виртуальной сетью Azure](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet)
* [О маршрутизации VPN типа "точка — сеть"](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-point-to-site-routing)

## <a name="scenario-9-i-receive-an-error-when-configuring-virtual-network-peering"></a>Сценарий 9. При настройке пиринга между виртуальными сетями появляется сообщение об ошибке

### <a name="error-1-current-tenant-tenant-id-isnt-authorized-to-access-linked-subscription"></a>Ошибка 1 Текущий клиент `<TENANT ID>` не имеет прав доступа к связанной подписке

Чтобы устранить эту проблему, выполните действия, описанные в разделе [Создание пиринга-Azure CLI](https://docs.microsoft.com/azure/virtual-network/create-peering-different-subscriptions#cli).

### <a name="error-2-not-connected"></a>Ошибка 2: Не подключено

Необходимо удалить одноранговые узлы из виртуальных сетей и создать их заново.

### <a name="error-3-failed-to-peer-a-databricks-virtual-network"></a>Ошибка 3: Не удалось установить одноранговое виртуальную сеть кирпичей

Чтобы устранить эту проблему, настройте пиринг виртуальной сети в колонке **Azure Databricks** , а затем укажите целевую виртуальную сеть с помощью **идентификатора ресурса**. Дополнительные сведения см. [в разделе одноранговая виртуальная сеть данных в удаленной виртуальной сети](https://docs.azuredatabricks.net/administration-guide/cloud-configurations/azure/vnet-peering.html#id2).

## <a name="next-steps"></a>Следующие шаги

* [Устранение проблем с подключением между виртуальными машинами Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-connectivity-problem-between-vms)