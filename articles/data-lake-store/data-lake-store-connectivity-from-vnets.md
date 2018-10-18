---
title: Подключение к Azure Data Lake Storage 1-го поколения из виртуальных сетей | Документация Майкрософт
description: Сведения о подключении к Azure Data Lake Storage 1-го поколения из виртуальных сетей Azure.
services: data-lake-store,data-catalog
documentationcenter: ''
author: esung22
manager: jhubbard
editor: cgronlun
ms.assetid: 683fcfdc-cf93-46c3-b2d2-5cb79f5e9ea5
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.date: 01/31/2018
ms.author: elsung
ms.openlocfilehash: 130d0154fc0558ae7284e8407ba88fda3a2a53d5
ms.sourcegitcommit: 794bfae2ae34263772d1f214a5a62ac29dcec3d2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/11/2018
ms.locfileid: "44391306"
---
# <a name="access-azure-data-lake-storage-gen1-from-vms-within-an-azure-vnet"></a>Доступ к Azure Data Lake Storage 1-го поколения с виртуальных машин в виртуальной сети Azure
Azure Data Lake Storage 1-го поколения — это служба PaaS, которая использует общедоступные IP-адреса в Интернете. Любой сервер с доступом к общедоступному Интернету, как правило, может подключаться к конечным точкам Azure Data Lake Storage 1-го поколения. По умолчанию все виртуальные машины, которые находятся в виртуальных сетях Azure, могут подключиться к Интернету, а значит и получить доступ к Azure Data Lake Storage 1-го поколения. Тем не менее для виртуальных машин в виртуальной сети можно настроить ограничение доступа к Интернету. Такие виртуальные машины также не смогут подключаться к Azure Data Lake Storage 1-го поколения. Блокировать доступ к общедоступному Интернету для виртуальных машин в виртуальных сетях Azure можно с помощью одного из следующих методов.

* Настройка групп безопасности сети (NSG).
* Настройка определяемых пользователем маршрутов (UDR).
* Обмен маршрутами через BGP (стандартный протокол динамической маршрутизации) при использовании подключения ExpressRoute, блокирующего доступ к Интернету.

В этой статье вы узнаете, как включить доступ к Azure Data Lake Storage 1-го поколения с виртуальных машин Azure, для которых был ограничен доступ к ресурсам, с помощью одного из трех перечисленных выше методов.

## <a name="enabling-connectivity-to-azure-data-lake-storage-gen1-from-vms-with-restricted-connectivity"></a>Настройка подключения к Azure Data Lake Storage 1-го поколения с виртуальных машин с ограниченным доступом
Чтобы обеспечить подключение к Azure Data Lake Storage 1-го поколения с таких виртуальных машин, для них необходимо настроить доступ к IP-адресу, по которому доступна учетная запись Azure Data Lake Storage 1-го поколения. IP-адреса для учетных записей Azure Data Lake Storage 1-го поколения можно определить, разрешая DNS-имена учетных записей (`<account>.azuredatalakestore.net`). Для разрешения DNS-имен учетных записей можно использовать такой инструмент, как **nslookup**. Откройте окно командной строки на компьютере и выполните следующую команду.

    nslookup mydatastore.azuredatalakestore.net

Результат будет выглядеть, как показано ниже. Значение свойства **Address** — это IP-адрес, связанный с учетной записью Data Lake Storage 1-го поколения.

    Non-authoritative answer:
    Name:    1434ceb1-3a4b-4bc0-9c69-a0823fd69bba-mydatastore.projectcabostore.net
    Address:  104.44.88.112
    Aliases:  mydatastore.azuredatalakestore.net


### <a name="enabling-connectivity-from-vms-restricted-by-using-nsg"></a>Настройка подключения из виртуальных машин, ограниченных с помощью NSG
При использовании правила NSG для блокировки доступа к Интернету можно создать другую группу безопасности сети, обеспечивающую доступ к IP-адресу Azure Data Lake Storage 1-го поколения. Дополнительные сведения о правилах NSG см. в статье [Безопасность сети](../virtual-network/security-overview.md). Инструкции по созданию NSG см. в статье [Создание групп безопасности сети](../virtual-network/tutorial-filter-network-traffic.md).

### <a name="enabling-connectivity-from-vms-restricted-by-using-udr-or-expressroute"></a>Настройка подключения из виртуальных машин, ограниченных с помощью UDR или ExpressRoute
При использовании маршрутов UDR или маршрутов, обмениваемых через BGP, для блокирования доступа к Интернету необходимо настроить специальный маршрут, с помощью которого виртуальные машины в таких подсетях смогут получать доступ к конечным точкам Data Lake Storage 1-го поколения. Дополнительные сведения см. в статье [Маршрутизация трафика в виртуальной сети](../virtual-network/virtual-networks-udr-overview.md). Инструкции по созданию определяемых пользователем маршрутов см. в статье [Создание определяемых пользователем маршрутов (UDR) в Resource Manager с помощью PowerShell](../virtual-network/tutorial-create-route-table-powershell.md).

### <a name="enabling-connectivity-from-vms-restricted-by-using-expressroute"></a>Настройка подключения из виртуальных машин, ограниченных с помощью ExpressRoute
Если настроен канал ExpressRoute, локальные серверы могут получать доступ к Data Lake Storage 1-го поколения через общедоступный пиринг. Дополнительные сведения о настройке ExpressRoute для общедоступного пиринга см. в статье [Вопросы и ответы по ExpressRoute](../expressroute/expressroute-faqs.md).

## <a name="see-also"></a>См. также
* [Обзор Azure Data Lake Storage Gen1](data-lake-store-overview.md)
* [Обеспечение безопасности в Azure Data Lake Storage 1-го поколения](data-lake-store-security-overview.md)

