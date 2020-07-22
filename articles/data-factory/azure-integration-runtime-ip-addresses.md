---
title: IP-адреса Azure Integration Runtime
description: Узнайте, какие IP-адреса необходимо разрешить из входящего трафика, чтобы правильно настроить брандмауэры для защиты сетевого доступа к хранилищам данных.
services: data-factory
ms.author: abnarain
author: nabhishek
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/06/2020
ms.openlocfilehash: 0c64a38295754e4754326dec126bfbc36e1bef61
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86523338"
---
# <a name="azure-integration-runtime-ip-addresses"></a>IP-адреса Azure Integration Runtime

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

IP-адреса, которые Azure Integration Runtime использует, зависят от региона, в котором находится среда выполнения интеграции Azure. *Все* Среды выполнения интеграции Azure, которые находятся в одном регионе, используют одни и те же диапазоны IP-адресов.

> [!IMPORTANT]  
> Потоки данных и Azure Integration Runtime, которые позволяют использовать управляемую виртуальную сеть, не поддерживают использование диапазонов фиксированных IP-адресов.
>
> Эти диапазоны IP-адресов можно использовать для перемещения данных, выполнения конвейера и внешних действий. Эти диапазоны IP-адресов можно использовать для список разрешений в хранилищах данных, группе безопасности сети (NSG) и брандмауэрах для входящего доступа из среды выполнения интеграции Azure. 

## <a name="azure-integration-runtime-ip-addresses-specific-regions"></a>Azure Integration Runtime IP-адреса: определенные регионы

Разрешите трафик с IP-адресов, указанных для среды выполнения интеграции Azure, в определенном регионе Azure, где находятся ваши ресурсы. Список диапазонов IP-адресов можно получить с помощью ссылки для [скачивания тегов службы из диапазона IP-адресов](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#discover-service-tags-by-using-downloadable-json-files). Например, если регион Azure — **AustraliaEast**, можно получить список диапазонов IP-адресов из **AustraliaEast**.


## <a name="known-issue-with-azure-storage"></a>Известная ошибка в службе хранилища Azure

* При подключении к учетной записи хранения Azure правила IP-сети не влияют на запросы, исходящие из среды выполнения интеграции Azure в том же регионе, что и учетная запись хранения. Дополнительные сведения см. в [этой статье](https://docs.microsoft.com/azure/storage/common/storage-network-security#grant-access-from-an-internet-ip-range). 

  Вместо этого мы рекомендуем использовать [Доверенные службы при подключении к службе хранилища Azure](https://techcommunity.microsoft.com/t5/azure-data-factory/data-factory-is-now-a-trusted-service-in-azure-storage-and-azure/ba-p/964993). 

## <a name="next-steps"></a>Дальнейшие действия

* [Вопросы безопасности при перемещении данных в фабрике данных Azure](data-movement-security-considerations.md)
