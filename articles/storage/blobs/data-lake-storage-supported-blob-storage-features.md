---
title: Функции хранения Blob доступны в Azure Data Lake Storage Gen2 Документы Майкрософт
description: Узнайте, какие функции хранения Blob можно использовать с помощью Хранилища озер данных Azure Gen2
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: b270ce3cd15dbd1e8dd53bd60376a87d6e08f75c
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80637212"
---
# <a name="blob-storage-features-available-in-azure-data-lake-storage-gen2"></a>Функции хранения Blob доступны в Azure Data Lake Storage Gen2

Функции Blob Storage, такие как [диагностические журналы,](../common/storage-analytics-logging.md) [уровни доступа](storage-blob-storage-tiers.md)и [политики управления жизненным циклом blob Storage,](storage-lifecycle-management-concepts.md) теперь работают с учетными записями, которые имеют иерархическое пространство имен. Таким образом, можно включить иерархические пространства имен в учетных записях хранения Blob, не теряя доступа к этим функциям.

В этой таблице перечислены функции хранения Blob, которые можно использовать с помощью хранилища azure Data Lake Data Gen2. Элементы, которые отображаются в этих таблицах, будут меняться с течением времени по мере расширения поддержки.

## <a name="supported-blob-storage-features"></a>Поддерживаемые функции хранения Blob

> [!NOTE]
> Уровень поддержки относится только к тому, как функция поддерживается data Lake Storage Gen2.

|Функция хранения blob |Уровень поддержки |Связанные статьи |
|---------------|-------------------|---|
|"Горячий" уровень доступа|Общедоступная версия|[Хранилище BLOB-объектов Azure: горячий, холодный и архивный уровни доступа](storage-blob-storage-tiers.md)|
|"Холодный" уровень доступа|Общедоступная версия|[Хранилище BLOB-объектов Azure: горячий, холодный и архивный уровни доступа](storage-blob-storage-tiers.md)|
|События|Общедоступная версия|[Реагирование на события хранилища BLOB-объектов](storage-blob-event-overview.md)|
|Метрики (классические)|Общедоступная версия|[Показатели аналитики azure Storage (Классический)](../common/storage-analytics-metrics.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Метрики в Azure Monitor|Общедоступная версия|[Метрики службы хранилища Azure в Azure Monitor](../common/storage-metrics-in-azure-monitor.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Команды памяти хранилища Blob PowerShell|Общедоступная версия|[Быстрый запуск: Загрузить, скачать и список капли с PowerShell](storage-quickstart-blobs-powershell.md)|
|Команды хранения Blob Azure CLI|Общедоступная версия|[Быстрый запуск: Создайте, загрузите и перечислите капли с помощью Azure CLI](storage-quickstart-blobs-cli.md)|
|API хранилища BLOB-объектов|Общедоступная версия|[Быстрый запуск: Библиотека хранения данных Azure Blob v12 для .NET](storage-quickstart-blobs-dotnet.md)<br>[Быстрый запуск: Управление капли с Java v12 SDK](storage-quickstart-blobs-java.md)<br>[Быстрый старт: Управление капли с Python v12 SDK](storage-quickstart-blobs-python.md)<br>[Быстрый запуск: Управление капли с JavaScript v12 SDK в Node.js](storage-quickstart-blobs-nodejs.md)|
|Архив доступ уровень|Предварительный просмотр|[Хранилище BLOB-объектов Azure: горячий, холодный и архивный уровни доступа](storage-blob-storage-tiers.md)|
|Политики управления жизненным циклом|Предварительный просмотр|[Управление жизненным циклом хранилища BLOB-объектов Azure](storage-lifecycle-management-concepts.md)|
|Журналы диагностики|Общедоступная версия|[Ведение журнала Аналитики Службы хранилища Azure](../common/storage-analytics-logging.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Канал изменений|Еще не поддерживается|[Изменение поддержки кормов в хранилище Azure Blob](storage-blob-change-feed.md)|
|Сбой учетной записи|Еще не поддерживается|[Восстановление после стихийных бедствий и сбой учетной записи](../common/storage-disaster-recovery-guidance.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Blob контейнер ACL|Еще не поддерживается|[Set Container ACL](https://docs.microsoft.com/rest/api/storageservices/set-container-acl)|
|Личные домены|Еще не поддерживается|[Карта пользовательского домена в конечную точку хранения Azure Blob](storage-custom-domain-name.md)|
|Неизменяемое хранилище|Еще не поддерживается|[Храните критически важные для бизнеса данные с непреложным хранилищем](storage-blob-immutable-storage.md)|
|Моментальные снимки|Еще не поддерживается|[Создание и управление снимком капли в .NET](storage-blob-snapshots.md)|
|обратимое удаление.|Еще не поддерживается|[Soft delete for Azure Storage blobs](storage-blob-soft-delete.md) (Обратимое удаление больших двоичных объектов службы хранилища Azure)|
|Статические веб-сайты|Еще не поддерживается|[Размещение статических веб-сайтов в службе хранилища Azure](storage-blob-static-website.md)|
|Регистрация в Azure Monitor|Еще не поддерживается|Пока недоступно|
|Премиум блок капли|Еще не поддерживается|[Создание учетной записи BlockBlobStorage](storage-blob-create-account-block-blob.md)|

## <a name="see-also"></a>См. также

- [Известные проблемы с Azure Data Lake Storage 2-го поколения](data-lake-storage-known-issues.md)
- [Службы Azure, поддерживающие хранение озер данных Azure Gen2](data-lake-storage-supported-azure-services.md)
- [Платформы с открытым исходным кодом, поддерживающие данные Azure Data Lake Storage Gen2](data-lake-storage-supported-open-source-platforms.md)
- [Multi-protocol access on Azure Data Lake Storage (preview)](data-lake-storage-multi-protocol-access.md) (Доступ с использованием нескольких протоколов на Azure Data Lake Storage (предварительная версия))