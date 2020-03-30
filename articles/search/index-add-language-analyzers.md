---
title: Добавление языковых анализаторов в поле строк
titleSuffix: Azure Cognitive Search
description: Многоязычный лексический анализ текста для неанглоязычных запросов и индексов в Azure Cognitive Search.
manager: nitinme
author: Yahnoosh
ms.author: jlembicz
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/10/2019
translation.priority.mt:
- de-de
- es-es
- fr-fr
- it-it
- ja-jp
- ko-kr
- pt-br
- ru-ru
- zh-cn
- zh-tw
ms.openlocfilehash: a97bee27b74aa211b4d4d56547726555edefa87a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283151"
---
# <a name="add-language-analyzers-to-string-fields-in-an-azure-cognitive-search-index"></a>Добавление языковых анализаторов в области строк в индексе когнитивного поиска Azure

*Анализатор языка* является отдельным типом [анализатора текста](search-analyzers.md), которая выполняет лексический анализ с использованием лингвистических правил целевого языка. Каждое поле, поддерживающее поиск, обладает свойством **анализатора**. Если ваш индекс содержит переведенные строки, например отдельные поля для английского и китайского текста, вы можете указать анализаторы языка для каждого поля, что позволит получить доступ к богатым лингвистическим возможностям этих анализаторов.  

Azure Cognitive Search поддерживает 35 анализаторов при поддержке Lucene и 50 анализаторов, поддерживаемых собственной технологией обработки естественного языка Microsoft, используемой в Office и Bing.

## <a name="comparing-analyzers"></a>Сравнение анализаторов

Некоторые разработчики предпочитают более знакомое и простое решение Lucene с открытым исходным кодом. Анализаторы языка Lucene работают быстрее, однако анализаторы Майкрософт обладают расширенными функциями, например лемматизацией, разбором слов на составные части (на таких языках, как немецкий, датский, голландский, шведский, норвежский, эстонский, финский, венгерский, словацкий) и распознаванием сущностей (URL-адресов, адресов электронной почты, дат, чисел). Рекомендуем вам сравнить результаты работы анализаторов от Майкрософт и Lucene и выбрать оптимальный для себя вариант. 

В зависимости от языка индексирование на базе анализаторов Майкрософт занимает в среднем в два-три раза больше времени, чем с помощью эквивалентных анализаторов Lucene. Время выполнения поискового запроса не должно значительно отличаться от запросов средних размеров. 

### <a name="english-analyzers"></a>Анализаторы английского языка

По умолчанию используется анализатор Lucene (цен. категория "Стандартный"), который хорошо работает с английским языком. Однако вполне вероятно, что лучше него работают анализаторы для английского языка от Lucene или Майкрософт. 
 
+ Анализатор для английского языка Lucene расширяет возможности стандартного анализатора. Он удаляет из слов признаки принадлежности (символы 's в конце), выделяет корень слова с помощью алгоритма стеммера Портера и удаляет английские стоп-слова.  

+ Анализатор английского языка от Майкрософт вместо выделения корня слова выполняет лемматизацию. Это означает, что он может обрабатывать перегибается и нерегулярные формы слов гораздо лучше, что приводит к более релевантным результатам поиска 

## <a name="configuring-analyzers"></a>Настройка анализаторов

Языковые анализаторы используются как есть. Для каждого поля в определении индекса можно задать в свойстве **анализатора** имя, определяющее язык и лингвистический стек (Microsoft или Lucene). Этот же анализатор будет применяться при индексировании и поиске поля. Например, в одном индексе могут существовать отдельные поля для описаний гостиницы на английском, французском и испанском языках.

> [!NOTE]
> В момент индексации можно использовать другой анализатор языка, чем во время запроса для поля. Эта возможность зарезервирована для [пользовательских анализаторов.](index-add-custom-analyzers.md) По этой причине, если вы попытаетесь установить свойства **searchAnalyzer** или **indexAnalyzer** на имя анализатора языка, REST API вернет ответ на ошибку. Вместо этого необходимо использовать свойство **анализатора.**

Используйте параметр запроса **searchFields**, чтобы указать поля для других языков, в которых будет выполнен запрос. Вы можете просмотреть примеры запросов, которые включают свойство анализатора в [поисковые документы.](https://docs.microsoft.com/rest/api/searchservice/search-documents) 

Для получения дополнительной информации о свойствах индекса см [&#41;&#40;. ](https://docs.microsoft.com/rest/api/searchservice/create-index) Для получения дополнительной информации об анализе в Azure Cognitive Search [см.](https://docs.microsoft.com/azure/search/search-analyzers)

<a name="language-analyzer-list"></a>

## <a name="language-analyzer-list"></a>Список анализаторов языка 
 Ниже приведен список поддерживаемых языков и названия анализаторов Lucene и Майкрософт.  

|Язык|Название анализатора Майкрософт|Название анализатора Lucene|  
|--------------|-----------------------------|--------------------------|  
|Арабский|ar.microsoft|ar.lucene|  
|Армянский||hy.lucene|  
|Бенгальский|bn.microsoft||  
|Баскский||eu.lucene|  
|Болгарский|bg.microsoft|bg.lucene|  
|Каталонский|ca.microsoft|ca.lucene|  
|Китайский (упрощенный)|zh-Hans.microsoft|zh-Hans.lucene|  
|Китайский (традиционное письмо)|zh-Hant.microsoft|zh-Hant.lucene|  
|Хорватский|hr.microsoft||  
|Чешский|cs.microsoft|cs.lucene|  
|Датский|da.microsoft|da.lucene|  
|Нидерландский|nl.microsoft|nl.lucene|  
|Английский|en.microsoft|en.lucene|  
|Эстонский|et.microsoft||  
|Финский|fi.microsoft|fi.lucene|  
|Французский|fr.microsoft|fr.lucene|  
|Галисийский||gl.lucene|  
|Немецкий|de.microsoft|de.lucene|  
|Греческий|el.microsoft|el.lucene|  
|Гуджарати|gu.microsoft||  
|Иврит|he.microsoft||  
|Hindi|hi.microsoft|hi.lucene|  
|Венгерский|hu.microsoft|hu.lucene|  
|Исландский|is.microsoft||  
|Индонезийский (бахаса)|id.microsoft|id.lucene|  
|Ирландский||ga.lucene|  
|Итальянский|it.microsoft|it.lucene|  
|Японский|ja.microsoft|ja.lucene|  
|Каннада|kn.microsoft||  
|Корейский|ko.microsoft|ko.lucene|  
|Латышский|lv.microsoft|lv.lucene|  
|Литовский|lt.microsoft||  
|Малаялам|ml.microsoft||  
|Малайский (латиница)|ms.microsoft||  
|Маратхи|mr.microsoft||  
|Норвежский|nb.microsoft|no.lucene|  
|Персидский||fa.lucene|  
|Польский|pl.microsoft|pl.lucene|  
|Португальский (Бразилия)|pt-Br.microsoft|pt-Br.lucene|  
|Португальский (Португалия)|pt-Pt.microsoft|pt-Pt.lucene|  
|Панджаби|pa.microsoft||  
|Румынский|ro.microsoft|ro.lucene|  
|Русский|ru.microsoft|ru.lucene|  
|Сербский (кириллица)|sr-cyrillic.microsoft||  
|Сербский (латиница)|sr-latin.microsoft||  
|Словацкий|sk.microsoft||  
|Словенский|sl.microsoft||  
|Испанский|es.microsoft|es.lucene|  
|Шведский|sv.microsoft|sv.lucene|  
|Тамильский|ta.microsoft||  
|Телугу|te.microsoft||  
|Тайский|th.microsoft|th.lucene|  
|Турецкий|tr.microsoft|tr.lucene|  
|Украинский|uk.microsoft||  
|Урду|ur.microsoft||  
|Вьетнамский|vi.microsoft||  

 Все анализаторы, у которых в названии есть идентификатор **Lucene**, работают на базе [анализаторов языка Apache Lucene](https://lucene.apache.org/core/6_6_1/core/overview-summary.html ).

## <a name="see-also"></a>См. также  

+ [Создание индекса &#40;Azure Cognitive Search REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/create-index)  

+ [AnalyzerName Class](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername) (Класс AnalyzerName)  

