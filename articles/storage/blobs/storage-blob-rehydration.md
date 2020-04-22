---
title: Rehydrate капли данные из архива уровня
description: Регидратировать капли из архивного хранения, чтобы вы могли получить доступ к данным.
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 04/08/2020
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: hux
ms.openlocfilehash: 82ea4ad23e3207f5641ade196f69595cd1e7b323
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81684108"
---
# <a name="rehydrate-blob-data-from-the-archive-tier"></a>Rehydrate капли данные из архива уровня

В то время как капля находится в уровне доступа архива, она считается автономной и не может быть прочитана или изменена. Метаданные капли остаются в сети и доступны, что позволяет перечислить капли и ее свойства. Чтение и изменение данных blob доступно только с онлайн-уровнями, такими как горячий или прохладный. Существует два варианта получения и доступа к данным, хранящимся на уровне доступа к архиву.

1. [Регидратировать архивные капли в онлайн-уровня](#rehydrate-an-archived-blob-to-an-online-tier) - Регидрат архив капли горячей или прохладной, изменив свой уровень с помощью [set Blob Tier](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier) операции.
2. [Копирование архивной капли на онлайн-уровне](#copy-an-archived-blob-to-an-online-tier) - Создайте новую копию архивной капли с помощью операции [Copy Blob.](https://docs.microsoft.com/rest/api/storageservices/copy-blob) Укажите другое название капли и уровень назначения горячей или прохладной.

 Для получения дополнительной информации [Azure Blob storage: hot, cool, and archive access tiers](storage-blob-storage-tiers.md)о ярусах см.

## <a name="rehydrate-an-archived-blob-to-an-online-tier"></a>Регидратировать архивную каплю в онлайн-уровне

[!INCLUDE [storage-blob-rehydration](../../../includes/storage-blob-rehydrate-include.md)]

## <a name="copy-an-archived-blob-to-an-online-tier"></a>Copy an archived blob to an online tier (Копирование архивного большого двоичного объекта на подключенный уровень)

Если вы не хотите, чтобы регидратировать ваш архив капли, вы можете сделать [операцию Copy Blob.](https://docs.microsoft.com/rest/api/storageservices/copy-blob) Ваш оригинальный капли останется неизменным в архиве в то время как новый капля создается в онлайн горячей или прохладной уровне для вас работать. В операции Copy Blob вы также можете установить дополнительное свойство *x-ms-rehydrate-priority* в Standard или High, чтобы указать приоритет, при котором вы хотите создать копию капли.

Копирование капли из архива может занять несколько часов в зависимости от выбранного приоритета регидратации. За кулисами, **Операция Copy Blob** читает ваш архив источник капли для создания нового онлайн капли в выбранном уровне назначения. Новая капля может быть видна, когда вы перечислите капли, но данные не доступны до тех пор, пока чтение из архива архива завершена, и данные будут записаны на новый онлайн-кабл назначения. Новая капля является независимой копией, и любая модификация или удаление не влияет на каплю исходного архива.

Архивные капли можно скопировать только на уровни назначения в сети в пределах одной учетной записи хранилища. Копирование архивной капли в другой архив капли не поддерживается. В следующей таблице указаны возможности CopyBlob.

|                                           | **Горячий источник яруса**   | **Прохладный источник уровня** | **Источник архивного уровня**    |
| ----------------------------------------- | --------------------- | -------------------- | ------------------- |
| **Горячий уровень назначения**                  | Поддерживается             | Поддерживается            | Поддерживается в пределах одной учетной записи; в ожидании регидратации               |
| **Прохладный уровень назначения**                 | Поддерживается             | Поддерживается            | Поддерживается в пределах одной учетной записи; в ожидании регидратации               |
| **Назначение архивного уровня**              | Поддерживается             | Поддерживается            | Не поддерживается         |

## <a name="pricing-and-billing"></a>Цены и выставление счетов

Регидратирующие капли из архива в горячие или холодные уровни заряжаются как считываемые операции, так и поиск данных. Использование High priority имеет более высокие эксплуатационные и эксплуатационные расходы по сравнению со стандартным приоритетом. Регидратация с высоким приоритетом отображается в качестве отдельного элемента строки в вашем счете. Если запрос с высоким приоритетом на возврат кабы из нескольких гигабайт занимает более 5 часов, с вас не будет взиматься высокая скорость поиска приоритетов. Однако стандартные коэффициенты поиска по-прежнему применяются, поскольку регидратация была приоритетной по сравнению с другими запросами.

Копирование капли из архива в горячие или холодные уровни взимается как чтение операций и поиска данных. За создание новой копии капли взимается операция записи. Плата за раннее удаление не взимается при копировании в онлайн-каплей, поскольку исходная капля остается неизмененной на уровне архива. При выборе взимается плата за извлечение высокого приоритета.

Капли в архивном ярусе должны храниться не менее 180 дней. Удаление или регидратация архивных капли до 180 дней понесет досрочное удаление сборов.

> [!NOTE]
> Для получения дополнительной информации о ценах на блок капли и регидратации данных, [см.](https://azure.microsoft.com/pricing/details/storage/blobs/) Для получения дополнительной [информации](https://azure.microsoft.com/pricing/details/data-transfers/)о расходах на передачу данных, см.

## <a name="quickstart-scenarios"></a>Сценарии быстрого запуска

### <a name="rehydrate-an-archive-blob-to-an-online-tier"></a>Регидратировать архив капли в онлайн-уровня
# <a name="portal"></a>[Портал](#tab/azure-portal)
1. Войдите на [портал Azure](https://portal.azure.com).

1. На портале Azure ищите **все ресурсы.**

1. Затем выберите учетную запись хранения.

1. Выберите контейнер, а затем выберите каплю.

1. В **свойствах Blob**выберите **уровень изменения.**

1. Выберите уровень доступа **Hot** или **Cool.** 

1. Выберите приоритет регидратации **стандарта** или **высокого**.

1. Выберите **Сохранить** в нижней части.

![Изменение уровня](media/storage-tiers/blob-access-tier.png)
![учетной записи хранения Проверить состояние регидратации](media/storage-tiers/rehydrate-status.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Следующий скрипт PowerShell может быть использован для изменения уровня капли архива. Переменная `$rgName` должна быть инициализирована с именем группы ресурсов. Переменная `$accountName` должна быть инициализирована с именем учетной записи хранилища. Переменная `$containerName` должна быть инициализирована с именем контейнера. Переменная `$blobName` должна быть инициализирована с вашим именем капли. 
```powershell
#Initialize the following with your resource group, storage account, container, and blob names
$rgName = ""
$accountName = ""
$containerName = ""
$blobName = ""

#Select the storage account and get the context
$storageAccount =Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

#Select the blob from a container
$blobs = Get-AzStorageBlob -Container $containerName -Blob $blobName -Context $context

#Change the blob’s access tier to Hot using Standard priority rehydrate
$blob.ICloudBlob.SetStandardBlobTier("Hot", “Standard”)
```
---

### <a name="copy-an-archive-blob-to-a-new-blob-with-an-online-tier"></a>Копировать архив капли на новый капля с онлайн-уровня
Следующий скрипт PowerShell может быть использован для копирования архивной капли на новую каплю в той же учетной записи хранения. Переменная `$rgName` должна быть инициализирована с именем группы ресурсов. Переменная `$accountName` должна быть инициализирована с именем учетной записи хранилища. `$srcContainerName` Переменные `$destContainerName` и переменные должны быть инициализированы с именами контейнеров. И `$srcBlobName` `$destBlobName` переменные должны быть инициализированы с вашими именами капли. 
```powershell
#Initialize the following with your resource group, storage account, container, and blob names
$rgName = ""
$accountName = ""
$srcContainerName = ""
$destContainerName = ""
$srcBlobName = ""
$destBlobName = ""

#Select the storage account and get the context
$storageAccount =Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

#Copy source blob to a new destination blob with access tier hot using standard rehydrate priority
Start-AzStorageBlobCopy -SrcContainer $srcContainerName -SrcBlob $srcBlobName -DestContainer $destContainerName -DestBlob $destBlobName -StandardBlobTier Hot -RehydratePriority Standard -Context $ctx
```

## <a name="next-steps"></a>Next Steps

* [Узнайте больше о уровнях хранения данных Blob](storage-blob-storage-tiers.md)
* [Цены на горячий, холодный и архивный уровни в учетных записях хранилища BLOB-объектов и учетных записях GPv2 по регионам](https://azure.microsoft.com/pricing/details/storage/)
* [Управление жизненным циклом хранилища BLOB-объектов Azure](storage-lifecycle-management-concepts.md)
* [Проверка сведений о ценах на передачу данных](https://azure.microsoft.com/pricing/details/data-transfers/)
