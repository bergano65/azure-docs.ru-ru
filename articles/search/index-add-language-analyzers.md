---
title: Добавление анализаторов языка в службе "Поиск Azure"
description: Многоязычный лексический анализ текста для запросов и индексов не на английском языке в службе "Поиск Azure".
ms.date: 02/14/2019
services: search
ms.service: search
ms.topic: conceptual
author: Yahnoosh
ms.author: jlembicz
ms.manager: cgronlun
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
ms.openlocfilehash: 9584f0ee4264724787c5913c0d6baa4c2769fa82
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58085683"
---
# <a name="add-language-analyzers-to-an-azure-search-index"></a>Добавление анализатора языка в индекс Поиска Azure

*Анализатор языка* является отдельным типом [анализатора текста](search-analyzers.md), которая выполняет лексический анализ с использованием лингвистических правил целевого языка. Каждое поле, поддерживающее поиск, обладает свойством **анализатора**. Если ваш индекс содержит переведенные строки, например отдельные поля для английского и китайского текста, вы можете указать анализаторы языка для каждого поля, что позволит получить доступ к богатым лингвистическим возможностям этих анализаторов.  

Служба "Поиск Azure" поддерживает 35 анализаторов на базе технологии Lucene и 50 анализаторов на базе собственной технологии Майкрософт для обработки естественных языков, которая используется в приложениях Office и поисковой системе Bing.

## <a name="comparing-analyzers"></a>Сравнение анализаторов

Некоторые разработчики предпочитают более знакомое и простое решение Lucene с открытым исходным кодом. Анализаторы языка Lucene работают быстрее, однако анализаторы Майкрософт обладают расширенными функциями, например лемматизацией, разбором слов на составные части (на таких языках, как немецкий, датский, голландский, шведский, норвежский, эстонский, финский, венгерский, словацкий) и распознаванием сущностей (URL-адресов, адресов электронной почты, дат, чисел). Рекомендуем вам сравнить результаты работы анализаторов от Майкрософт и Lucene и выбрать оптимальный для себя вариант. 

В зависимости от языка индексирование на базе анализаторов Майкрософт занимает в среднем в два-три раза больше времени, чем с помощью эквивалентных анализаторов Lucene. Время выполнения поискового запроса не должно значительно отличаться от запросов средних размеров. 

### <a name="english-analyzers"></a>Анализаторы английского языка

По умолчанию используется анализатор Lucene (цен. категория "Стандартный"), который хорошо работает с английским языком. Однако вполне вероятно, что лучше него работают анализаторы для английского языка от Lucene или Майкрософт. 
 
+ Анализатор для английского языка Lucene расширяет возможности стандартного анализатора. Он удаляет из слов признаки принадлежности (символы 's в конце), выделяет корень слова с помощью алгоритма стеммера Портера и удаляет английские стоп-слова.  

+ Анализатор английского языка от Майкрософт вместо выделения корня слова выполняет лемматизацию. Это означает, что он гораздо лучше обрабатывает флективные и неправильные формы слов, благодаря чему вы получаете более подходящие результаты поиска. 

  > [!Tip]
  > [Search Analyzer Demo](https://alice.unearth.ai/) обеспечивает параллельное сравнение результатов, полученных от стандартного анализатора Lucene, анализатора английского языка Lucene и обработчика естественного языка Майкрософт (английского языка). Для всех данных поиска, которые вы вводите, результаты каждого анализатора отображаются в соседних областях.

## <a name="configuring-analyzers"></a>Настройка анализаторов

Языковые анализаторы используются как есть. Для каждого поля в определении индекса можно задать в свойстве **анализатора** имя, определяющее язык и лингвистический стек (Microsoft или Lucene). Этот же анализатор будет применяться при индексировании и поиске поля. Например, в одном индексе могут существовать отдельные поля для описаний гостиницы на английском, французском и испанском языках. Кроме того, вместо **анализатора** вы можете использовать **indexAnalyzer** и **searchAnalyzer** для разных правил анализа во время индексации и времени запроса. 

Используйте параметр запроса **searchFields**, чтобы указать поля для других языков, в которых будет выполнен запрос. Вы можете ознакомиться с примерами запросов, в которых используется свойство анализатора, в разделе [Поиск документов](https://docs.microsoft.com/rest/api/searchservice/search-documents). 

Дополнительные сведения о свойствах индекса см. в статье [Create Index &#40;Azure Search Service REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/create-index) (Создание индекса (REST API Службы поиска Azure)). Дополнительные сведения об анализе в службе "Поиск Azure" см. в статье [Анализаторы для обработки текста в службе "Поиск Azure"](https://docs.microsoft.com/azure/search/search-analyzers).

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
|Каталанский|ca.microsoft|ca.lucene|  
|Китайский (упрощенное письмо)|zh-Hans.microsoft|zh-Hans.lucene|  
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
|Хинди|hi.microsoft|hi.lucene|  
|Венгерский|hu.microsoft|hu.lucene|  
|Исландский|is.microsoft||  
|Индонезийский (бахаса)|id.microsoft|id.lucene|  
|Ирландский||ga.lucene|  
|Итальянский|it.microsoft|it.lucene|  
|Японский|ja.microsoft|ja.lucene|  
|Каннада|ka.microsoft||  
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

 Все анализаторы, у которых в названии есть идентификатор **Lucene**, работают на базе [анализаторов языка Apache Lucene](https://lucene.apache.org/core/4_9_0/core/overview-summary.html ).

## <a name="see-also"></a>См. также  
 [Create Index &#40;Azure Search Service REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/create-index) (Создание индекса (REST API Службы поиска Azure))  
 [AnalyzerName Class](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername) (Класс AnalyzerName)  
 [Video: module 7 of Azure Search MVA presentation](https://channel9.msdn.com/Series/Adding-Microsoft-Azure-Search-to-Your-Websites-and-Apps/07) (Видео. 7 модуль презентации MVA, посвященной службе "Поиск Azure").  

