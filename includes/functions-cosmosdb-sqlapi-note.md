---
title: включение файла
description: включение файла
services: functions
author: ggailey777
manager: jeconnoc
ms.service: functions
ms.topic: include
ms.date: 08/22/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: c54aa861a47b11756f05e003e9b944df6c5b0e28
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67185073"
---
Привязки Azure Cosmos DB поддерживаются только для использования с API SQL. Для всех других API Azure Cosmos DB доступ к базе данных из функции должен осуществляться с использованием статического клиента для API, включая [API базы данны Azure Cosmos для MongoDB](../articles/cosmos-db/mongodb-introduction.md), [API Cassandra](../articles/cosmos-db/cassandra-introduction.md), [API Gremlin](../articles/cosmos-db/graph-introduction.md) и [API таблиц](../articles/cosmos-db/table-introduction.md).
