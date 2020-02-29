---
title: Функции хранилища BLOB-объектов, доступные в Azure Data Lake Storage 2-го поколения | Документация Майкрософт
description: Узнайте, какие функции хранилища BLOB-объектов можно использовать с Azure Data Lake Storage 2-го поколения
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 215244204aa58cc2fdedc639d48e01b514759694
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/29/2020
ms.locfileid: "78196014"
---
# <a name="blob-storage-features-available-in-azure-data-lake-storage-gen2"></a>Функции хранилища BLOB-объектов, доступные в Azure Data Lake Storage 2-го поколения

Функции хранилища BLOB-объектов, такие как [ведение журнала диагностики](../common/storage-analytics-logging.md), [уровни доступа](storage-blob-storage-tiers.md)и [политики управления жизненным циклом хранилища BLOB-объектов](storage-lifecycle-management-concepts.md) , теперь работают с учетными записями, имеющими иерархическое пространство имен. Таким образом, можно включить иерархические пространства имен в учетных записях хранения BLOB-объектов без потери доступа к этим функциям.

В этой таблице перечислены функции хранилища BLOB-объектов, которые можно использовать с Azure Data Lake Storage 2-го поколения. Элементы, отображаемые в этих таблицах, со временем будут меняться, так как поддержка будет расширена.

## <a name="supported-blob-storage-features"></a>Поддерживаемые функции хранилища BLOB-объектов

> [!NOTE]
> Уровень поддержки относится только к том, как эта функция поддерживается Data Lake Storage 2-го поколения.

|Функция хранилища BLOB-объектов |Уровень поддержки |Связанные статьи |
|---------------|-------------------|---|
|"Горячий" уровень доступа|Общедоступная версия|[Хранилище BLOB-объектов Azure: горячий, холодный и архивный уровни доступа](storage-blob-storage-tiers.md)|
|"Холодный" уровень доступа|Общедоступная версия|[Хранилище BLOB-объектов Azure: горячий, холодный и архивный уровни доступа](storage-blob-storage-tiers.md)|
|События|Общедоступная версия|[Reacting to Blob storage events (preview)](storage-blob-event-overview.md) (Реагирование на события хранилища BLOB-объектов)|
|Метрики (классические)|Общедоступная версия|[Метрики аналитики службы хранилища Azure (классическая модель)](../common/storage-analytics-metrics.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Метрики в Azure Monitor|Общедоступная версия|[Метрики службы хранилища Azure в Azure Monitor](../common/storage-metrics-in-azure-monitor.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Команды PowerShell для хранилища BLOB-объектов|Общедоступная версия|[Краткое руководство. Отправка, скачивание и вывод списка больших двоичных объектов с помощью PowerShell](storage-quickstart-blobs-powershell.md)|
|Команды Azure CLI хранилища BLOB-объектов|Общедоступная версия|[Краткое руководство. Создание, скачивание и вывод списка больших двоичных объектов с помощью Azure CLI](storage-quickstart-blobs-cli.md)|
|API хранилища BLOB-объектов|Общедоступная версия|[Краткое руководство. Клиентская библиотека хранилища BLOB-объектов Azure версии 12 для .NET](storage-quickstart-blobs-dotnet.md)<br>[Краткое руководство. Управление большими двоичными объектами с помощью пакета SDK для Java версии 12](storage-quickstart-blobs-java.md)<br>[Краткое руководство. Управление большими двоичными объектами с помощью пакета SDK для Python версии 12](storage-quickstart-blobs-python.md)<br>[Краткое руководство. Управление большими двоичными объектами с помощью пакета SDK для JavaScript версии 12 в Node. js](storage-quickstart-blobs-nodejs.md)|
|Архивный уровень доступа|Preview (Предварительный просмотр)|[Хранилище BLOB-объектов Azure: горячий, холодный и архивный уровни доступа](storage-blob-storage-tiers.md)|
|Политики управления жизненным циклом|Preview (Предварительный просмотр)|[Управление жизненным циклом хранилища BLOB-объектов Azure (предварительная версия)](storage-lifecycle-management-concepts.md)|
|Журналы диагностики|Preview (Предварительный просмотр)|[Ведение журнала аналитики службы хранилища Azure](../common/storage-analytics-logging.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Канал изменений|Еще не поддерживается|[Поддержка веб-канала изменений в хранилище BLOB-объектов Azure](storage-blob-change-feed.md)|
|Переключение учетной записи|Еще не поддерживается|[Аварийное восстановление и отработка отказа учетной записи](../common/storage-disaster-recovery-guidance.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|ACL контейнера BLOB-объектов|Еще не поддерживается|[Set Container ACL](https://docs.microsoft.com/rest/api/storageservices/set-container-acl)|
|Личные домены|Еще не поддерживается|[Сопоставьте личный домен с конечной точкой хранилища BLOB-объектов Azure](storage-custom-domain-name.md)|
|Неизменяемое хранилище|Еще не поддерживается|[Хранение критически важных для бизнеса данных большого двоичного объекта с неизменяемым хранилищем](storage-blob-immutable-storage.md)|
|Моментальные снимки|Еще не поддерживается|[Создание моментального снимка BLOB-объекта и управление им в .NET](storage-blob-snapshots.md)|
|обратимое удаление.|Еще не поддерживается|[Soft delete for Azure Storage blobs](storage-blob-soft-delete.md) (Обратимое удаление больших двоичных объектов службы хранилища Azure)|
|Статические веб-сайты|Еще не поддерживается|[Размещение статических веб-сайтов в службе хранилища Azure](storage-blob-static-website.md)|
|Вход в Azure Monitor|Еще не поддерживается|Пока недоступно|
|Блочные BLOB-объекты Premium|Еще не поддерживается|[Создание учетной записи Блоккблобстораже](storage-blob-create-account-block-blob.md)|

## <a name="see-also"></a>См. также раздел

- [Известные проблемы с Azure Data Lake Storage 2-го поколения](data-lake-storage-known-issues.md)
- [Службы Azure, которые поддерживают Azure Data Lake Storage 2-го поколения](data-lake-storage-supported-azure-services.md)
- [Платформы с открытым кодом, поддерживающие Azure Data Lake Storage 2-го поколения](data-lake-storage-supported-open-source-platforms.md)
- [Multi-protocol access on Azure Data Lake Storage (preview)](data-lake-storage-multi-protocol-access.md) (Доступ с использованием нескольких протоколов на Azure Data Lake Storage (предварительная версия))