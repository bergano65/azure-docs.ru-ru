---
title: Языковые концепции - NA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker поддерживает содержимое базы знаний на различных языках. При этом каждая служба QnA Maker должна быть зарезервирована для одного языка. Первая база знаний, созданная, ориентированная на конкретную службу SnA Maker, устанавливает язык этой службы.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 09/24/2019
ms.author: diberry
ms.openlocfilehash: 38701e8bbef1c5d78eca2242105e81fe7261c0f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220636"
---
# <a name="language-support-of-knowledge-base-content-for-qna-maker"></a>Языковая поддержка содержимого базы знаний для QnA Maker

Язык службы выбирается при создании первой базы знаний в ресурсе. Все дополнительные базы знаний в ресурсе должны быть на одном языке.

Язык определяет релевантность результатов, которые предоставляет компания «NA Maker» в ответ на запросы пользователей.

## <a name="one-language-for-all-knowledge-bases-in-resource"></a>Один язык для всех баз знаний в ресурсе

В то же время компания «NA Maker» позволяет выбрать язык для службы в связи с созданием первой базы знаний. Для всех баз знаний в ресурсе NA Maker все они должны быть на одном языке. Этот язык не может быть изменен.

Создание баз знаний на разных языках на одном ресурсе негативно влияет на актуальность результатов, которые компания «NA Maker» предоставляет в ответ на запросы пользователей.

Просмотрите список [поддерживаемых языков](../overview/language-support.md#languages-supported) и то, как языки влияют на [соответствие и актуальность.](#query-matching-and-relevance)

## <a name="select-language-when-creating-first-knowledge-base"></a>Выберите язык при создании первой базы знаний

Выбор языка является частью шагов по созданию первой базы знаний в ресурсе.

![Скриншот портала «NA Maker» по выбору языка для первой базы знаний](../media/language-support/select-language-when-creating-knowledge-base.png)

## <a name="query-matching-and-relevance"></a>Сопоставление запросов и релевантность
Для получения результатов компания «NA Maker» зависит от [анализаторов языка Когнитивного поиска Azure](https://docs.microsoft.com/rest/api/searchservice/language-support) Cognitive Search.

В то время как возможности когнитивного поиска Azure находятся на одном уровне для поддерживаемых языков, у создателя nA есть дополнительный ranker, который находится выше результатов поиска Azure. В этой модели ранкера мы используем некоторые специальные семантические и текстовые функции на следующих языках.

|Языки с дополнительным ranker|
|--|
|Китайский|
|Чешский|
|Нидерландский|
|Английский|
|Французский|
|Немецкий|
|Венгерский|
|Итальянский|
|Японский|
|Корейский|
|Польский|
|Португальский|
|Испанский|
|Шведский|

Этот дополнительный рейтинг является внутренней работой ranker создателя qnA.

## <a name="verify-language"></a>Проверить язык

Вы можете проверить язык вашего ресурса NA Maker со страницы настроек службы в nA Maker.

![Скриншот страницы настроек настройки сервиса](../media/language-support/language-knowledge-base.png)


## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Перенос базы знаний](../Tutorials/migrate-knowledge-base.md)
