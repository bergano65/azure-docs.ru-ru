---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 07/13/2020
ms.author: jingwang
ms.openlocfilehash: fbde8bc28f8fc34b7a6a6443950b8733c6dcff45
ms.sourcegitcommit: 67e8e1caa8427c1d78f6426c70bf8339a8b4e01d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/02/2020
ms.locfileid: "91672277"
---
<!--
    Separate the generic requirement on Self-hosted Integration Runtime set-up from connector articles.
-->
Если хранилище данных размещено в локальной сети, виртуальной сети Azure или виртуальном частном облаке Amazon, для подключения к нему нужно настроить [локальную среду выполнения интеграции](../articles/data-factory/create-self-hosted-integration-runtime.md).

Если же хранилище данных представляет собой управляемую облачную службу данных, можно использовать среду выполнения интеграции Azure. Если доступ предоставляется только по IP-адресам, утвержденным в правилах брандмауэра, вы можете добавить [IP-адреса Azure Integration Runtime](../articles/data-factory/azure-integration-runtime-ip-addresses.md) в список разрешений. 

Дополнительные сведения о вариантах и механизмах обеспечения сетевой безопасности, поддерживаемых Фабрикой данных, см. в статье [Стратегии получения доступа к данным](../articles/data-factory/data-access-strategies.md).
