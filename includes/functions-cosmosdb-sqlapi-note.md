---
title: включение файла
description: включение файла
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: include
ms.date: 08/22/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: c68b56463625a09f9c8341881284706ab8250f46
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/05/2019
ms.locfileid: "67608353"
---
Привязки Azure Cosmos DB поддерживаются только для использования с API SQL. Для всех других API Azure Cosmos DB доступ к базе данных из функции должен осуществляться с использованием статического клиента для API, включая [API базы данны Azure Cosmos для MongoDB](../articles/cosmos-db/mongodb-introduction.md), [API Cassandra](../articles/cosmos-db/cassandra-introduction.md), [API Gremlin](../articles/cosmos-db/graph-introduction.md) и [API таблиц](../articles/cosmos-db/table-introduction.md).
