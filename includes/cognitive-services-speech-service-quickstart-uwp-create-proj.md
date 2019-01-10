---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 12/13/2018
ms.author: erhopf
ms.openlocfilehash: 79fbe2db1ec9758d1e15ba7d89363429415918c7
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/21/2018
ms.locfileid: "53729481"
---
1. Запустите Visual Studio 2017.

1. Убедитесь, что рабочая нагрузка **Разработка приложений для универсальной платформы Windows** доступна. Выберите **Инструменты** > **Get Tools and Features** (Получить средства и компоненты) в строке меню Visual Studio, чтобы открыть Visual Studio Installer. Если эта рабочая нагрузка уже включена, закройте диалоговое окно.

    ![Снимок экрана Visual Studio Installer с выделенной вкладкой "Рабочие нагрузки"](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-workload.png)

    В противном случае установите флажок рядом с полем **Кроссплатформенная разработка .NET** и выберите **Изменить** в правом нижнем углу диалогового окна. Для установки этого нового компонента нужно некоторое время.

1. Создайте пустое универсальное приложение Visual C# для Windows. Сначала из меню выберите **Файл** > **Создать** > **Проект**. В диалоговом окне **Создать проект** в области слева разверните элементы **Установлено** > **Visual C#** > **Универсальное приложение Windows**. Затем выберите **Пустое приложение (универсальное приложение Windows)**. В качестве имени проекта введите *helloworld*.

    ![Снимок экрана: диалоговое окно нового проекта](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-01-new-blank-app.png)

1. Для пакета SDK службы "Речь" требуется, чтобы ваше приложение было создано для Windows 10 Fall Creators Update или более поздней версии. В появившемся окне **Новый проект приложения для универсальной платформы Windows** выберите значение **Windows 10 Fall Creators Update (10.0; сборка 16299)** для параметра **Минимальная версия**. В окне **Целевая версия** выберите ту же или более позднюю версию, после чего нажмите кнопку **ОК**.

    ![Снимок экрана окна нового проекта приложения для универсальной платформы Windows](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-02-new-uwp-project.png)

1. Если вы используете 64-разрядную версию Windows, можно переключиться на платформу сборки `x64` с помощью раскрывающегося меню на панели инструментов Visual Studio. (64-разрядная версия Windows может запускать 32-разрядные приложения, поэтому при необходимости вы можете оставить значение `x86`.)

   ![Снимок экрана с панелью инструментов Visual Studio, на которой выделено значение "x64"](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-03-switch-to-x64.png)

   > [!NOTE]
   > Пакет SDK для службы "Речь" поддерживает только Intel-совместимые процессоры. В настоящее время ARM не поддерживается.

1. Установите и создайте ссылку на [пакет Speech SDK NuGet](https://aka.ms/csspeech/nuget). В обозревателе решений щелкните правой кнопкой мыши решение и выберите пункт **Управление пакетами NuGet для решения**.

    ![Снимок экрана обозревателя решений, где выделено управление пакетами NuGet для решения](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-04-manage-nuget-packages.png)

1. В верхнем правом углу в поле **Источник пакета** выберите **nuget.org**. Найдите пакет `Microsoft.CognitiveServices.Speech`, а затем установите его в проект **helloworld**.

    ![Снимок экрана диалогового окна управления пакетами для решения](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-05-nuget-install-1.0.0.png "Установка пакета NuGet")

1. Примите условия отображаемой лицензии, чтобы начать установку пакета NuGet.

    ![Снимок экрана диалогового окна принятия условий лицензионного соглашения](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-06-nuget-license.png "Принять условия лицензии")

1. В консоли диспетчера пакетов отобразятся следующие выходные данные.

   ```text
   Successfully installed 'Microsoft.CognitiveServices.Speech 1.2.0' to helloworld
   ```

1. Поскольку приложение использует микрофон для ввода речи, добавьте возможность **Микрофон** к проекту. В обозревателе решений дважды щелкните **Package.appxmanifest**, чтобы изменить манифест приложения. Затем перейдите на вкладку **Возможности**, установите флажок для возможности **Микрофон** и сохраните изменения.

   ![Снимок экрана манифеста приложения Visual Studio с выделенными вкладкой "Возможности" и пунктом "Микрофон"](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-07-capabilities.png)
