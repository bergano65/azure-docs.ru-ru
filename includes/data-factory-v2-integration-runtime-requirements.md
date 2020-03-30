---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 08/12/2019
ms.author: jingwang
ms.openlocfilehash: 2e90d218aa6dc90746ba0e928fb3393f0bdb5e5a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "68966358"
---
<!--
    Separate the generic requirement on Self-hosted Integration Runtime set-up from connector articles.
-->
Если хранилище данных настроено одним из следующих способов, необходимо настроить [автономное время интеграции,](../articles/data-factory/create-self-hosted-integration-runtime.md) чтобы подключиться к этому хранилику данных:

- Хранилище данных находится внутри внутренней сети, внутри виртуальной сети Azure или внутри виртуального облака Amazon.
- Хранилище данных — это управляемая служба облачных данных, доступ к которой ограничен iPs, включенным в белый список в правилах брандмауэра.
