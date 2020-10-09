---
title: Перемещение данных в хранилище BLOB-объектов Azure и из него — процесс обработки и анализа данных группы
description: Перемещение данных в хранилище BLOB-объектов Azure и из него с помощью Обозреватель службы хранилища Azure, AzCopy, Python и SSIS.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: fc58651bcb3b266b981fb953fd7341427d47fb2c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "76717578"
---
# <a name="move-data-to-and-from-azure-blob-storage"></a>Перемещение данных в хранилище BLOB-объектов Azure и из него

Процесс обработки и анализа данных группы требует приема или загрузки данных в различные среды хранения для обработки или анализа наиболее подходящим способом на каждом этапе процесса.

## <a name="different-technologies-for-moving-data"></a>Технологии перемещения данных

В следующих статьях описано, как перемещать данные из хранилища BLOB-объектов Azure и в него с помощью разных технологий.

* [Обозреватель хранилищ Azure](move-data-to-azure-blob-using-azure-storage-explorer.md)
* [AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10)
* [Python](move-data-to-azure-blob-using-python.md)
* [MSSQL Integration Services](move-data-to-azure-blob-using-ssis.md)

Выбор метода зависит от сценария. Статья [Сценарии для расширенной аналитики в Машинном обучении Azure](plan-sample-scenarios.md) поможет определить ресурсы, необходимые для различных рабочих процессов обработки и анализа данных в рамках расширенного аналитического процесса.

> [!NOTE]
> Полное описание базовых принципов использования хранилища BLOB-объектов Azure см. в статьях [Приступая к работе с хранилищем BLOB-объектов Azure с помощью .NET](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) и [Основные понятия службы BLOB-объектов](https://msdn.microsoft.com/library/azure/dd179376.aspx).
> 
> 

## <a name="using-azure-data-factory"></a>Использование Фабрики данных Azure

В качестве альтернативы можно использовать [фабрику данных Azure](https://azure.microsoft.com/services/data-factory/) для выполнения следующих действий: 

* создание и планирование конвейера, который скачивает данные из хранилища BLOB-объектов Azure; 
* передача данных в опубликованную веб-службу Машинного обучения Azure; 
* получение результатов прогнозной аналитики; 
* отправка результатов в хранилище. 

Дополнительные сведения см. в статье [Создание прогнозных конвейеров с помощью действий Машинного обучения Azure](../../data-factory/transform-data-using-machine-learning.md).

## <a name="prerequisites"></a>Предварительные требования
Для выполнения инструкций из этой статьи у вас должна быть подписка Azure, учетная запись хранения и соответствующий ключ к хранилищу данных для этой учетной записи. Перед отправкой и загрузкой данных необходимо указать имя учетной записи хранения Azure и ключ учетной записи.

* Сведения о настройке подписки Azure см. в [статье Бесплатная пробная версия на один месяц](https://azure.microsoft.com/pricing/free-trial/).
* Инструкции по созданию учетной записи хранения и сведениям об учетных записях и ключах [см. в](../../storage/common/storage-create-storage-account.md)этой статье.

