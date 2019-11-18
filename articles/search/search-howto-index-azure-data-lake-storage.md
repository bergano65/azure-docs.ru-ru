---
title: Поиск Azure Data Lake Storage 2-го поколения (Предварительная версия)
titleSuffix: Azure Cognitive Search
description: Узнайте, как индексировать содержимое и метаданные в Azure Data Lake Storage 2-го поколения. Сейчас эта функция доступна в общедоступной предварительной версии
manager: nitinme
author: markheff
ms.author: maheff
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 628b8bb5c3cb83ae6038a7150420893d7abe61d5
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/15/2019
ms.locfileid: "74112280"
---
# <a name="indexing-documents-in-azure-data-lake-storage-gen2"></a>Индексирование документов в Azure Data Lake Storage 2-го поколения

> [!IMPORTANT] 
> Поддержка Azure Data Lake Storage 2-го поколения в настоящее время доступна в общедоступной предварительной версии. Для предварительной версии функции соглашение об уровне обслуживания не предусмотрено. Мы не рекомендуем использовать ее в рабочей среде. Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Вы можете запросить доступ к предварительным просмотрам, заполнив [эту форму](https://aka.ms/azure-cognitive-search/indexer-preview). Эта функция предоставляется в [версии REST API 2019-05-06-Preview](search-api-preview.md). В настоящее время нет поддержки портала или пакета SDK для .NET.


При настройке учетной записи хранения Azure можно включить [Иерархическое пространство имен](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-namespace). Это позволяет организовать сбор содержимого в учетной записи в иерархию каталогов и вложенных подкаталогов. Включив иерархическое пространство имен, вы включаете [Azure Data Lake Storage 2-го поколения](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction).

В этой статье описывается, как приступить к индексированию документов, которые находятся в Azure Data Lake Storage 2-го поколения.

## <a name="set-up-azure-data-lake-storage-gen2-indexer"></a>Настройка индексатора Azure Data Lake Storage 2-го поколения

Для индексации содержимого из Data Lake Storage 2-го поколения необходимо выполнить несколько действий.

### <a name="step-1-sign-up-for-the-preview"></a>Шаг 1. Регистрация в предварительной версии

Зарегистрируйтесь в предварительной версии индексатора Data Lake Storage 2-го поколения, заполнив [эту форму](https://aka.ms/azure-cognitive-search/indexer-preview). После принятия предварительной версии вы получите сообщение электронной почты с подтверждением.

### <a name="step-2-follow-the-azure-blob-storage-indexing-setup-steps"></a>Шаг 2. Следуйте инструкциям по настройке индексирования хранилища BLOB-объектов Azure

После получения подтверждения о том, что предварительная версия регистрации прошла успешно, можно приступать к созданию конвейера индексирования.

Вы можете индексировать содержимое и метаданные из Data Lake Storage 2-го поколения с помощью [REST API версии 2019-05-06-Preview](search-api-preview.md). В настоящее время отсутствует поддержка портала или пакета SDK для .NET.

Индексирование содержимого в Data Lake Storage 2-го поколения идентично индексированию содержимого в хранилище BLOB-объектов Azure. Чтобы узнать, как настроить Data Lake Storage 2-го поколения источник данных, индекс и индексатор, см. статью [индексирование документов в хранилище BLOB-объектов Azure с помощью azure когнитивный Поиск](search-howto-indexing-azure-blob-storage.md). В статье хранилище BLOB-объектов также приводятся сведения о поддерживаемых форматах документов, свойствах метаданных больших двоичных объектов, извлечении, добавочном индексировании и т. д. Эта информация будет одинаковой для Data Lake Storage 2-го поколения.

## <a name="access-control"></a>управление доступом;

Azure Data Lake Storage 2-го поколения реализует [модель управления доступом](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control) , которая поддерживает как управление доступом на основе РОЛЕЙ (RBAC), так и POSIX-подобные списки управления доступом (ACL). При индексировании содержимого из Data Lake Storage 2-го поколения Azure Когнитивный поиск не извлечет сведения о RBAC и ACL из содержимого. В результате эта информация не будет включаться в индекс Azure Когнитивный поиск.

Если важна поддержка контроля доступа для каждого документа в индексе, разработчик приложения должен реализовать [фильтрацию безопасности](https://docs.microsoft.com/azure/search/search-security-trimming-for-azure-search).