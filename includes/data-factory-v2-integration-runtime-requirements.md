---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 08/12/2019
ms.author: jingwang
ms.openlocfilehash: 2e90d218aa6dc90746ba0e928fb3393f0bdb5e5a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "68966358"
---
<!--
    Separate the generic requirement on Self-hosted Integration Runtime set-up from connector articles.
-->
Если хранилище данных настроено одним из приведенных ниже способов, для подключения к нему необходимо настроить [локальную среду выполнения интеграции](../articles/data-factory/create-self-hosted-integration-runtime.md).

- Хранилище данных находится в локальной сети, в виртуальной сети Azure или в виртуальном частном облаке Amazon.
- Хранилище данных — это управляемая облачная служба данных, доступ к которой ограничен IP-адресами, указанными в списке разрешенных в правилах брандмауэра.
