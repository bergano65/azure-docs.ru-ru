---
title: Перемещение данных в хранилище BLOB-объектов Azure и из него — процесс обработки и анализа данных группы
description: Перемещение данных в хранилище BLOB-объектов Azure и из него
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.component: team-data-science-process
ms.topic: article
ms.date: 11/04/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: a37c19ac0d3c053644b2f1f970ef9f84eac2f1df
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/10/2018
ms.locfileid: "53139863"
---
# <a name="move-data-to-and-from-azure-blob-storage"></a>Перемещение данных в хранилище BLOB-объектов Azure и из него

Процесс обработки и анализа данных группы требует приема или загрузки данных в различные среды хранения для обработки или анализа наиболее подходящим способом на каждом этапе процесса.

## <a name="different-technologies-for-moving-data"></a>Технологии перемещения данных

В следующих статьях описано, как перемещать данные из хранилища BLOB-объектов Azure и в него с помощью разных технологий.

* [Обозреватель хранилищ Azure](move-data-to-azure-blob-using-azure-storage-explorer.md)
* [AzCopy](move-data-to-azure-blob-using-azcopy.md)
* [Python](move-data-to-azure-blob-using-python.md)
* [SSIS](move-data-to-azure-blob-using-ssis.md)

Выбор метода зависит от сценария. Статья [Сценарии для расширенной аналитики в Машинном обучении Azure](plan-sample-scenarios.md) поможет определить ресурсы, необходимые для различных рабочих процессов обработки и анализа данных в рамках расширенного аналитического процесса.

> [!NOTE]
> Полное описание базовых принципов использования хранилища BLOB-объектов Azure см. в статьях [Приступая к работе с хранилищем BLOB-объектов Azure с помощью .NET](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) и [Основные понятия службы BLOB-объектов](https://msdn.microsoft.com/library/azure/dd179376.aspx).
> 
> 

## <a name="using-azure-data-factory"></a>Использование фабрики данных Azure

В качестве альтернативы можно использовать [фабрику данных Azure](https://azure.microsoft.com/services/data-factory/) для выполнения следующих действий: 

* создание и планирование конвейера, который скачивает данные из хранилища BLOB-объектов Azure; 
* передача данных в опубликованную веб-службу Машинного обучения Azure; 
* получение результатов прогнозной аналитики; 
* отправка результатов в хранилище. 

Дополнительные сведения см. в статье [Создание прогнозных конвейеров с помощью действий Машинного обучения Azure](../../data-factory/transform-data-using-machine-learning.md).

## <a name="prerequisites"></a>Предварительные требования
Для выполнения инструкций из этой статьи у вас должна быть подписка Azure, учетная запись хранения и соответствующий ключ к хранилищу данных для этой учетной записи. Чтобы отправлять и скачивать данные, необходимо знать имя учетной записи хранения Azure и ее ключ.

* Сведения о настройке подписки Azure см. на странице [Создайте бесплатную учетную запись Azure уже сегодня](https://azure.microsoft.com/pricing/free-trial/).
* Инструкции по созданию учетной записи хранения и получению сведений об учетной записи и ключах см. в статье [Об учетных записях хранения Azure](../../storage/common/storage-create-storage-account.md).

