---
title: Как реализовать настраиваемую синхронизацию, чтобы достичь высокого уровня доступности и производительности в Azure Cosmos DB
description: В этой статье объясняется, как реализовать настраиваемую синхронизацию, чтобы достичь высокого уровня доступности и производительности в Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 2/12/2019
ms.author: mjbrown
ms.openlocfilehash: 43cb73784806358bccb9758be2923d3df5e9badd
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/19/2019
ms.locfileid: "56414887"
---
# <a name="how-to-implement-custom-synchronization-to-optimize-for-higher-availability-and-performance"></a>Как реализовать настраиваемую синхронизацию, чтобы достичь высокого уровня доступности и производительности

В Azure Cosmos DB предлагается пять четко определенных уровней согласованности, благодаря которым можно достичь компромисса между согласованностью, производительностью и доступностью. Строгая согласованность обеспечивает синхронную репликацию данных и сохранение устойчивости в каждом регионе, в котором доступна учетная запись Azure Cosmos. Тем не менее эта конфигурация обеспечивает максимальный уровень устойчивости за счет производительности и доступности. Если приложению необходимо контролировать (снизить) устойчивость данных в зависимости от своих потребностей без ущерба для доступности, можно использовать настраиваемую синхронизацию на уровне приложения, чтобы достичь желаемого уровня устойчивости.

На схеме ниже наглядно показана модель настраиваемой синхронизации.

![Настраиваемая синхронизация](./media/how-to-custom-synchronization/custom-synchronization.png)

В этом сценарии контейнер Azure Cosmos глобально реплицируется в нескольких регионах на нескольких континентах. Использование строгой согласованности для всех регионов в этом сценарии повлияет на производительность. Чтобы обеспечить более высокий уровень устойчивости без повышения задержки записи, в приложении можно использовать два клиента с одним токеном сеанса.

Первый клиент может записывать данные в локальном регионе (например, в западной части США). Второй клиент (например, в восточной части США) выполняет чтение и используется для синхронизации. Получая токен сеанса из ответа операции записи, последующая операция чтения обеспечивает синхронизацию операций записи в восточной части США. Azure Cosmos DB гарантирует, что операции записи будут выполняться по крайней мере в одном регионе и выдержат региональный сбой, если исходный регион записи будет отключен. В этом сценарии каждая запись синхронизируется с восточной частью США, что снижает задержку использования строгой согласованности во всех регионах. В сценарии с несколькими источниками, когда операции записи выполняются в каждом регионе, эту модель можно расширить для параллельной синхронизации с несколькими регионами.

## <a name="configure-the-clients"></a>Настройка клиентов

В этом примере показан уровень доступа к данным и создание экземпляров двух клиентов.

```csharp
class MyDataAccessLayer
{
    private DocumentClient writeClient;
    private DocumentClient readClient;

    public async Task InitializeAsync(Uri accountEndpoint, string key)
    {
        ConnectionPolicy writeConnectionPolicy = new ConnectionPolicy
        {
            ConnectionMode = ConnectionMode.Direct,
            ConnectionProtocol = Protocol.Tcp,
            UseMultipleWriteLocations = true
        };
        writeConnectionPolicy.SetCurrentLocation(LocationNames.WestUS);

        ConnectionPolicy readConnectionPolicy = new ConnectionPolicy
        {
            ConnectionMode = ConnectionMode.Direct,
            ConnectionProtocol = Protocol.Tcp
        };
        readConnectionPolicy.SetCurrentLocation(LocationNames.EastUS);

        writeClient = new DocumentClient(accountEndpoint, key, writeConnectionPolicy);
        readClient = new DocumentClient(accountEndpoint, key, readConnectionPolicy, ConsistencyLevel.Session);

        await Task.WhenAll(new Task[]
        {
            writeClient.OpenAsync(),
            readClient.OpenAsync()
        });
    }
}
```

## <a name="implement-custom-synchronization"></a>Реализация пользовательской синхронизации

После инициализации клиентов приложение может выполнять операции записи в локальном регионе (западная часть США) и принудительно синхронизировать операции записи в восточной части США, как показано ниже.

```csharp
class MyDataAccessLayer
{
    public async Task CreateItem(string containerLink, Document document)
    {
        ResourceResponse<Document> response = await writeClient.CreateDocumentAsync(
            containerLink, document);

        await readClient.ReadDocumentAsync(response.Resource.SelfLink,
            new RequestOptions { SessionToken = response.SessionToken });
    }
}
```

Эту модель можно расширить для параллельной синхронизации с несколькими регионами.

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о глобальном распределении и согласованности в Azure Cosmos DB см. в следующих статьях:

* [Выбор правильного уровня согласованности](consistency-levels-choosing.md)

* [Компромиссы согласованности, доступности и производительности](consistency-levels-tradeoffs.md)

* [Управление уровнями согласованности в Azure Cosmos DB](how-to-manage-consistency.md)

* [Секционирование и горизонтальное масштабирование в Azure Cosmos DB](partition-data.md)
