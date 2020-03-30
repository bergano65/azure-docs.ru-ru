---
title: Создание и управление моментальным снимком каплей в .NET - Хранилище Azure
description: Узнайте, как создать снимок капли только для чтения для резервного копирования данных капли в данный момент времени.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 09/06/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 17cd57fbcf9b1c14fb275a070bdefdd1282c4d6e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79370531"
---
# <a name="create-and-manage-a-blob-snapshot-in-net"></a>Создание и управление снимком капли в .NET

Моментальный снимок — это версия BLOB-объекта только для чтения, сделанная в определенный момент времени. Моментальные снимки полезны для архивации BLOB-объектов. В этой статье показано, как создавать и управлять снимками blob с помощью [клиентской библиотеки Azure Storage для .NET.](/dotnet/api/overview/azure/storage?view=azure-dotnet)

## <a name="about-blob-snapshots"></a>О снимках капли

[!INCLUDE [updated-for-az](../../../includes/storage-data-lake-gen2-support.md)]

Моментальный снимок большого двоичного объекта идентичен объекту, на основе которого он создан. Единственное исключение: к URI большого двоичного объекта добавляется значение **DateTime**, которое указывает время создания снимка. Например, если страничный BLOB-объект имеет URI `http://storagesample.core.blob.windows.net/mydrives/myvhd`, то URI снимка будет иметь такой вид: `http://storagesample.core.blob.windows.net/mydrives/myvhd?snapshot=2011-03-09T01:42:34.9360000Z`.

> [!NOTE]
> Все моментальные снимки имеют одинаковый URI базового большого двоичного объекта. Большой двоичный объект и его моментальный снимок отличаются только добавлением значения **DateTime** .
>

Большой двоичный объект может иметь любое количество моментальных снимков. Снимки сохраняются до тех пор, пока они явно не будут удалены, что означает, что снимок не может пережить его базовую каплю. Чтобы было удобнее контролировать моментальные снимки большого двоичного объекта, их можно пронумеровать.

При создании моментального снимка BLOB-объекта его системные свойства и их значения копируются в снимок. Метаданные базового большого двоичного объекта также копируются в моментальный снимок, если при его создании не были указаны отдельные метаданные для снимка. После создания моментального снимка его можно прочитать, скопировать или удалить, но вы не можете изменять его.

Свойства аренды, связанные с базовым BLOB-объектом, не влияют на моментальный снимок. Вы не можете получить аренду в моментальном снимке.

VHD-файл используется для хранения текущей информации о диске виртуальной машины и его состояния. Можно отключить диск в виртуальной машине или завершить ее работу, после чего создать моментальный снимок его VHD-файла. Этот файл моментального снимка можно будет использовать позже, чтобы получить VHD-файл на этот момент времени и повторно создать виртуальную машину.

## <a name="create-a-snapshot"></a>Создание моментального снимка

Чтобы создать снимок блока капли, используйте один из следующих методов:

- [СозданиеСнимой снимок](/dotnet/api/microsoft.azure.storage.blob.cloudblockblob.createsnapshot)
- [СозданиеСнимойАсин](/dotnet/api/microsoft.azure.storage.blob.cloudblockblob.createsnapshotasync)

Следующий пример кода показывает, как создать снимок. В этом примере мы указываем дополнительные метаданные для моментального снимка при его создании.

```csharp
private static async Task CreateBlockBlobSnapshot(CloudBlobContainer container)
{
    // Create a new block blob in the container.
    CloudBlockBlob baseBlob = container.GetBlockBlobReference("sample-base-blob.txt");

    // Add blob metadata.
    baseBlob.Metadata.Add("ApproxBlobCreatedDate", DateTime.UtcNow.ToString());

    try
    {
        // Upload the blob to create it, with its metadata.
        await baseBlob.UploadTextAsync(string.Format("Base blob: {0}", baseBlob.Uri.ToString()));

        // Sleep 5 seconds.
        System.Threading.Thread.Sleep(5000);

        // Create a snapshot of the base blob.
        // You can specify metadata at the time that the snapshot is created.
        // If no metadata is specified, then the blob's metadata is copied to the snapshot.
        Dictionary<string, string> metadata = new Dictionary<string, string>();
        metadata.Add("ApproxSnapshotCreatedDate", DateTime.UtcNow.ToString());
        await baseBlob.CreateSnapshotAsync(metadata, null, null, null);
    }
    catch (StorageException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

## <a name="delete-snapshots"></a>Удаление моментальных снимков

Чтобы удалить каплю, вы должны сначала удалить любые снимки этой капли. Можно удалить моментальный снимок отдельно или указать, чтобы при удалении исходного большого двоичного объекта были удалены и все его моментальные снимки. Попытка удалить большой двоичный объект, у которого еще есть моментальные снимки, завершится ошибкой.

Чтобы удалить снимки blob, используйте один из следующих методов удаления капли, и включите в enum [DeleteSnapshotsOption.](/dotnet/api/microsoft.azure.storage.blob.deletesnapshotsoption)

- [Удалить](/dotnet/api/microsoft.azure.storage.blob.cloudblob.delete)
- [DeleteAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.deleteasync)
- [DeleteIfExists](/dotnet/api/microsoft.azure.storage.blob.cloudblob.deleteifexists)
- [DeleteIfExistsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.deleteifexistsasync)

В следующем коде показан пример удаления большого двоичного объекта и его моментальных снимков в .NET, где `blockBlob` является объектом типа [CloudBlockBlob][dotnet_CloudBlockBlob]:

```csharp
await blockBlob.DeleteIfExistsAsync(DeleteSnapshotsOption.IncludeSnapshots, null, null, null);
```

## <a name="return-the-absolute-uri-to-a-snapshot"></a>Возврат абсолютного URI для моментального снимка

Следующий пример кода создает моментальный снимок и выписывает абсолютное URI для основного местоположения.

```csharp
//Create the blob service client object.
const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key";

CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

//Get a reference to a container.
CloudBlobContainer container = blobClient.GetContainerReference("sample-container");
container.CreateIfNotExists();

//Get a reference to a blob.
CloudBlockBlob blob = container.GetBlockBlobReference("sampleblob.txt");
blob.UploadText("This is a blob.");

//Create a snapshot of the blob and write out its primary URI.
CloudBlockBlob blobSnapshot = blob.CreateSnapshot();
Console.WriteLine(blobSnapshot.SnapshotQualifiedStorageUri.PrimaryUri);
```

## <a name="understand-how-snapshots-accrue-charges"></a>Общая информация о том, как моментальные снимки увеличивают плату

Создание моментальных снимков, представляющих собой копии больших двоичных объектов с доступом только для чтения, может привести к дополнительной плате за хранение данных в вашей учетной записи. При проектировании приложения важно учитывать, как эта плата может накапливаться, чтобы минимизировать затраты.

### <a name="important-billing-considerations"></a>Важные вопросы о выставлении счетов

Следующий список включает ключевые пункты, которые следует рассмотреть при создании моментального снимка.

- Плата взимается за уникальные блоки и страницы независимо от того, где они находятся: в большом двоичном объекте или моментальном снимке. Ваша учетная запись не влечет за собой дополнительных затрат на моментальные снимки, связанные с большим двоичным объектом, пока большой двоичный объект, на котором они основаны, не будет обновлен. После изменения базового большого двоичного объекта он начнет отличаться от своих моментальных снимков. Теперь вы будете оплачивать все уникальные блоки и страницы в каждом большом двоичном объекте или моментальном снимке.
- Когда вы заменяете блок в блочном большом двоичном объекте, за этот блок начинает взиматься плата как за уникальный. Это произойдет, даже если у блока тот же идентификатор блока и те же данные, что и в моментальном снимке. Как только блок фиксируется заново, он расходится со всеми своими дубликатами во всех моментальных снимках и вы будете оплачивать его данные. Это же происходит и для страницы в страничном BLOB-объекте, обновляемом идентичными данными.
- При замещении блочного BLOB-объекта путем вызова метода [UploadFromFile][dotnet_UploadFromFile], [UploadText][dotnet_UploadText], [UploadFromStream][dotnet_UploadFromStream] или [UploadFromByteArray][dotnet_UploadFromByteArray] заменяются все блоки в этом большом двоичном объекте. Если с этим большим двоичным объектом связан моментальный снимок, то все блоки в базовом большом двоичном объекте перестанут совпадать с блоками в моментальном снимке, и вы будете оплачивать хранение всех блоков в обоих больших двоичных объектах. Это произойдет, даже если данные базового большого двоичного объекта и моментального снимка останутся идентичными.
- В службе BLOB-объектов Azure нет средств, позволяющих определить, содержат ли два блока идентичные данные. Каждый блок, который был загружен и зафиксирован, обрабатывается как уникальный, даже если он содержит те же данные и имеет тот же идентификатор блока. Так как плата добавляется за уникальные блоки, важно иметь в виду, что обновление большого двоичного объекта, содержащего моментальный снимок, приведет к появлению дополнительных уникальных блоков и дополнительным расходам.

### <a name="minimize-cost-with-snapshot-management"></a>Минимизация затрат с помощью управления моментальными снимками

Рекомендуется управлять моментальными снимками осторожно, чтобы избежать наценок. Выполните приведенные ниже рекомендации, чтобы свести к минимуму затраты на хранение моментальных снимков.

- Удаляйте и повторно создавайте моментальные снимки, связанные с большим двоичным объектом, при обновлении этого объекта, даже если вы обновляете его идентичными данными, если только структура вашего приложения не требует сохранения моментальных снимков. За счет удаления и повторного создания моментальных снимков больших двоичных объектов можно исключить расхождение большого двоичного объекта с моментальными снимками.
- Если необходимо хранить моментальные снимки для больших двоичных объектов, то не вызывайте для их обновления методы [UploadFromFile][dotnet_UploadFromFile], [UploadText][dotnet_UploadText], [UploadFromStream][dotnet_UploadFromStream] и [UploadFromByteArray][dotnet_UploadFromByteArray]. Эти методы заменяют все блоки в большом двоичном объекте, и после этого базовый большой двоичный объект начинает значительно отличаться от своих моментальных снимков. Вместо этого обновляйте наименьшее возможное количество блоков с помощью методов [PutBlock][dotnet_PutBlock] и [PutBlockList][dotnet_PutBlockList].

### <a name="snapshot-billing-scenarios"></a>Сценарии выставления счетов за моментальные снимки

В следующих сценариях показано, как добавляется плата за большой двоичный объект и его моментальные снимки.

#### <a name="scenario-1"></a>Сценарий 1

В сценарии 1 базовый большой двоичный объект создания моментального снимка не обновлялся, поэтому плата начисляется только за уникальные блоки 1, 2 и 3.

![Ресурсы хранилища Azure](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-1.png)

#### <a name="scenario-2"></a>Сценарий 2

В сценарии 2 базовый большой двоичный объект изменился, а моментальный снимок — нет. Блок 3 был обновлен, и, хотя он содержит такие же данные и тот же идентификатор, он отличается от блока 3 в моментальном снимке. В результате плата в учетной записи взимается за четыре блока.

![Ресурсы хранилища Azure](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-2.png)

#### <a name="scenario-3"></a>Сценарий 3

В сценарии 3 базовый большой двоичный объект изменился, а моментальный снимок — нет. Блок 3 был заменен блоком 4 в базовом большом двоичном объекте, но моментальный снимок по-прежнему отражает блок 3. В результате плата в учетной записи взимается за четыре блока.

![Ресурсы хранилища Azure](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-3.png)

#### <a name="scenario-4"></a>Сценарий 4

В сценарии 4 базовый большой двоичный объект был полностью обновлен и не содержит никаких первоначальных блоков. В результате плата в учетной записи взимается за все восемь уникальных блоков. Этот сценарий может произойти при использовании таких методов обновления, как [UploadFromFile][dotnet_UploadFromFile], [UploadText][dotnet_UploadText], [UploadFromStream][dotnet_UploadFromStream] и [UploadFromByteArray][dotnet_UploadFromByteArray], так как эти методы заменяют все содержимое больших двоичных объектов.

![Ресурсы хранилища Azure](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-4.png)

## <a name="next-steps"></a>Дальнейшие действия

- Дополнительные сведения о работе с моментальными снимками диска виртуальной машины см. в статье [Архивация неуправляемых дисков виртуальной машины Azure с помощью добавочных моментальных снимков](../../virtual-machines/windows/incremental-snapshots.md).

- Дополнительные примеры кода, в которых используется хранилище BLOB-объектов, см. на странице с [примерами кода Azure](https://azure.microsoft.com/documentation/samples/?service=storage&term=blob). Вы можете скачать пример приложения и запустить его или просмотреть код на GitHub.

[dotnet_AccessCondition]: https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.accesscondition
[dotnet_CloudBlockBlob]: https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._cloud_block_blob
[dotnet_CreateSnapshotAsync]: https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.blob.cloudpageblob.createsnapshotasync
[dotnet_HTTPStatusCode]: https://docs.microsoft.com/java/api/com.microsoft.store.partnercenter.network.httpstatuscode
[dotnet_PutBlockList]: /dotnet/api/microsoft.azure.storage.blob.cloudblockblob.putblocklist
[dotnet_PutBlock]: /dotnet/api/microsoft.azure.storage.blob.cloudblockblob.putblock
[dotnet_UploadFromByteArray]: https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._cloud_blob.uploadfrombytearray
[dotnet_UploadFromFile]: https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._cloud_blob.uploadfromfile
[dotnet_UploadFromStream]: /dotnet/api/microsoft.azure.storage.blob.cloudappendblob.uploadfromstream
[dotnet_UploadText]: /dotnet/api/microsoft.azure.storage.blob.cloudappendblob.uploadtext
