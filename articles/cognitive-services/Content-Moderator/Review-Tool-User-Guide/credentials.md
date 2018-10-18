---
title: Учетные данные в Azure Content Moderator | Документация Майкрософт
description: Управление учетными данными Content Moderator, используемыми с интерфейсами API.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 06/25/2017
ms.author: sajagtap
ms.openlocfilehash: 6477879953dc2bb2c7503eb0b2d4b5effa7b6a11
ms.sourcegitcommit: d211f1d24c669b459a3910761b5cacb4b4f46ac9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/06/2018
ms.locfileid: "44024661"
---
# <a name="manage-credentials"></a>Управление учетными данными

Учетные данные Content Moderator можно создать с помощью следующих средств:

- [Портал Azure](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator)
- [Инструмент проверки Content Moderator](http://contentmoderator.cognitive.microsoft.com/)

В этой статье объясняется, где их можно найти и как они связаны между собой.

## <a name="the-azure-portal"></a>Портал Azure

На панели мониторинга портала Azure выберите свою учетную запись Content Moderator. В разделе **Управление ресурсами** выберите **Ключи**. Чтобы скопировать ключ, щелкните значок справа от ключа.

![Ключи Content Moderator на портале Azure](images/credentials-azure-portal-keys.PNG)

### <a name="use-the-azure-account-with-the-review-tool-and-review-api"></a>Использование учетной записи Azure с инструментом проверки и API проверки
Чтобы использовать свой ключ Azure с интерфейсами API проверки, скопируйте идентификатор ресурса, указанный на экране **Свойства** на приведенном ниже снимке экрана. Затем введите его на экране учетных данных инструмента проверки в поля **Whitelisted Resource Id(s)** (Разрешенные идентификаторы ресурсов), как показано в следующем разделе **Идентификатор ресурса**. 

> [!NOTE]
> Регион подписки Content Moderator должен совпадать с регионом команды проверки, чтобы обеспечить распознавание команды и доступ к ее данным. Например, в изображениях на этой странице регион **Западная часть США** **(4)** содержит подписку Azure для Content Moderator и вашу команду проверки.
>
> После ввода в инструмент проверки ключа и идентификатора ресурса из подписки Azure ключ **Ocp-Apim-Subscription-Key** для пробной версии, отображенный на экране "Credentials" (Учетные данные), больше не используется, но всегда будет доступен.
> Ключ пробной версии позволяет выполнить до 5000 транзакций в месяц с частотой в 1 запрос в секунду.

![Идентификатор ресурса Content Moderator на портале Azure](images/credentials-azure-portal-resourceid.PNG)

### <a name="use-the-azure-account-with-the-workflows-in-the-review-tool"></a>Использование учетной записи Azure с рабочими процессами в инструменте проверки

Чтобы использовать свой ключ Azure для рабочих процессов, доступных в Content Moderator, введите его в поле **Ocp-Apim-Subscription-Key** в разделе **Workflow Settings** (Параметры рабочего процесса), как показано в разделе **Рабочие процессы** ниже. Щелкните **+**, чтобы сохранить свой идентификатор ресурса.

![Учетные данные рабочего процесса Content Moderator в инструменте проверки](images/credentials-workflow.PNG)

## <a name="the-review-tool"></a>Инструмент проверки

На панели мониторинга инструмента проверки на вкладке **Settings** (Параметры) выберите **Credentials** (Учетные данные).

![Учетные данные Content Moderator в инструменте проверки](images/credentials-trial-resource-workflow.PNG)

В следующем разделе более подробно рассматривается приведенное выше изображение.

### <a name="api"></a>API

В первой части указаны **конечная точка API проверки**, **идентификатор команды** и **Ocp-Apim-Subscription-Key** (ключ пробной версии Content Moderator, созданный вместе с командой проверки). Используйте их для вызова всех интерфейсов API Content Moderator, включая API проверки.

Также запишите свой идентификатор региона для конечной точки API. Например, **westus** — регион в https://westus.api.cognitive.microsoft.com/contentmoderator/review/v1.0.

![Ключ Content Moderator в инструменте проверки](images/credentials-trialkey.PNG)

### <a name="resource-id"></a>Идентификатор ресурса

Эта тема рассматривается в разделе [Использование учетной записи Azure с инструментом проверки и API проверки](credentials.md#how-to-use-your-azure-account-with-the-review-tool). Это поле обычно пусто, если вы не добавите в него идентификатор ресурса Azure, как описано в предыдущем разделе.

### <a name="workflows"></a>Рабочие процессы

Мы рассматривали этот набор полей в предыдущем разделе о [выполнении рабочих процессов с помощью ключа Azure](credentials.md#use-the-azure-account-with-the-workflows-in-the-review-tool). По умолчанию инструмент проверки использует для выполнения рабочих процессов автоматически созданный ключ пробной версии, который отображается в самом начале. С помощью других двух полей можно использовать списки терминов и изображений для операций проверки текста и оценки изображений соответственно.

## <a name="next-steps"></a>Дополнительная информация

* Узнайте, как использовать учетные данные Content Moderator в своих [рабочих процессах](workflows.md).
