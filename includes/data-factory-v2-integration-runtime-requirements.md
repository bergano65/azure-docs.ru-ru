---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 08/12/2019
ms.author: jingwang
ms.openlocfilehash: 0b03957178af0578d2c6cd91d7377c93f413cec3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84629476"
---
<!--
    Separate the generic requirement on Self-hosted Integration Runtime set-up from connector articles.
-->
Если хранилище данных настроено одним из следующих способов, необходимо настроить локальную [среду выполнения интеграции](../articles/data-factory/create-self-hosted-integration-runtime.md) для подключения к хранилищу данных.

- Хранилище данных находится в локальной сети, в виртуальной сети Azure или в виртуальном частном облаке Amazon.
- Хранилище данных — это управляемая облачная служба данных, в которой доступ ограничен IP-адресами, список разрешений в правилах брандмауэра.
