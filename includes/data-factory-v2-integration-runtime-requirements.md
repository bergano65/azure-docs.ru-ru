---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 07/13/2020
ms.author: jingwang
ms.openlocfilehash: 994b8285999bfa52e2aea9a57ad832aefddcfb76
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86545082"
---
<!--
    Separate the generic requirement on Self-hosted Integration Runtime set-up from connector articles.
-->
Если хранилище данных расположено в локальной сети, в виртуальной сети Azure или в виртуальном частном облаке Amazon, необходимо настроить локальную [среду выполнения интеграции](../articles/data-factory/create-self-hosted-integration-runtime.md) для подключения к ней.

Если хранилище данных является управляемой облачной службой данных, доступ к которой ограничивается IP-адресами, список разрешений в правилах брандмауэра, вы можете использовать среду выполнения интеграции Azure и добавить в список разрешений [IP](../articles/data-factory/azure-integration-runtime-ip-addresses.md) . 

Сведения о механизмах сетевой безопасности, поддерживаемых фабрикой данных для доступа к хранилищам данных в целом, см. в статье [стратегии доступа к данным](../articles/data-factory/data-access-strategies.md) .
