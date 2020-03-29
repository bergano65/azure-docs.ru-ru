---
title: Поиск по Azure Data Lake Storage Gen2 (предварительный просмотр)
titleSuffix: Azure Cognitive Search
description: Узнайте, как индексировать содержимое и метаданные в Azure Data Lake Storage Gen2. Эта функция в настоящее время находится в общедоступном предварительном просмотре
manager: nitinme
author: markheff
ms.author: maheff
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 4b725c8a1bf0649a640c02a9a1828ec9014d36d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76905664"
---
# <a name="indexing-documents-in-azure-data-lake-storage-gen2"></a>Индексирование документов в Azure Data Lake Storage Gen2

> [!IMPORTANT] 
> Поддержка Azure Data Lake Storage Gen2 в настоящее время находится в открытом доступе. Для предварительной версии функции соглашение об уровне обслуживания не предусмотрено. Мы не рекомендуем использовать ее в рабочей среде. Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Вы можете запросить доступ к превью, заполнив [эту форму.](https://aka.ms/azure-cognitive-search/indexer-preview) Эта функция предоставляется в [версии REST API 2019-05-06-Preview](search-api-preview.md). В настоящее время нет портала или поддержки .NET SDK.


При настройке учетной записи хранения Azure у вас есть возможность включить [иерархическое пространство имен.](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-namespace) Это позволяет организовать сбор содержимого в учетной записи в иерархию каталогов и вложенных субдиректоров. Включив иерархическое пространство имен, вы включите [данные Azure Data Lake Storage Gen2.](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction)

В этой статье описывается, как начать работу с индексирования документов, которые находятся в Azure Data Lake Storage Gen2.

## <a name="set-up-azure-data-lake-storage-gen2-indexer"></a>Настройка индекса хранения данных Azure Data Lake Gen2

Для индексации содержимого Data Lake Storage Gen2 необходимо провести несколько этапов.

### <a name="step-1-sign-up-for-the-preview"></a>Шаг 1: Подпишитесь на предварительный просмотр

Подпишитесь на предварительный просмотр индекса data Lake Storage Gen2, заполнив [эту форму.](https://aka.ms/azure-cognitive-search/indexer-preview) Вы получите подтверждение электронной почты, как только вы были приняты в предварительный просмотр.

### <a name="step-2-follow-the-azure-blob-storage-indexing-setup-steps"></a>Шаг 2: Следуйте шагам по настройке индексирования хранилища Azure Blob

После того как вы получили подтверждение того, что регистрация предварительного просмотра прошла успешно, вы готовы создать конвейер индексирования.

Вы можете индексировать содержимое и метаданные из Data Lake Storage Gen2 с помощью [версии REST API 2019-05-06-Preview](search-api-preview.md). В настоящее время нет портала или поддержки .NET SDK.

Содержание индексирования в Data Lake Storage 2 идентично содержанию индексирования в хранилище Azure Blob. Чтобы понять, как настроить источник данных Data Lake Storage Gen2, индекс и индексер, обратитесь к [Как индексировать документы в Azure Blob Storage с помощью Azure Cognitive Search.](search-howto-indexing-azure-blob-storage.md) Статья о хранении Blob также содержит информацию о том, какие форматы документов поддерживаются, какие свойства метаданных blob извлекаются, инкрементная индексация и многое другое. Эта информация будет такой же для data Lake Storage Gen2.

## <a name="access-control"></a>Управление доступом

Azure Data Lake Storage Gen2 реализует [модель управления доступом,](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control) которая поддерживает как контроль доступа на основе ролей Azure (RBAC), так и POSIX-подобные списки управления доступом (ACL). При индексации содержимого Data Lake Storage Gen2 Azure Cognitive Search не будет извлекать информацию RBAC и ACL из содержимого. В результате эта информация не будет включена в ваш индекс когнитивного поиска Azure.

Если важно поддерживать контроль доступа к каждому документу в индексе, разработчик приложения должен реализовать [обрезку безопасности.](https://docs.microsoft.com/azure/search/search-security-trimming-for-azure-search)

## <a name="change-detection"></a>Обнаружение изменений

Индексер хранения данных озера Gen2 поддерживает обнаружение изменений. Это означает, что при запуске индекса он только переиндексирует измененные `LastModified` капли, определяемые меткой времени капли.

> [!NOTE] 
> Data Lake Storage Gen2 позволяет переименовать каталоги. Когда каталог переименован, метки времени для капли в этом каталоге не обновляются. В результате, индексер не будет переиндексировать эти капли. Если вам нужно капли в каталоге, чтобы быть переиндексированы после переименования каталога, потому `LastModified` что теперь они имеют новые URL-адреса, вам нужно будет обновить метки времени для всех капли в каталоге, так что индексатор знает, чтобы переиндексировать их во время будущего запуска.
