---
title: Что собой представляет Приватный канал Azure?
description: Общие сведения о функциях, архитектуре и реализации Приватного канала Azure. Узнайте, как работают частные конечные точки и служба Приватного канала Azure и как их использовать.
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: overview
ms.date: 06/02/2020
ms.author: allensu
ms.custom: fasttrack-edit
ms.openlocfilehash: 4752fe65cf06b894413df2fda0decdfbb427afd5
ms.sourcegitcommit: 0a5bb9622ee6a20d96db07cc6dd45d8e23d5554a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/05/2020
ms.locfileid: "84449385"
---
# <a name="what-is-azure-private-link"></a>Что собой представляет Приватный канал Azure? 
Приватный канал Azure обеспечивает доступ к службам Azure PaaS (например, к службе хранилища Azure и Базе данных SQL), а также размещенным в Azure службам, которые принадлежат клиенту или партнеру, через [частную конечную точку](private-endpoint-overview.md) виртуальной сети.

Трафик между виртуальной сетью и службой проходит через магистральную сеть Майкрософт. Предоставление доступа к службе через общедоступный Интернет больше не требуется. Вы можете создать собственную [службу приватного канала](private-link-service-overview.md) в виртуальной сети и предоставлять ее клиентам. Настройка и потребление с использованием Приватного канала Azure согласованы между службами Azure PaaS, клиентскими и общими партнерскими службами.

> [!IMPORTANT]
> Приватный канал Azure теперь является общедоступным. Как частная конечная точка, так и служба "Приватный канал" (служба за стандартной подсистемой балансировки нагрузки) являются общедоступными. Различные службы PaaS Azure будут подключаться к Приватному каналу Azure по разным расписаниям. Проверьте [доступность](https://docs.microsoft.com/azure/private-link/private-link-overview#availability) в разделе ниже для получения точного статуса Azure PaaS по Приватному каналу. Сведения об известных ограничениях см. в разделах о [частной конечной точке](private-endpoint-overview.md#limitations) и [службе "Приватный канал"](private-link-service-overview.md#limitations). 

![Обзор закрытой конечной точки](media/private-link-overview/private-endpoint.png)

## <a name="key-benefits"></a>Основные преимущества
Приватный канал Azure обеспечивает следующие преимущества:  
- **Закрытый доступ к службам на платформе Azure**: Подключайте виртуальную сеть к службам, работающим в Azure, не используя публичный IP-адрес источника или пункта назначения. Поставщики услуг могут преобразовать свои службы в собственной виртуальной сети, а потребители могут получить доступ к этим службам в своей локальной виртуальной сети. Платформа Приватного канала будет поддерживать подключение между потребителем и службами через магистральную сеть Azure. 
 
- **Локальные и одноранговые сети**: доступ к службам, выполняемым в Azure, из локальной сети через частные одноранговые или VPN-туннели ExpressRoute и одноранговые виртуальные сети с помощью закрытых конечных точек. Вам не нужно настраивать общедоступный пиринг или пересекать Интернет для доступа к службе. Приватный канал обеспечивает безопасный способ переносить рабочие нагрузки в Azure.
 
- **Защита от утечки данных**: частная конечная точка сопоставляется с экземпляром ресурса PaaS, а не со всей службой. Объекты-получатели могут подключаться только к определенному ресурсу. Доступ к любому другому ресурсу в службе блокируется. Этот механизм обеспечивает защиту от рисков утечки данных. 
 
- **Глобальный доступ**: Устанавливайте частное подключение к службам, работающим в других регионах. Виртуальная сеть объекта-получателя может находиться в регионе А и подключаться к службам за Приватным каналом в регионе В.  
 
- **Расширьте свои собственные службы**: Воспользуйтесь теми же возможностями и функциональностью, чтобы преобразовать для частного просмотра службу объектам-получателям в Azure. Разместив свою службу за Azure Load Balancer уровня "Стандартный", вы можете включить его для Приватного канала. Затем объект-получатель может подключиться непосредственно к службе с помощью закрытой конечной точки в собственной виртуальной сети. Вы можете управлять этими запросами на подключение, используя поток вызова утверждения. Приватный канал Azure работает для объектов-получателей и служб, принадлежащих к разным клиентам Azure Active Directory. 

## <a name="availability"></a>Доступность 
 В следующей таблице перечислены службы Приватного канала и регионы, в которых они доступны. 

|Поддерживаемые службы  |Доступные регионы | Состояние  |
|:-------------------|:-----------------|:--------|
|Службы Приватного канала за Azure Load Balancer (цен. категория "Стандартный") | Все общедоступные регионы  | GA <br/> [Дополнительные сведения](https://docs.microsoft.com/azure/private-link/private-link-service-overview) |
| Хранилище Azure        |  Все общедоступные регионы       | GA <br/> [Дополнительные сведения](/azure/storage/common/storage-private-endpoints)  |
| Azure Data Lake Storage 2-го поколения        |  Все общедоступные регионы      | GA <br/> [Дополнительные сведения](/azure/storage/common/storage-private-endpoints)  |
|  База данных SQL Azure         | Все общедоступные регионы      |   GA <br/> [Дополнительные сведения](https://docs.microsoft.com/azure/sql-database/sql-database-private-endpoint-overview)      |
|Azure Synapse Analytics (Хранилище данных SQL)| Все общедоступные регионы |GA <br/> [Дополнительные сведения](https://docs.microsoft.com/azure/sql-database/sql-database-private-endpoint-overview)|
|Azure Cosmos DB|  Все общедоступные регионы |GA <br/> [Дополнительные сведения](https://docs.microsoft.com/azure/cosmos-db/how-to-configure-private-endpoints)|
|  База данных Azure для PostgreSQL — отдельный сервер         | Все общедоступные регионы      |   GA <br/> [Дополнительные сведения](https://docs.microsoft.com/azure/postgresql/concepts-data-access-and-security-private-link)      |
|  База данных Azure для MySQL         | Все общедоступные регионы      |   GA <br/> [Дополнительные сведения](https://docs.microsoft.com/azure/mysql/concepts-data-access-security-private-link)     |
|  База данных Azure для MariaDB         | Все общедоступные регионы      |   GA <br/> [Дополнительные сведения](https://docs.microsoft.com/azure/mariadb/concepts-data-access-security-private-link)      |
|  Azure Key Vault         | Все общедоступные регионы      |   GA   <br/> [Дополнительные сведения](https://docs.microsoft.com/azure/key-vault/private-link-service)   |
|Служба Azure Kubernetes — API Kubernetes | Все общедоступные регионы      |   GA   <br/> [Дополнительные сведения](https://docs.microsoft.com/azure/aks/private-clusters)   |
|Поиск Azure | Все общедоступные регионы |   GA   <br/> [Дополнительные сведения](https://docs.microsoft.com/azure/search/search-security-overview#endpoint-access)    |
|Реестр контейнеров Azure | Все общедоступные регионы      |   GA   <br/> [Дополнительные сведения](https://docs.microsoft.com/azure/container-registry/container-registry-private-link)   |
|Конфигурация приложений Azure | Все общедоступные регионы      |   Preview (Предварительный просмотр)   |
|Azure Backup | Все общедоступные регионы     |   GA   <br/> [Дополнительные сведения](https://docs.microsoft.com/azure/backup/private-endpoints)   |
|концентратору событий Azure | Все общедоступные регионы      |    GA   <br/> [Дополнительные сведения](https://docs.microsoft.com/azure/event-hubs/private-link-service)  |
|Служебная шина Azure | Все общедоступные регионы      |  GA   <br/> [Дополнительные сведения](https://docs.microsoft.com/azure/service-bus-messaging/private-link-service)    |
|Ретранслятор Azure | Все общедоступные регионы      |   Preview (Предварительный просмотр) <br/> [Дополнительные сведения](https://docs.microsoft.com/azure/service-bus-relay/private-link-service)  |
|Сетка событий Azure| Все общедоступные регионы       |   GA   <br/> [Дополнительные сведения](https://docs.microsoft.com/azure/event-grid/network-security) |
|Веб-приложения Azure | Все общедоступные регионы      |   Preview (Предварительный просмотр)   <br/> [Дополнительные сведения](https://docs.microsoft.com/azure/app-service/networking/private-endpoint)   |
|Машинное обучение Azure | Восточная часть США, западная часть США 2, центрально-южная часть США      |   Preview (Предварительный просмотр)   <br/> [Дополнительные сведения](https://docs.microsoft.com/azure/machine-learning/how-to-configure-private-link)   |
| Центр Интернета вещей | Все общедоступные регионы    |   Preview (Предварительный просмотр)   <br/> [Дополнительные сведения](https://docs.microsoft.com/azure/iot-hub/virtual-network-support ) |
| Azure SignalR | Восточная часть США, западная часть США 2, центрально-южная часть США      |   Preview (Предварительный просмотр)   <br/> [Дополнительные сведения](https://aka.ms/asrs/privatelink)   |
| Azure Monitor <br/>(Log Analytics и Application Insights) | Все общедоступные регионы      |   GA   <br/> [Дополнительные сведения](https://docs.microsoft.com/azure/azure-monitor/platform/private-link-security)   |


Самые актуальные уведомления доступны на странице [обновлений виртуальной сети Azure](https://azure.microsoft.com/updates/?product=virtual-network).

## <a name="logging-and-monitoring"></a>Ведение журналов и мониторинг

Приватный канал Azure интегрируется с Azure Monitor. Это сочетание обеспечивает следующие возможности:

 - Архивация журналов в учетную запись хранения.
 - Потоковая передача событий в концентратор событий.
 - Ведение журнала Azure Monitor.

В Azure Monitor можно получить доступ к следующей информации: 
- **Закрытая конечная точка**: 
    - Данные, обработанные закрытой конечной точкой (в/с)
 
- **Служба "Приватный канал"** :
    - Данные, обработанные Приватным каналом (в/с)
    - Доступность порта NAT  
 
## <a name="pricing"></a>Цены   
Дополнительные сведения о ценах см. на [странице цен на службу "Приватный канал" Azure](https://azure.microsoft.com/pricing/details/private-link/).
 
## <a name="faqs"></a>Часто задаваемые вопросы  
Часто задаваемые вопросы см. в разделе [Часто задаваемые вопросы о Приватном канале Azure](private-link-faq.md).
 
## <a name="limits"></a>Ограничения  
Сведения об ограничениях см. в разделе об [ограничениях службы "Приватный канал" Azure](../azure-resource-manager/management/azure-subscription-service-limits.md#private-link-limits).

## <a name="service-level-agreement"></a>Соглашение об уровне обслуживания
Сведения об уровне обслуживания см. в статье [Соглашение об уровне обслуживания для Приватный канал Azure](https://azure.microsoft.com/support/legal/sla/private-link/v1_0/).

## <a name="next-steps"></a>Дальнейшие действия

- [Краткое руководство. Создание частной конечной точки с помощью портала Azure](create-private-endpoint-portal.md).
- [Краткое руководство. Создание службы "Приватный канал" с помощью портала Azure](create-private-link-service-portal.md).


 
