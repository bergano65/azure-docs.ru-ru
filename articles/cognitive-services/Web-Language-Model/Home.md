---
title: Общие сведения об API языковой модели на основе веб-содержимого
titleSuffix: Azure Cognitive Services
description: API языковой модели на основе веб-содержимого в Microsoft Cognitive Services предоставляет современные инструментальные средства для обработки естественного языка.
services: cognitive-services
author: piyushbehre
manager: cgronlun
ms.service: cognitive-services
ms.component: web-language-model
ms.topic: overview
ms.date: 08/12/2016
ms.author: pibehre
ROBOTS: NOINDEX
ms.openlocfilehash: 79b126fc33175b7cd6df96ab07cd7b726d6065a7
ms.sourcegitcommit: f20e43e436bfeafd333da75754cd32d405903b07
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/17/2018
ms.locfileid: "49389944"
---
# <a name="what-is-the-web-language-model-api-preview"></a>Что такое API языковой модели на основе веб-содержимого? (предварительная версия)

> [!IMPORTANT]
> Поддержка предварительной версии службы "Языковая модель на основе веб-содержимого" прекращена 9 августа 2018 г. Мы рекомендуем использовать [модули текстовой аналитики Машинного обучения Azure](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/text-analytics) для анализа и обработки текста.

API языковой модели на основе веб-содержимого Microsoft — это облачная служба на основе REST, которая предоставляет современные инструментальные средства для обработки естественного языка. Этот API позволяет вашему приложению использовать потенциал больших данных с помощью языковых моделей, обученных с помощью веб-данных, собранных Bing на рынке en-US (английский, США).

Эти сглаженные вероятностные языковые модели с использованием N-граммы, которые поддерживают до 5 шагов цепи Маркова, обучены на основании следующих единиц данных:

- основного текста веб-страницы;
- заголовка веб-страницы;
- текста привязки веб-страницы;
- текста запроса веб-поиска.

API веб-модели языка поддерживают четыре операции поиска.

1. Совместная (log10) вероятность последовательности слов.
2. Условная (log10) вероятность одного слова в заданной последовательности предыдущих слов.
3. Список слов (завершений), скорее всего, для того чтобы следовать определенной последовательности слов.
4. Разбиение слов строк, которые не содержат пробелы.

## <a name="getting-started"></a>Приступая к работе

1. Оформите подписку на службу.
2. Скачайте [пакет SDK](https://www.github.com/microsoft/cognitive-weblm-windows).
3. Запустите пример кода пакета SDK.
4. Подробные сведения о конечных точках, включая фрагменты кода в разных языках, см. в [Справочнике по API](https://westus.dev.cognitive.microsoft.com/docs/services/55de9ca4e597ed1fd4e2f104).

## <a name="underlying-technology"></a>Основная технология

Следующий документ содержит сведения о разработке эти языковых моделей и на него следует ссылаться в научных публикациях, которые используют эту услугу.

- [An Overview of Microsoft Web N-gram Corpus and Applications](http://research.microsoft.com/apps/pubs/default.aspx?id=130762) (Обзор веб-данных N-граммы и приложений Microsoft), NAACL-HLT 2010

Нажмите [здесь](https://academic.microsoft.com/#/search?iq=And%28Ty%3D'0'%2CRId%3D2145833060%29&q=papers%20citing%20an%20overview%20of%20microsoft%20web%20n%20gram%20corpus%20and%20applications&filters=&from=0&sort=0), чтобы увидеть текущий список документов, ссылающихся на этот текст.
