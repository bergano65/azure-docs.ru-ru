---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 09/13/2018
ms.author: wolfma
ms.openlocfilehash: 7a028f65467c517be59be6f0c6d2c45ef9a7685e
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/08/2018
ms.locfileid: "51285783"
---
1. Запустите Visual Studio 2017.

1. В строке меню в Visual Studio выберите **Инструменты > Get Tools** (Получить инструменты) и убедитесь, что доступна рабочая нагрузка **Разработка классических приложений .NET**. Если рабочая нагрузка не была установлена, установите флажок, а затем щелкните **Изменить**, чтобы начать установку. Скачивание и установка может занять несколько минут.

   Если теперь флажок рядом с полем **Разработка классических приложений .NET** установлен, можно закрыть диалоговое окно.

   ![Включение рабочей нагрузки "Разработка классического приложения .NET"](~/articles/cognitive-services/speech-service/media/sdk/vs-enable-net-desktop-workload.png)

1. Теперь давайте создадим проект. В строке меню выберите **Файл > Создать > Проект**. В открывшемся диалоговом окне в левой области разверните разделы **Установленные > Visual C# > Классическое приложение Windows** и выберите **Консольное приложение (.NET Framework)**. Назовите этот проект *helloworld*.

    ![Создание консольного приложения Visual C# (.NET Framework)](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-01-new-console-app.png "Create Visual C# Console App (.NET Framework)")

1. Теперь, когда проект настроен, нам нужно установить [пакет SDK для распознавания речи для NuGet](https://aka.ms/csspeech/nuget) и сослаться на него в своем коде. Найдите обозреватель решений и щелкните правой кнопкой мыши проект helloworld. В меню выберите **Управление пакетами NuGet...**

   ![Щелкните правой кнопкой мыши Управление пакетами NuGet для решения](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-02-manage-nuget-packages.png "Управление пакетами NuGet для решения")

1. В правом верхнем углу диспетчера пакетов NuGet найдите раскрывающийся список **Источник пакета** и убедитесь, что выбран вариант **nuget.org**. Затем выберите **Обзор**, выполните поиск пакета `Microsoft.CognitiveServices.Speech` и установите последнюю стабильную версию.

   ![Установка пакета NuGet Microsoft.CognitiveServices.Speech](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-03-nuget-install-1.0.0.png "Install NuGet package")

1. Примите все соглашения и лицензии для запуска установки.

   ![Принять лицензию](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-04-nuget-license.png "Принять лицензию")

    После установки пакета в консоли диспетчера пакетов появится подтверждение.

1. Далее нужно создать конфигурацию платформы, соответствующую архитектуре компьютера, который вы используете для создания и запуска консольного приложения. В строке меню выберите **Создать** > **Диспетчер конфигураций...**

    ![Запуск диспетчера конфигураций](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-05-cfg-manager-click.png "Запуск диспетчера конфигураций")

1. В диалоговом окне **Диспетчер конфигураций** найдите раскрывающийся список **Активная платформа решения** и выберите **Создать**.

    ![Добавление новой платформы в окне диспетчера конфигураций](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-06-cfg-manager-new.png "Добавление новой платформы в окне диспетчера конфигураций")

1. Если вы используете 64-разрядную версию Windows, при появлении запроса **Type or select the new platform** (Введите или выберите новую платформу) выберите `x64`. Если вы используете 32-разрядную версию Windows, выберите `x86`. Закончив, нажмите кнопку **ОК**.

    ![Добавление новой платформы с именем "64" в 64-разрядной версии Windows](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-07-cfg-manager-add-x64.png "Добавление 64-разрядной платформы")
