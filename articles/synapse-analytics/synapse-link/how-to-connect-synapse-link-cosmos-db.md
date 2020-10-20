---
title: Подключение к Azure Synapse Link (предварительная версия) для Azure Cosmos DB
description: Подключение Azure Cosmos DB к рабочей области Synapse с помощью Azure Synapse Link
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: synapse-link
ms.date: 04/21/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: 50717c7e8a7b0f748df98c1896e1b7eb64cd7be3
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/07/2020
ms.locfileid: "91819350"
---
# <a name="connect-to-azure-synapse-link-preview-for-azure-cosmos-db"></a>Подключение к Azure Synapse Link (предварительная версия) для Azure Cosmos DB

В этой статье описывается, как получить доступ к базе данных Azure Cosmos DB из Azure Synapse Analytics Studio с помощью Azure Synapse Link.

## <a name="prerequisites"></a>Предварительные требования

Перед подключением базы данных Azure Cosmos DB к рабочей области вам потребуется следующее:

> [!IMPORTANT]
> Сейчас Azure Synapse Link для Azure Cosmos DB поддерживается для рабочих областей, в которых не включена управляемая виртуальная сеть. 

* имеющаяся база данных Azure Cosmos DB (можно также создать новую учетную запись, следуя инструкциям в этом [кратком руководстве](https://docs.microsoft.com/azure/cosmos-db/how-to-manage-database-account));
* имеющаяся рабочая область Synapse (можно также создать новую рабочую область, следуя инструкциям в этом [кратком руководстве](https://docs.microsoft.com/azure/synapse-analytics/quickstart-create-workspace)). 

## <a name="enable-azure-cosmos-db-analytical-store"></a>Включение аналитического хранилища Azure Cosmos DB

Для запуска крупномасштабной аналитики в Azure Cosmos DB без негативных последствий для производительности в рабочей среде рекомендуем включить Synapse Link для Azure Cosmos DB. Synapse Link предоставляет возможность HTAP для контейнера и встроенную поддержку в Azure Synapse.

## <a name="navigate-to-synapse-studio"></a>Перейдите в Synapse Studio

В рабочей области Synapse выберите **Запуск Synapse Studio**. На домашней странице Synapse Studio выберите **Данные**, чтобы перейти в **обозреватель объектов данных**.

## <a name="connect-an-azure-cosmos-db-database-to-a-synapse-workspace"></a>Подключение базы данных Azure Cosmos DB к рабочей области Synapse

База данных Azure Cosmos DB подключается в качестве связанной службы. Связанная служба Azure Cosmos DB позволяет пользователям просматривать и изучать данные, а также считывать и записывать их из Apache Spark для Azure Synapse Analytics или SQL в Azure Cosmos DB.

В обозревателе объектов данных можно напрямую подключиться к базе данных Azure Cosmos DB, выполнив следующие действия.

1. Щелкните значок ***+*** рядом с данными.
2. Выберите **Connect to external data** (Подключение к внешнем данным).
3. Укажите API для подключения: API SQL или API для MongoDB.
4. Выберите ***Продолжить***
5. Присвойте имя связанной службе. Имя будет отображаться в обозревателе объектов и использоваться средой выполнения Synapse для подключения к базе данных и контейнерам. Рекомендуем использовать понятное имя.
6. Выберите **имя учетной записи Azure Cosmos DB** и **имя базы данных**.
7. (Необязательно.) Если регион не указан, операции среды выполнения Synapse будут направляться в ближайший регион, где включено аналитическое хранилище. Однако можно вручную задать регион, который пользователи будут использовать для доступа к аналитическому хранилищу Azure Cosmos DB. Выберите **Additional connection properties** (Дополнительные свойства подключения) — а затем **Создать**. В разделе **Имя свойства** введите ***PreferredRegions*** и задайте в поле **Значение** нужный регион (например, WestUS2 — без пробелов).
8. Нажмите кнопку ***Создать***

Базы данных Azure Cosmos DB отображаются на вкладке **Подключено** в разделе Azure Cosmos DB. Azure Cosmos DB позволяет отличить контейнер с поддержкой HTAP от контейнера, поддерживающего только OLTP, по следующим значкам:

**Контейнер, поддерживающий только OLTP**:

![Контейнер OLTP](../media/quickstart-connect-synapse-link-cosmosdb/oltp-container.png)

**Контейнер с поддержкой HTAP**:

![Контейнер HTAP](../media/quickstart-connect-synapse-link-cosmosdb/htap-container.png)

## <a name="quickly-interact-with-code-generated-actions"></a>Быстрое взаимодействие с созданными кодом действиями

Щелкнув правой кнопкой мыши контейнер, вы получите список жестов, которые активируют среду выполнения Spark или SQL. Запись в контейнер происходит через хранилище транзакций Azure Cosmos DB и приводит к использованию единиц запросов.  

## <a name="next-steps"></a>Дальнейшие действия

* [Узнайте, какие общие возможности поддерживают Synapse и Azure Cosmos DB](./concept-synapse-link-cosmos-db-support.md).
* [Узнайте, как отправлять запросы в аналитическое хранилище с помощью Spark](./how-to-query-analytical-store-spark.md).
