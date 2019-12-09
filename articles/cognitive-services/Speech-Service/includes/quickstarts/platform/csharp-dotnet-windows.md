---
title: Краткое руководство. Настройка платформы (Windows) для пакета SDK службы "Речь" для .NET Framework — служба "Речь"
titleSuffix: Azure Cognitive Services
description: В этом руководстве показано, как настроить платформу для приложения .NET Framework на C# для Windows с пакетом SDK службы "Речь".
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 10/10/2019
ms.author: erhopf
ms.openlocfilehash: a858a078f8e22a7176fc0eeb09ae0133e2ea11a4
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/04/2019
ms.locfileid: "74818552"
---
а также как установить [пакет SDK для службы "Речь"](~/articles/cognitive-services/speech-service/speech-sdk.md) для .NET Framework (Windows).

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим кратким руководством вам понадобится:

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)

## <a name="create-a-visual-studio-project-and-install-the-speech-sdk"></a>Создание проекта Visual Studio и установка пакета SDK для службы "Речь"

Вам необходимо установить [пакет SDK NuGet для службы "Речь"](https://aka.ms/csspeech/nuget), чтобы вы могли ссылаться на него в коде. Для этого может потребоваться сначала создать проект **helloworld**. Если у вас уже есть проект с рабочей нагрузкой **Разработка классических приложений .NET**, можно использовать этот проект и перейти к разделу [Установка пакета SDK для службы "Речь" с помощью диспетчера пакетов NuGet](#use-nuget-package-manager-to-install-the-speech-sdk).

### <a name="create-helloworld-project"></a>Создание проекта helloworld

1. Откройте Visual Studio 2019.

1. В окне "Период запуска" выберите **Создать новый проект**. 

1. В окне **Создание проекта** выберите **Консольное приложение (.NET Framework)** и нажмите кнопку **Далее**.

1. В окне **Настройка нового проекта** введите *helloworld* в **Имя проекта**, выберите или создайте путь каталога в **Расположение**, а затем выберите **Создать**.

1. В строке меню Visual Studio выберите **Инструменты** > **Get Tools and Features** (Получить инструменты и компоненты), открывающие Visual Studio Installer и показывающие диалоговое окно **Идет изменение**.

1. Проверьте, доступна ли рабочая нагрузка **разработки классического приложения .NET**. Если рабочая нагрузка не была установлена, установите флажок возле нее, а затем выберите **Изменить**, чтобы начать установку. Скачивание и установка может занять несколько минут.

   Если флажок рядом с **Разработка классических приложений .NET** уже установлен, выберите **Закрыть**, чтобы выйти из диалогового окна.

   ![Включение рабочей нагрузки "Разработка классического приложения .NET"](~/articles/cognitive-services/speech-service/media/sdk/vs-enable-net-desktop-workload.png)

1. Закройте Visual Studio Installer.

### <a name="use-nuget-package-manager-to-install-the-speech-sdk"></a>Установка пакета SDK для службы "Речь" с помощью диспетчера пакетов NuGet

1. В Обозревателе решений щелкните правой кнопкой мыши на проект **helloworld** и выберите **Управление пакетами NuGet**, чтобы отобразить Диспетчер пакетов NuGet.

   ![Диспетчер пакетов NuGet](~/articles/cognitive-services/speech-service/media/sdk/vs-nuget-package-manager.png)

1. В правом верхнем углу найдите раскрывающийся список **Источник пакета** и убедитесь, что выбран вариант **`nuget.org`** .

1. В левом верхнем углу нажмите кнопку **Просмотреть**.

1. В поле поиска введите *Microsoft.CognitiveServices.Speech* и выберите **Ввести**.

1. В результатах поиска выберите пакет **Microsoft.CognitiveServices.Speech**, а затем выберите **Установить** для установки последней стабильной версии.

   ![Установка пакета Microsoft.CognitiveServices.Speech NuGet](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-03-nuget-install-1.0.0.png)

1. Примите все соглашения и лицензии для запуска установки.

   После установки пакета на **Консоли диспетчера пакетов** появится подтверждение.

Теперь можно перейти к разделу [Дополнительная информация](#next-steps) ниже.

## <a name="next-steps"></a>Дополнительная информация

[!INCLUDE [windows](../quickstart-list.md)]
