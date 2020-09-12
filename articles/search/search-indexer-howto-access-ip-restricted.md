---
title: Разрешить доступ к диапазонам IP-адресов индексатора
titleSuffix: Azure Cognitive Search
description: Руководство, в котором описывается настройка правил брандмауэра IP-адресов для доступа к индексаторам.
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/07/2020
ms.openlocfilehash: c80462707d3dccbb8fccff244017053c25ad46e8
ms.sourcegitcommit: 4a7a4af09f881f38fcb4875d89881e4b808b369b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/04/2020
ms.locfileid: "89463780"
---
# <a name="setting-up-ip-firewall-rules-to-enable-indexer-access"></a>Настройка правил брандмауэра IP-адресов для включения доступа к индексатору

Правила брандмауэра IP-адресов для ресурсов Azure, таких как учетные записи хранения, учетные записи Cosmos DB и серверы SQL Azure, допускают только трафик, исходящий из конкретных диапазонов IP-адресов для доступа к данным.

В этой статье описано, как настроить правила IP-адресов с помощью портал Azure для учетной записи хранения, чтобы Azure Когнитивный поиск индексаторы могли безопасно получать доступ к данным. Хотя это и характерно для хранилища, это правило можно напрямую преобразовать в другие ресурсы Azure, которые также предлагают правила IP-брандмауэра для защиты доступа к данным.

> [!NOTE]
> Правила брандмауэра IP для учетной записи хранения вступают в силу только в том случае, если учетная запись хранения и служба поиска находятся в разных регионах. Если программа установки не позволяет это сделать, рекомендуется использовать [параметр Trusted Service Exception](search-indexer-howto-access-trusted-service-exception.md).

## <a name="get-the-ip-address-of-the-search-service"></a>Получение IP-адреса службы поиска

Получите полное доменное имя (FQDN) службы поиска. Это будет выглядеть так `<search-service-name>.search.windows.net` :. Полное доменное имя можно узнать, найдя службу поиска на портал Azure.

   ![Получение полного доменного имени службы](media\search-indexer-howto-secure-access\search-service-portal.png "Получение полного доменного имени службы")

IP-адрес службы поиска можно получить, выполнив `nslookup` (или `ping` ) полное доменное имя. Это будет один из IP-адресов, добавляемых в правила брандмауэра.

```azurepowershell

nslookup contoso.search.windows.net
Server:  server.example.org
Address:  10.50.10.50

Non-authoritative answer:
Name:    <name>
Address:  150.0.0.1
Aliases:  contoso.search.windows.net
```

## <a name="get-the-ip-address-ranges-for-azurecognitivesearch-service-tag"></a>Получение диапазонов IP-адресов для тега службы "Азурекогнитивесеарч"

Диапазоны IP-адресов для `AzureCognitiveSearch` тега службы можно получить с помощью [API обнаружения (Предварительная версия)](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api-public-preview) или [загружаемого файла JSON](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files).

Для этого пошагового руководства при условии, что служба поиска является общедоступным облаком Azure, необходимо скачать [открытый JSON-файл Azure](https://www.microsoft.com/download/details.aspx?id=56519) .

   ![Скачать JSON файл](media\search-indexer-howto-secure-access\service-tag.png "Скачать JSON файл")

В файле JSON, предполагая, что служба поиска находится в западной центральной части США, список IP-адресов для среды выполнения индексатора с несколькими клиентами приведен ниже.

```json
    {
      "name": "AzureCognitiveSearch.WestCentralUS",
      "id": "AzureCognitiveSearch.WestCentralUS",
      "properties": {
        "changeNumber": 1,
        "region": "westcentralus",
        "platform": "Azure",
        "systemService": "AzureCognitiveSearch",
        "addressPrefixes": [
          "52.150.139.0/26",
          "52.253.133.74/32"
        ]
      }
    }
```

Для IP-адресов/32 удалите "/32" (52.253.133.74/32-> 52.253.133.74), другие можно использовать буквально.

## <a name="add-the-ip-address-ranges-to-ip-firewall-rules"></a>Добавление диапазонов IP-адресов в правила брандмауэра IP

Самый простой способ добавить диапазоны IP-адресов в правило брандмауэра учетной записи хранения — с помощью портал Azure. Найдите учетную запись хранения на портале и перейдите на вкладку "**брандмауэры и виртуальные сети**".

   ![Брандмауэр и виртуальные сети](media\search-indexer-howto-secure-access\storage-firewall.png "Брандмауэр и виртуальные сети")

Добавьте три IP-адреса, полученные ранее (1 — IP-адрес службы поиска, 2 для `AzureCognitiveSearch` тега службы) в диапазоне адресов, и нажмите кнопку "**сохранить**".

   ![Правила IP-адресов брандмауэра](media\search-indexer-howto-secure-access\storage-firewall-ip.png "Правила IP-адресов брандмауэра")

После того, как индексаторы смогут получить доступ к данным в учетной записи хранения, обновление правил брандмауэра занимает 5-10 минут.

## <a name="next-steps"></a>Next Steps

Теперь, когда вы узнали, как получить два набора IP-адресов, чтобы разрешить доступ к индексам, воспользуйтесь следующими ссылками, чтобы обновить правила брандмауэра IP-адресов для некоторых распространенных источников данных.

- [Настройка брандмауэров службы хранилища Azure](https://docs.microsoft.com/azure/storage/common/storage-network-security)
- [Настройка брандмауэра IP для CosmosDB](https://docs.microsoft.com/azure/cosmos-db/firewall-support)
- [Настройка брандмауэра IP для Azure SQL Server](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)