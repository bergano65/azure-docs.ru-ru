---
title: Как реализовать настраиваемую синхронизацию, чтобы достичь высокого уровня доступности и производительности в Azure Cosmos DB
description: В этой статье объясняется, как реализовать настраиваемую синхронизацию, чтобы достичь высокого уровня доступности и производительности в Azure Cosmos DB.
author: rimman
ms.service: cosmos-db
ms.topic: sample
ms.date: 05/23/2019
ms.author: rimman
ms.openlocfilehash: cd89a145f5746696cc8fc163eb46896081d85a90
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/27/2019
ms.locfileid: "66240962"
---
# <a name="implement-custom-synchronization-to-optimize-for-higher-availability-and-performance"></a>Реализация настраиваемой синхронизации для достижения высокого уровня доступности и производительности

В Azure Cosmos DB предлагается [пять четко определенных уровней согласованности](consistency-levels.md), благодаря которым можно достичь компромисса между согласованностью, производительностью и доступностью. Строгая согласованность помогает обеспечить синхронную репликацию данных и сохранить устойчивость в каждом регионе, в котором доступна учетная запись Azure Cosmos. Эта конфигурация обеспечивает максимальный уровень устойчивости за счет производительности и доступности. Если приложению необходимо контролировать или снизить устойчивость данных в зависимости от своих потребностей без ущерба для доступности, можно использовать *настраиваемую синхронизацию* на уровне приложения, чтобы достичь желаемого уровня устойчивости.

На изображении ниже наглядно показана модель настраиваемой синхронизации.

![Настраиваемая синхронизация](./media/how-to-custom-synchronization/custom-synchronization.png)

В этом сценарии контейнер Azure Cosmos глобально реплицируется в нескольких регионах на нескольких континентах. Использование строгой согласованности для всех регионов в этом сценарии влияет на производительность. Чтобы обеспечить более высокий уровень устойчивости без повышения задержки записи, в приложении можно использовать два клиента с одним [маркером сеанса](how-to-manage-consistency.md#utilize-session-tokens).

Первый клиент может записывать данные в локальном регионе (например, в западной части США). Второй клиент (например, в восточной части США) выполняет чтение и используется для синхронизации. Получая маркер сеанса из ответа операции записи, последующая операция чтения обеспечивает синхронизацию операций записи в восточной части США. Azure Cosmos DB гарантирует, что операции записи будут выполнятся по крайней мере в одном регионе. Они гарантированно выдержат региональный сбой, если исходный регион записи будет отключен. В этом сценарии каждая запись синхронизируется с восточной частью США, что снижает задержку использования строгой согласованности во всех регионах. В сценарии с несколькими источниками, когда операции записи выполняются в каждом регионе, эту модель можно расширить для параллельной синхронизации с несколькими регионами.

## <a name="configure-the-clients"></a>Настройка клиентов

В следующем примере показан уровень доступа к данным, который создает два экземпляра клиента для настраиваемой синхронизации:

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

Вы можете расширить модель для параллельной синхронизации в нескольких регионах.

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о глобальном распределении и согласованности в Azure Cosmos DB см. в следующих статьях:

* [Choose the right consistency level](consistency-levels-choosing.md) (Выбор правильного уровня согласованности)
* [Компромиссы согласованности, доступности и производительности](consistency-levels-tradeoffs.md)
* [Управление уровнями согласованности в Azure Cosmos DB](how-to-manage-consistency.md)
* [Секционирование и горизонтальное масштабирование в Azure Cosmos DB](partition-data.md)
