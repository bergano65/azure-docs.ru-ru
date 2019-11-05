---
title: Новые возможности службы
titleSuffix: Azure Cognitive Search
description: Объявления о новых и улучшенных функциях, включая переименование службы поиска Azure в Azure Когнитивный поиск.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: e81eaf232e3234ac4de0cfb7412e23709f0c0b99
ms.sourcegitcommit: 3f8017692169bd75483eefa96c225d45cd497f06
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73549092"
---
# <a name="whats-new-in-azure-cognitive-search"></a>Новые возможности Azure Когнитивный поиск

Узнайте о новых возможностях службы. Закладка этой страницы для обновления со службой.

<a name="new-service-name"></a>

## <a name="new-service-name-for-azure-search"></a>Новое имя службы для поиска Azure

Теперь служба поиска Azure переименована в **azure когнитивный Поиск** , чтобы отразить расширенное использование функций для работы с обучением и обработки искусственного интеллекта в основных операциях. В то время как функции «автообучение» добавляют новые возможности, использование AI является строго необязательным. Вы можете по-прежнему использовать Azure Когнитивный поиск без искусственного интеллекта для создания полнофункциональных, полнотекстовых поисковых решений по закрытому, разнородных текстовому содержимому в индексе, который вы создаете и управляете в облаке. 

Версии API, пакеты NuGet, пространства имен и конечные точки не изменяются. Изменение имени службы не влияет на имеющиеся решения поиска.

## <a name="feature-announcements"></a>Объявления о функциях

4 ноября 2019-Ignite Конференция

+ [Добавочное индексирование](cognitive-search-incremental-indexing-conceptual.md), теперь в предварительной версии, позволяет обрабатывать или повторно обрабатывать только те шаги, которые абсолютно необходимы при внесении изменений в конвейер обогащения. Это особенно полезно при наличии ранее проанализированного содержимого. Выходные данные дорогостоящего анализа хранятся, а затем используются в качестве базиса для дополнительного индексирования или дополнения.

<!-- 
+ Custom Entity Lookup is a cognitive skill used during indexing that allows you to provide a list of custom entities (such as part numbers, diseases, or names of locations you care about) that should be found within the text. It supports fuzzy matching, case-insensitive matching, and entity synonyms. -->

+ [Извлечение документов](cognitive-search-skill-document-extraction.md) — это восприятный навык, используемый во время индексирования, который позволяет извлекать содержимое файла из набора навыков. Ранее перед выполнением набора навыков произошло взлом документов. С добавлением этого навыка можно также выполнить эту операцию в рамках выполнения набора навыков.

+ [Преобразование текста](cognitive-search-skill-text-translation.md) — это восприятный навык, используемый во время индексирования, который оценивает текст и для каждой записи возвращает текст, преобразованный на указанный целевой язык.

+ [Шаблоны Power BI](https://github.com/Azure-Samples/cognitive-search-templates/blob/master/README.md) могут быстро приступить к визуализациям и анализу расширенного содержимого в хранилище знаний в Power BI Desktop. Этот шаблон предназначен для проектов таблиц Azure, созданных с помощью [мастера импорта данных](knowledge-store-create-portal.md).

## <a name="service-updates"></a>Обновления службы

[Объявления об обновлении службы](https://azure.microsoft.com/updates/?product=search&status=all) для когнитивный Поиск Azure можно найти на веб-сайте Azure.