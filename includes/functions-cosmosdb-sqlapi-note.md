---
title: включить файл
description: включить файл
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 08/22/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: f7495c52e36bdc207145f17ec72eb57b7ebf1784
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/19/2020
ms.locfileid: "76279599"
---
Привязки Azure Cosmos DB поддерживаются только для использования с API SQL. Для всех других API Azure Cosmos DB доступ к базе данных из функции должен осуществляться с использованием статического клиента для API, включая [API базы данны Azure Cosmos для MongoDB](../articles/cosmos-db/mongodb-introduction.md), [API Cassandra](../articles/cosmos-db/cassandra-introduction.md), [API Gremlin](../articles/cosmos-db/graph-introduction.md) и [API таблиц](../articles/cosmos-db/table-introduction.md).
