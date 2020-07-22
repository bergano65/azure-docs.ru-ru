---
title: Что такое Пользовательский переводчик?
titleSuffix: Azure Cognitive Services
description: Custom Translator предоставляет те же возможности, что и Microsoft Translator Hub для систем статистического машинного перевода (SMT), но только для систем нейронного машинного перевода (NMT).
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 05/26/2020
ms.author: swmachan
ms.topic: overview
ms.openlocfilehash: d78767474150bc9571b25fe1f26135d6f41d1f20
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/05/2020
ms.locfileid: "85961240"
---
# <a name="what-is-custom-translator"></a>Что такое Пользовательский переводчик?

[Пользовательский переводчик](https://portal.customtranslator.azure.ai) — компонент службы "Переводчик", который позволяет компаниям, разработчикам приложений и поставщикам языковой службы создавать настраиваемую систему нейронного машинного перевода (NMT). Настраиваемые системы перевода легко интегрируются с существующими приложениями, рабочими процессами и веб-сайтами. 

Системы перевода, созданные с помощью [Пользовательского переводчика](https://portal.customtranslator.azure.ai), предоставляются через одну облачную [безопасную](https://cognitive.uservoice.com/knowledgebase/articles/1147537-api-and-customization-confidentiality) высокопроизводительную и высокомасштабируемую [службу "Переводчик" Azure Cognitive Services версии 3](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl), которая выполняет миллиарды переводов каждый день.

Custom Translator поддерживает более трех десятков языков и напрямую сопоставляется с языками, доступными для NMT. Полный список см. в разделе [Транслитерация](https://docs.microsoft.com/azure/cognitive-services/translator/language-support#customization).

## <a name="features"></a>Компоненты

Custom Translator предоставляет разные возможности для создания пользовательской системы перевода, а впоследствии и получения доступа к ней.

|Компонент  |Описание  |
|---------|---------|
|[Использование технологии нейронного машинного перевода](https://www.microsoft.com/translator/blog/2016/11/15/microsoft-translator-launching-neural-network-based-translations-for-all-its-speech-languages/)     |  Оптимизация перевода за счет использования нейронного машинного перевода (NMT), предоставляемого Custom Translator.       |
|[Создание систем, которые знают терминологию вашей бизнес-отрасли](what-are-parallel-documents.md)     |  Настройка и сборка систем перевода с помощью параллельных документов, которые понимают особую терминологию вашего бизнеса и отрасли.       |
|[Использование словаря для создания моделей](what-is-dictionary.md)     |   Если у вас нет набора данных для обучения, вы можете обучить модель только с использованием данных из словаря.       |
|[Совместная работа с другими пользователями](how-to-manage-settings.md#share-your-workspace)     |   Взаимодействие с другими участниками команды путем совместного использования результатов работы.     |
|[Доступ к вашей пользовательской модели перевода](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl)     |  Ваши существующие приложения и программы могут в любое время получать доступ к вашей пользовательской модели перевода с помощью Переводчика версии 3.       |

## <a name="get-better-translations"></a>Получение лучшего перевода

В службе "Переводчик" [нейронный машинный перевод (NMT)](https://www.microsoft.com/translator/blog/2016/11/15/microsoft-translator-launching-neural-network-based-translations-for-all-its-speech-languages/) появился в 2016 году. NMT предоставляет существенные преимущества в отношении качества перевода через стандартную отраслевую технологию [статистического машинного перевода (SMT)](https://en.wikipedia.org/wiki/Statistical_machine_translation). NMT лучше фиксирует контекст целого предложения перед переводом, поэтому он предоставляет более качественные и более естественные переводы с лучшими формулировками. [Custom Translator](https://portal.customtranslator.azure.ai) предоставляет нейронный машинный перевод для пользовательских моделей, что повышает конечное качество перевода.

Вы можете создать систему перевода, используя уже переведенные документы. При этом качество перевода будет выше, чем при использовании стандартных систем перевода. Пользователи могут отправлять документы в форматах ALIGN, PDF, LCL, HTML, HTM, XLF, TMX, XLIFF, TXT, DOCX и XLSX.

Custom Translator также принимает данные, которые являются параллельными на уровне документов, чтобы оптимизировать сбор и подготовку данных. Если пользователи имеют доступ к разным версиям одного содержимого на нескольких языках, представленным в отдельных документах, Custom Translator сможет автоматически сопоставить предложения между этими документами.

Если предоставляется нужный объем и правильный тип данных для обучения, зачастую [оценка BLEU](what-is-bleu-score.md) повышается на 5–10 позиций благодаря Custom Translator.

## <a name="be-productive-and-cost-effective"></a>Продуктивное и экономичное решение

При использовании [Custom Translator](https://portal.customtranslator.azure.ai) для обучения и развертывания пользовательской системы не требуется навыков программирования.

На безопасном портале [Custom Translator](https://portal.customtranslator.azure.ai) пользователи могут передать данные для обучения, обучать, тестировать системы и развернуть их в рабочей среде через интуитивно понятный пользовательский интерфейс. Система будет доступна для использования в масштабе через несколько часов (фактическое время зависит от размера данных для обучения).

К [Custom Translator](https://portal.customtranslator.azure.ai) можно также получить доступ программно через [выделенный API](https://custom-api.cognitive.microsofttranslator.com/swagger/) (в настоящее время в предварительной версии). Этот API позволяет пользователям управлять созданием и обновлением обучения на регулярной основе через собственное приложение или веб-службу.

Затраты на использование настраиваемой модели для перевода содержимого зависят от ценовой категории Переводчика. См. сведения о [ценах на Переводчика](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/) на веб-странице Cognitive Services.

## <a name="securely-translate-anytime-anywhere-on-all-your-apps-and-services"></a>Безопасный перевод в любое время и в любом месте во всех ваших приложениях и службах

Служба "Переводчик" позволяет работать с пользовательскими системами и интегрировать их в любой продукт или рабочий процесс на любом устройстве с использованием стандартной технологии REST.

## <a name="next-steps"></a>Дальнейшие действия

- Ознакомьтесь с информацией о [ценах](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/).

- В этом [кратком руководстве](quickstart-build-deploy-custom-model.md) вы узнаете, как создать модель перевода в Custom Translator.
