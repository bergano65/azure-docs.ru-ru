---
title: Примеры для разработчиков, использующие Каталог данных Azure
description: В этой статье рассматриваются доступные примеры разработчиков, использующие REST API каталога данных.
ms.service: data-catalog
author: JasonWHowell
ms.author: jasonh
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: cdf90029d02680de4ce55e33b3ed0fe810d70b4c
ms.sourcegitcommit: 4b5dcdcd80860764e291f18de081a41753946ec9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/03/2019
ms.locfileid: "68775250"
---
# <a name="azure-data-catalog-developer-samples"></a>Примеры для разработчиков, использующие Каталог данных Azure

Приступите к разработке приложений каталога данных Azure с помощью REST API каталога данных. REST API каталога данных — это API на основе REST, который предоставляет программный доступ к ресурсам каталога данных для регистрации, аннотирования и поиска ресурсов-контейнеров данных программными средствами.

Ниже приведены примеры, доступные на сайте GitHub.

* [Начало работы с Каталогом данных Azure](https://github.com/Azure-Samples/data-catalog-dotnet-get-started/)
  
   В примере для начала работы показано, как пройти проверку подлинности в Azure AD для регистрации, поиска и удаления ресурса данных с помощью REST API каталога данных.
   
* [Приступая к работе с каталогом данных Azure с помощью субъекта-службы](https://github.com/Azure-Samples/data-catalog-dotnet-service-principal-get-started/)

   В этом примере показано, как зарегистрировать, найти и удалить ресурс данных с помощью REST API каталога данных. В этом примере используется проверка подлинности субъекта-службы.

* [Средство импорта и экспорта для каталога данных Azure](https://github.com/Azure-Samples/data-catalog-dotnet-import-export/)

   В этом примере показано, как использовать каталог данных REST API для выборки ресурсов из каталога данных Azure и их сериализации в файл. Он также показывает, как взять набор ресурсов-контейнеров, сериализованный в формат JSON, и отправить его в каталог. Он поддерживает экспорт подмножества каталога с помощью поискового запроса.

* [Полное регистрация и Добавление аннотаций в каталог данных Azure](https://github.com/Azure-Samples/data-catalog-dotnet-excel-register-data-assets/)
  
   В этом примере показано, как выполнить массовый регистрацию ресурсов данных из книги Excel с помощью REST API и Open XML.
  
* [Групповое импорт терминов глоссария в каталог данных Azure](https://github.com/Azure-Samples/data-catalog-bulk-import-glossary/)

   В этом примере показано, как импортировать термины глоссария из CSV-файлов в глоссарий ADC.

* [Групповое импорт связей в каталог данных Azure](https://github.com/Azure-Samples/data-catalog-bulk-import-relationship/)

   В этом примере показано, как программным способом импортировать сведения о связях из CSV-файла в каталог данных.

* [Публикация отношений в каталоге данных Azure](https://github.com/Azure-Samples/data-catalog-dotnet-publish-relationships/)

   В этом примере показано, как можно программно публиковать сведения о связях в каталоге данных.
   
## <a name="next-steps"></a>Следующие шаги
[Справочник по REST API каталога данных Azure](/rest/api/datacatalog/)
