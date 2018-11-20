---
title: Краткое руководство. Создание, развертывание и использование пользовательской модели в Custom Translator
titleSuffix: Azure Cognitive Services
description: В этом кратком руководстве вы выполните пошаговый процесс создания системы перевода с помощью Custom Translator.
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.component: custom-translator
ms.date: 11/13/2018
ms.author: v-rada
ms.topic: quickstart
ms.openlocfilehash: 949fb2487c3ca519c1d7022c1434d0a029ed20bb
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/14/2018
ms.locfileid: "51626582"
---
# <a name="quickstart-build-deploy-and-use-a-custom-model-for-translation"></a>Краткое руководство. Создание, развертывание и использование пользовательской модели перевода

В этой статье приведена пошаговая инструкция по созданию системы перевода с помощью Custom Translator.

## <a name="prerequisites"></a>Предварительные требования

1. Чтобы войти на портал [Custom Translator](https://portal.customtranslator.azure.ai) и использовать его, вам потребуется [учетная запись Майкрософт](https://signup.live.com) или [Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) (размещенная в Azure учетная запись организации).

2. Подписка на API перевода текстов, которую можно получить на портале Azure. Вам потребуется связать ключ подписки API перевода текстов с рабочей областью в Custom Translator. [Сведения о регистрации для использования API перевода текстов](https://docs.microsoft.com/azure/cognitive-services/translator/translator-text-how-to-signup).

3. Если у вас есть все указанные выше компоненты, войдите на портал [Custom Translator](https://portal.customtranslator.azure.ai). На портале Custom Translator перейдите на страницу параметров, где вы можете связать ключ подписки API перевода текстов Майкрософт с рабочей областью. 

## <a name="create-a-project"></a>Создание проекта

На целевой странице портала Custom Translator щелкните New Project (Новый проект). В диалоговом окне можно ввести имя нужного проекта, языковую пару и категорию, а также заполнить другие соответствующие поля. Сохраните проект. [Дополнительные сведения о создании проекта](how-to-create-project.md).

![Создание проекта](media/quickstart/ct-how-to-create-project.png)


## <a name="upload-documents"></a>Отправка документов

Затем отправьте наборы документов для [обучения](training-and-model.md#training-dataset-for-custom-translator), [настройки](training-and-model.md#tuning-dataset-for-custom-translator) и [тестирования](training-and-model.md#testing-dataset-for-custom-translator). Вы можете отправить [параллельные](what-are-parallel-documents.md) или комбинированные документы. Можно также отправить [словарь](what-is-dictionary.md).

Вы можете отправить документы со вкладки документов или со страницы конкретного проекта.

![Отправка документов](media/quickstart/ct-how-to-upload.png)

При отправке документов выберите тип документа (обучение, настройка или тестирование) и языковую пару. В случае отправки параллельных документов необходимо дополнительно указать имя документа. Дополнительные сведения см. в статье о [передаче документов](how-to-upload-document.md).

## <a name="create-a-model"></a>Создание модели

После передачи всех необходимых документов нужно переходить к созданию модели.

Выберите проект, который вы создали. Вы увидите все отправленные вами документы, языковая пара которых совпадает с настроенной для этого проекта. Выберите документы, которые необходимо включить в модель. Вы можете выбрать данные для [обучения](training-and-model.md#training-dataset-for-custom-translator), [настройки](training-and-model.md#tuning-dataset-for-custom-translator) или [тестирования](training-and-model.md#testing-dataset-for-custom-translator) или выбрать только данные для обучения и разрешить Custom Translator автоматически создать для модели наборы для настройки и тестирования.

![Создание модели](media/quickstart/ct-how-to-train.png)

Когда вы выберете нужные документы, нажмите кнопку Create Model (Создать модель) для создания модели и начала обучения. На вкладке Models (Модели) вы увидите состояние обучения и сведения обо всех обученных моделях.

[Дополнительные сведения об обучении модели](how-to-train-model.md).

## <a name="analyze-your-model"></a>Анализ модели

После успешного завершения обучения проверьте результаты. Оценка BLEU — это метрика, которая показывает качество вашего перевода. Вы также можете самостоятельно сравнить переводы, сделанные с помощью вашей пользовательской модели, с переводами, которые предоставлены в наборе тестирования, перейдя на вкладку Test (Тестирование) и выбрав System Results (Результаты системы). Проверка некоторых из этих переводов самостоятельно даст вам хорошее представление о качестве перевода, сделанного системой. Дополнительные сведения см. в статье [Просмотр результатов теста системы](how-to-view-system-test-results.md).

## <a name="deploy-a-trained-model"></a>Развертывание обученной модели

Когда вы будете готовы развернуть обученную модель, нажмите кнопку Deploy (Развернуть). У вас может быть одна развернутая модель на проект, и вы можете просмотреть состояние развертывания в столбце состояния. Дополнительные сведения см. в разделе [Развертывание модели](how-to-view-system-test-results.md#deploy-a-model)

![Развертывание обученной модели](media/quickstart/ct-how-to-deploy.png)

## <a name="use-a-deployed-model"></a>Использование развернутой модели

Доступ к развернутым моделям можно получить через [API перевода текстов Майкрософт версии 3, указав идентификатор категории](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl). Дополнительные сведения об API перевода текстов можно найти на [этой веб-странице](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference).

## <a name="next-steps"></a>Дополнительная информация

- Узнайте, как переходить между элементами в [рабочей области Custom Translator и управлять проектами](workspace-and-project.md).
