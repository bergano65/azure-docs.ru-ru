---
title: Функции хранилища BLOB-объектов, доступные в Azure Data Lake Storage 2-го поколения | Документация Майкрософт
description: Узнайте, какие функции хранилища BLOB-объектов можно использовать с Azure Data Lake Storage 2-го поколения.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 07/31/2020
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 0e7cedaea89e2ed3d998df6ffe0ecaa06115e265
ms.sourcegitcommit: 29400316f0c221a43aff3962d591629f0757e780
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/02/2020
ms.locfileid: "87513797"
---
# <a name="blob-storage-features-available-in-azure-data-lake-storage-gen2"></a>Функции хранилища BLOB-объектов, доступные в Azure Data Lake Storage 2-го поколения

Такие функции хранилища BLOB-объектов, как [журнал ведения диагностики](../common/storage-analytics-logging.md), [уровни доступа](storage-blob-storage-tiers.md) и  [политики управления жизненным циклом хранилища BLOB-объектов](storage-lifecycle-management-concepts.md), теперь работают с учетными записями, у которых есть иерархическое пространство имен. Таким образом, можно включить иерархические пространства имен в учетных записях хранения BLOB-объектов без потери доступа к этим функциям.

В этой таблице перечислены функции хранилища BLOB-объектов, которые можно использовать с Azure Data Lake Storage 2-го поколения. Элементы в этих таблицах со временем будут меняться, так как поддержка постоянно расширяется. Дополнительные сведения о конкретных проблемах, связанных с состоянием предварительной версии функции, см. в статье [Известные проблемы](data-lake-storage-known-issues.md).

## <a name="supported-blob-storage-features"></a>Поддерживаемые функции хранилища BLOB-объектов

> [!NOTE]
> Уровень поддержки относится только к тому, как эта функция поддерживается в Data Lake Storage 2-го поколения. 
>
> [Учетные записи BlockBlobStorage с производительностью уровня "Премиум"](storage-blob-create-account-block-blob.md) для Data Lake Storage 2-го поколения в настоящее время находятся на этапе общедоступной предварительной версии. Уровни поддержки для этих типов учетных записей отображаются в столбце **BlockBlobStorage (Премиум)** .

|Функция хранилища BLOB-объектов |Общего назначения версии 2 |BlockBlobStorage (Премиум) |Связанные статьи |
|---------------|-------------------|---|
|"Горячий" уровень доступа|Общедоступная версия|Не поддерживается|[Хранилище BLOB-объектов Azure: горячий, холодный и архивный уровни доступа](storage-blob-storage-tiers.md)|
|"Холодный" уровень доступа|Общедоступная версия|Не поддерживается|[Хранилище BLOB-объектов Azure: горячий, холодный и архивный уровни доступа](storage-blob-storage-tiers.md)|
|События|Общедоступная версия|Preview (Предварительный просмотр)|[Reacting to Blob storage events (preview)](storage-blob-event-overview.md) (Реагирование на события хранилища BLOB-объектов)|
|Метрики (классические)|Общедоступная версия|Не поддерживается|[Метрики Аналитики Службы хранилища Azure (классические)](../common/storage-analytics-metrics.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Метрики в Azure Monitor|Общедоступная версия|Preview (Предварительный просмотр)|[Метрики службы хранилища Azure в Azure Monitor](../common/storage-metrics-in-azure-monitor.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Команды PowerShell для хранилища BLOB-объектов|Общедоступная версия|Preview (Предварительный просмотр)|[Краткое руководство. Отправка, скачивание и составление списка больших двоичных объектов с помощью PowerShell](storage-quickstart-blobs-powershell.md)|
|Команды Azure CLI для хранилища BLOB-объектов|Общедоступная версия|Preview (Предварительный просмотр)|[Краткое руководство. Создание, скачивание и составление списка больших двоичных объектов с помощью Azure CLI](storage-quickstart-blobs-cli.md)|
|API хранилища BLOB-объектов|Общедоступная версия|Preview (Предварительный просмотр)|[Краткое руководство. Использование библиотеки хранилища BLOB-объектов Azure версии 12 для .NET](storage-quickstart-blobs-dotnet.md)<br>[Краткое руководство. Управление большими двоичными объектами с помощью пакета SDK для Java версии 12](storage-quickstart-blobs-java.md)<br>[Краткое руководство. Управление большими двоичными объектами с помощью пакета SDK для Python версии 12](storage-quickstart-blobs-python.md)<br>[Краткое руководство. Управление большими двоичными объектами с помощью пакета SDK для JavaScript версии 12 в Node.js](storage-quickstart-blobs-nodejs.md)|
|Журналы диагностики|Общедоступная версия|Preview (Предварительный просмотр) <div role="complementary" aria-labelledby="diagnostic-logging"><sup>1</sup></div> |[Ведение журнала аналитики службы хранилища Azure](../common/storage-analytics-logging.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Архивный уровень доступа|Общедоступная версия|Не поддерживается|[Хранилище BLOB-объектов Azure: горячий, холодный и архивный уровни доступа](storage-blob-storage-tiers.md)|
|Политики управления жизненным циклом|Общедоступная версия|Еще не поддерживается|[Управление жизненным циклом хранилища BLOB-объектов Azure (предварительная версия)](storage-lifecycle-management-concepts.md)|
|Вход в Azure Monitor|Preview (Предварительный просмотр) |Еще не поддерживается|[Мониторинг службы хранилища Azure](../common/monitor-storage.md)|
|Моментальные снимки|Предварительный просмотр<div role="complementary" aria-labelledby="preview-form"><sup>3</sup></div>|Еще не поддерживается|[Моментальные снимки BLOB-объектов](snapshots-overview.md)|
|Статические веб-сайты|Предварительный просмотр<div role="complementary" aria-labelledby="preview-form"><sup>3</sup></div>|Еще не поддерживается|[Размещение статических веб-сайтов в службе хранилища Azure](storage-blob-static-website.md)|
|Неизменяемое хранилище|Предварительный просмотр<div role="complementary" aria-labelledby="preview-form"><sup>3</sup></div>|Еще не поддерживается|[Хранение критически важных для бизнеса данных большого двоичного объекта с помощью неизменяемого хранилища](storage-blob-immutable-storage.md)|
|Blobfuse|Preview (Предварительный просмотр)|Еще не поддерживается|[Как подключить хранилище BLOB-объектов в качестве файловой системы с использованием blobfuse](storage-how-to-mount-container-linux.md)|
|Канал изменений|Еще не поддерживается|Еще не поддерживается|[Поддержка канала изменений в хранилище BLOB-объектов Azure](storage-blob-change-feed.md)|
|Отработка отказа учетной записи|Еще не поддерживается|Еще не поддерживается|[Аварийное восстановление и отработка отказа учетной записи](../common/storage-disaster-recovery-guidance.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Список управления доступом для контейнера больших двоичных объектов|Еще не поддерживается<div role="complementary" aria-labelledby="blob-container-ACL"><sup>2</sup></div>|Еще не поддерживается<div role="complementary" aria-labelledby="blob-container-ACL"><sup>2</sup></div>|[Set Container ACL](https://docs.microsoft.com/rest/api/storageservices/set-container-acl)|
|Личные домены|Еще не поддерживается|Еще не поддерживается|[Сопоставление личного домена с конечной точкой хранилища BLOB-объектов Azure](storage-custom-domain-name.md)|
|обратимое удаление.|Еще не поддерживается|Еще не поддерживается|[Soft delete for Azure Storage blobs](storage-blob-soft-delete.md) (Обратимое удаление больших двоичных объектов службы хранилища Azure)|

<div id="diagnostic-logging"><sup>1</sup> для учетных записей хранения блочных BLOB-объектов уровня "Премиум" невозможно включить журналы диагностики (классические) с помощью портал Azure. Их можно включить с помощью PowerShell.</div><br>

<div id="blob-container-ACL"><sup>2</sup> списки управления доступом можно задать в корневой папке контейнера, но не в самом контейнере.</div><br>

<div id="preview-form"><sup>3</sup> Чтобы использовать моментальные снимки, неизменяемое хранилище или статические веб-сайты с Data Lake Storage 2-го поколения, необходимо зарегистрироваться в предварительной версии, выполнив эту <a href=https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR2EUNXd_ZNJCq_eDwZGaF5VUOUc3NTNQSUdOTjgzVUlVT1pDTzU4WlRKRy4u>форму</a>.  </div>

## <a name="see-also"></a>См. также раздел

- [Известные проблемы с Azure Data Lake Storage 2-го поколения](data-lake-storage-known-issues.md)
- [Службы Azure, поддерживающие Azure Data Lake Storage 2-го поколения](data-lake-storage-supported-azure-services.md)
- [Платформы с открытым кодом, поддерживающие Azure Data Lake Storage 2-го поколения](data-lake-storage-supported-open-source-platforms.md)
- [Multi-protocol access on Azure Data Lake Storage (preview)](data-lake-storage-multi-protocol-access.md) (Доступ с использованием нескольких протоколов на Azure Data Lake Storage (предварительная версия))