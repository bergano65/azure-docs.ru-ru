---
title: Функции хранилища BLOB-объектов, доступные в Azure Data Lake Storage 2-го поколения | Документация Майкрософт
description: Узнайте, какие функции хранилища BLOB-объектов можно использовать с Azure Data Lake Storage 2-го поколения
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 05/10/2020
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: ab704f53e608e7021f53a5d4b0a3e06c2ab807a1
ms.sourcegitcommit: 801a551e047e933e5e844ea4e735d044d170d99a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/11/2020
ms.locfileid: "83007452"
---
# <a name="blob-storage-features-available-in-azure-data-lake-storage-gen2"></a>Функции хранилища BLOB-объектов, доступные в Azure Data Lake Storage 2-го поколения

Функции хранилища BLOB-объектов, такие как [ведение журнала диагностики](../common/storage-analytics-logging.md), [уровни доступа](storage-blob-storage-tiers.md)и [политики управления жизненным циклом хранилища BLOB-объектов](storage-lifecycle-management-concepts.md) , теперь работают с учетными записями, имеющими иерархическое пространство имен. Таким образом, можно включить иерархические пространства имен в учетных записях хранения BLOB-объектов без потери доступа к этим функциям.

В этой таблице перечислены функции хранилища BLOB-объектов, которые можно использовать с Azure Data Lake Storage 2-го поколения. Элементы, отображаемые в этих таблицах, со временем будут меняться, так как поддержка будет расширена. Дополнительные сведения о конкретных проблемах, связанных с состоянием предварительной версии функции, см. в статье [Известные проблемы](data-lake-storage-known-issues.md) .

## <a name="supported-blob-storage-features"></a>Поддерживаемые функции хранилища BLOB-объектов

> [!NOTE]
> Уровень поддержки относится только к том, как эта функция поддерживается Data Lake Storage 2-го поколения. 
>
> [Учетные записи хранения BLOB-объектов с расширенными уровнями производительности](storage-blob-create-account-block-blob.md) для Data Lake Storage 2-го поколения сейчас доступны в общедоступной предварительной версии. Уровни поддержки для этих типов учетных записей отображаются в столбце **учетные записи хранения блочных BLOB-объектов Premium** .

|Функция хранилища BLOB-объектов |Учетные записи хранения общего назначения версии 2|Учетные записи хранения блочных BLOB-объектов категории "Премиум" |Похожие статьи |
|---------------|-------------------|---|
|"Горячий" уровень доступа|Общедоступная версия|Не поддерживается|[Хранилище BLOB-объектов Azure: горячий, холодный и архивный уровни доступа](storage-blob-storage-tiers.md)|
|"Холодный" уровень доступа|Общедоступная версия|Не поддерживается|[Хранилище BLOB-объектов Azure: горячий, холодный и архивный уровни доступа](storage-blob-storage-tiers.md)|
|События|Общедоступная версия|Предварительный просмотр|[Реагирование на события хранилища BLOB-объектов](storage-blob-event-overview.md)|
|Метрики (классические)|Общедоступная версия|Не поддерживается|[Метрики аналитики службы хранилища Azure (классическая модель)](../common/storage-analytics-metrics.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Метрики в Azure Monitor|Общедоступная версия|Предварительный просмотр|[Метрики службы хранилища Azure в Azure Monitor](../common/storage-metrics-in-azure-monitor.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Команды PowerShell для хранилища BLOB-объектов|Общедоступная версия|Предварительный просмотр|[Краткое руководство. Отправка, скачивание и вывод списка больших двоичных объектов с помощью PowerShell](storage-quickstart-blobs-powershell.md)|
|Команды Azure CLI хранилища BLOB-объектов|Общедоступная версия|Предварительный просмотр|[Краткое руководство. Создание, скачивание и вывод списка больших двоичных объектов с помощью Azure CLI](storage-quickstart-blobs-cli.md)|
|API хранилища BLOB-объектов|Общедоступная версия|Предварительный просмотр|[Краткое руководство. Клиентская библиотека хранилища BLOB-объектов Azure версии 12 для .NET](storage-quickstart-blobs-dotnet.md)<br>[Краткое руководство. Управление большими двоичными объектами с помощью пакета SDK для Java версии 12](storage-quickstart-blobs-java.md)<br>[Краткое руководство. Управление большими двоичными объектами с помощью пакета SDK для Python версии 12](storage-quickstart-blobs-python.md)<br>[Краткое руководство. Управление большими двоичными объектами с помощью пакета SDK для JavaScript версии 12 в Node. js](storage-quickstart-blobs-nodejs.md)|
|Журналы диагностики|Общедоступная версия|Предварительный просмотр <div role="complementary" aria-labelledby="diagnostic-logging"><sup>1</sup></div> |[Ведение журнала аналитики службы хранилища Azure](../common/storage-analytics-logging.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Архивный уровень доступа|Предварительный просмотр|Не поддерживается|[Хранилище BLOB-объектов Azure: горячий, холодный и архивный уровни доступа](storage-blob-storage-tiers.md)|
|Политики управления жизненным циклом|Предварительный просмотр|Еще не поддерживается|[Управление жизненным циклом хранилища BLOB-объектов Azure](storage-lifecycle-management-concepts.md)|
|Канал изменений|Еще не поддерживается|Еще не поддерживается|[Поддержка веб-канала изменений в хранилище BLOB-объектов Azure](storage-blob-change-feed.md)|
|Переключение учетной записи|Еще не поддерживается|Еще не поддерживается|[Аварийное восстановление и отработка отказа учетной записи](../common/storage-disaster-recovery-guidance.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|ACL контейнера BLOB-объектов|Еще не поддерживается|Еще не поддерживается|[Set Container ACL](https://docs.microsoft.com/rest/api/storageservices/set-container-acl)|
|Личные домены|Еще не поддерживается|Еще не поддерживается|[Сопоставьте личный домен с конечной точкой хранилища BLOB-объектов Azure](storage-custom-domain-name.md)|
|Неизменяемое хранилище|Еще не поддерживается|Еще не поддерживается|[Хранение критически важных для бизнеса данных большого двоичного объекта с неизменяемым хранилищем](storage-blob-immutable-storage.md)|
|Моментальные снимки|Еще не поддерживается|Еще не поддерживается|[Создание моментального снимка BLOB-объекта и управление им в .NET](storage-blob-snapshots.md)|
|обратимое удаление.|Еще не поддерживается|Еще не поддерживается|[Soft delete for Azure Storage blobs](storage-blob-soft-delete.md) (Обратимое удаление больших двоичных объектов службы хранилища Azure)|
|Статические веб-сайты|Еще не поддерживается|Еще не поддерживается|[Размещение статических веб-сайтов в службе хранилища Azure](storage-blob-static-website.md)|
|Вход в Azure Monitor|Еще не поддерживается|Еще не поддерживается|Пока недоступно|

<div id="diagnostic-logging"><sup>1</sup> Для учетных записей хранения блочных BLOB-объектов уровня "Премиум" невозможно включить журналы диагностики (классические) с помощью портал Azure. Включите их с помощью PowerShell.</div>

## <a name="see-also"></a>См. также раздел

- [Известные проблемы с Azure Data Lake Storage 2-го поколения](data-lake-storage-known-issues.md)
- [Службы Azure, которые поддерживают Azure Data Lake Storage 2-го поколения](data-lake-storage-supported-azure-services.md)
- [Платформы с открытым кодом, поддерживающие Azure Data Lake Storage 2-го поколения](data-lake-storage-supported-open-source-platforms.md)
- [Multi-protocol access on Azure Data Lake Storage (preview)](data-lake-storage-multi-protocol-access.md) (Доступ с использованием нескольких протоколов на Azure Data Lake Storage (предварительная версия))