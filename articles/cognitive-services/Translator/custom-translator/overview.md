---
title: Что такое Custom Translator?
titleSuffix: Azure Cognitive Services
description: Custom Translator предоставляет те же возможности, что и Microsoft Translator Hub для систем статистического машинного перевода (SMT), но только для систем нейронного машинного перевода (NMT).
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.component: custom-translator
ms.date: 11/13/2018
ms.author: v-rada
ms.topic: overview
ms.openlocfilehash: 76e6bc006ff6049b631409a3515628fbd169f713
ms.sourcegitcommit: 8314421d78cd83b2e7d86f128bde94857134d8e1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/19/2018
ms.locfileid: "51976984"
---
# <a name="what-is-custom-translator-preview"></a>Что такое Custom Translator (предварительная версия)?

[Custom Translator](https://portal.customtranslator.azure.ai) — компонент службы Microsoft Translator, который позволяет переводческим компаниям, разработчикам приложений и поставщикам языковой службы создавать настраиваемую систему нейронного машинного перевода (NMT). Настраиваемые системы перевода легко интегрируются с существующими приложениями, рабочими процессами и веб-сайтами. [Custom Translator](https://portal.customtranslator.azure.ai/) предоставляет те же возможности, что и [Microsoft Translator Hub](https://hub.microsofttranslator.com/) для систем статистического машинного перевода (SMT), но только для систем нейронного машинного перевода (NMT).

Системы перевода, созданные с использованием [Custom Translator](https://portal.customtranslator.azure.ai), доступны через один облачный [безопасный](https://cognitive.uservoice.com/knowledgebase/articles/1147537-api-and-customization-confidentiality) высокопроизводительный и высокомасштабируемый [API перевода текстов Майкрософт версии 3](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl), который выполняет миллиарды переводов каждый день. 

Custom Translator поддерживает более трех десятков языков и напрямую сопоставляется с языками, доступными для NMT. Полный список см. в статье [Поддержка языков и регионов в API перевода текстов]( https://docs.microsoft.com/azure/cognitive-services/translator/language-support).

## <a name="features"></a>Функции

Custom Translator предоставляет разные возможности для создания пользовательской системы перевода, а впоследствии и получения доступа к ней.

|Функция  |ОПИСАНИЕ  |
|---------|---------|
|[Использование технологии нейронного машинного перевода](https://blogs.msdn.microsoft.com/translation/2016/11/15/microsoft-translator-launching-neural-network-based-translations-for-all-its-speech-languages/)     |  Оптимизация перевода за счет использования нейронного машинного перевода (NMT), предоставляемого Custom Translator.       |
|[Создание систем, которые знают терминологию вашей бизнес-отрасли](what-are-parallel-documents.md)     |  Настройка и сборка систем перевода с помощью параллельных документов, которые понимают особую терминологию вашего бизнеса и отрасли.       |
|[Использование словаря для создания моделей](what-is-dictionary.md)     |   Если у вас нет набора данных для обучения, вы можете обучить модель только с использованием данных из словаря.       |
|[Совместная работа с другими пользователями](how-to-manage-settings.md#share-your-workspace)     |   Взаимодействие с другими участниками команды путем совместного использования результатов работы.     |
|[Доступ к вашей пользовательской модели перевода](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl)     |  Ваши существующие приложения или программы могут в любое время получать доступ к вашей пользовательской модели перевода с помощью API перевода текстов Майкрософт версии 3.       |

## <a name="get-better-translations"></a>Получение лучшего перевода

В службе Microsoft Translator [нейронный машинный перевод (NMT)](https://blogs.msdn.microsoft.com/translation/2016/11/15/microsoft-translator-launching-neural-network-based-translations-for-all-its-speech-languages/) появился в 2016 году. NMT предоставляет существенные преимущества в отношении качества перевода через стандартную отраслевую технологию [статистического машинного перевода (SMT)](https://en.wikipedia.org/wiki/Statistical_machine_translation). NMT лучше фиксирует контекст целого предложения перед переводом, поэтому он предоставляет более качественные и более естественные переводы с лучшими формулировками. [Custom Translator](https://portal.customtranslator.azure.ai) предоставляет нейронный машинный перевод для пользовательских моделей, что повышает конечное качество перевода.

Вы можете создать систему перевода, используя уже переведенные документы. В этих документах лучше представлена терминология и стиль вашей отрасли, чем в универсальной системе перевода. Пользователи могут отправлять документы в форматах ALIGN, PDF, LCL, HTML, HTM, XLF, TMX, XLIFF, TXT, DOCX и XLSX.

Custom Translator также принимает данные, которые являются параллельными на уровне документов, чтобы оптимизировать сбор и подготовку данных. Если пользователи имеют доступ к разным версиям одного содержимого на нескольких языках, представленным в отдельных документах, Custom Translator сможет автоматически сопоставить предложения между этими документами.

Если предоставляется нужный объем и правильный тип данных для обучения, зачастую [оценка BLEU](what-is-bleu-score.md) повышается на 5–10 позиций благодаря Custom Translator.

## <a name="be-productive-and-cost-effective"></a>Продуктивное и экономичное решение

При использовании [Custom Translator](https://portal.customtranslator.azure.ai) для обучения и развертывания пользовательской системы не требуется навыков программирования. 

На безопасном портале [Custom Translator](https://portal.customtranslator.azure.ai) пользователи могут передать данные для обучения, обучать, тестировать системы и развернуть их в рабочей среде через интуитивно понятный пользовательский интерфейс. Система будет доступна для использования в масштабе через несколько часов (фактическое время зависит от размера данных для обучения).

К [Custom Translator](https://portal.customtranslator.azure.ai) можно также получить доступ программно через [выделенный API](https://custom-api.cognitive.microsofttranslator.com/swagger/) (в настоящее время в предварительной версии). Этот API позволяет пользователям управлять созданием и обновлением обучения на регулярной основе через собственное приложение или веб-службу.

Затраты на использование пользовательской модели для перевода содержимого зависят от ценовой категории API перевода текстов пользователя. Цены на Cognitive Services для API перевода текстов см. [здесь](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/).

## <a name="securely-translate-anytime-anywhere-on-all-your-apps-and-services"></a>Безопасный перевод в любое время и в любом месте во всех ваших приложениях и службах

API перевода текстов Майкрософт позволяет легко использовать пользовательские системы перевода и интегрировать их в любой продукт или рабочий процесс на любом устройстве через стандартную технологию REST.

## <a name="next-steps"></a>Дополнительная информация

- Ознакомьтесь с информацией о [ценах](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/).

- В этом [кратком руководстве](quickstart-build-deploy-custom-model.md) вы узнаете, как создать модель перевода в Custom Translator.
