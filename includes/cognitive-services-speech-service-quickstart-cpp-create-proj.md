---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 2/20/2019
ms.author: erhopf
ms.openlocfilehash: af61d975b540787b9b54d9fdea66773c10aeb6ac
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/23/2019
ms.locfileid: "66145448"
---
1. Запустите Visual Studio 2017.

1. Убедитесь, что рабочая нагрузка **Desktop development with C++** (Разработка классических приложений на C++) доступна. Выберите **Tools**(Средства) > **Get Tools and Features** (Получить средства и компоненты) в строке меню Visual Studio, чтобы открыть установщик Visual Studio. Если эта рабочая нагрузка уже включена, перейдите к следующему шагу.

    ![Снимок экрана, где отображается вкладка "Рабочие нагрузки" Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-cpp-workload.png)

    В противном случае поставьте флажок рядом с **Desktop development with C++** (Разработка классических приложений на C++).

1. Убедитесь, что компонент **Диспетчер пакетов NuGet** доступен. Переключитесь на вкладку **Отдельные компоненты** диалогового окна установщика Visual Studio и выберите **Диспетчер пакетов NuGet**, если он еще не включен.

      ![Снимок экрана вкладки "Отдельные компоненты" Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-nuget-package-manager.png)

1. При необходимости включить рабочую нагрузку C++ или NuGet выберите **Изменить** (в правом нижнем углу диалогового окна). Для установки новых компонентов нужно некоторое время. Если обе функции уже были включены, закройте диалоговое окно.

1. Создайте новое консольное классическое приложение Visual C++ для Windows. Сначала из меню выберите **Файл** > **Создать** > **Проект**. В диалоговом окне **Создать проект** в левой области разверните **Установлено** > **Visual C++** > **Классическое приложение Windows**. Затем выберите **Консольное приложение Windows**. В качестве имени проекта введите *helloworld*.

    ![Снимок экрана: диалоговое окно нового проекта](../articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-01-new-console-app.png)

1. Если вы используете 64-разрядную Windows, можно переключиться на платформу сборки `x64` с помощью раскрывающегося меню на панели инструментов Visual Studio. (64-разрядные версии Windows могут запускать 32-разрядные приложения, так что это не обязательно.)

    ![Снимок экрана панели инструментов Visual Studio с выделенным параметром x64](../articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-02-switch-to-x64.png)

1. В обозревателе решений правой кнопкой мыши щелкните решение и выберите пункт **Manage NuGet Packages for Solution** (Управление пакетами NuGet для решения).

    ![Снимок экрана обозревателя решений, где выделен параметр "Управление пакетами NuGet для решения"](../articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-03-manage-nuget-packages.png)

1. В верхнем правом углу в поле **Источник пакета** выберите **nuget.org**. Найдите пакет `Microsoft.CognitiveServices.Speech`, а затем установите его в проект **helloworld**.

    ![Снимок экрана диалогового окна "Управление пакетами для решения"](../articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-04-nuget-install-1.0.0.png)

    > [!NOTE]
    > Текущая версия пакета SDK для распознавания речи для Cognitive Services — `1.5.0`.

1. Примите условия отображаемой лицензии, чтобы начать установку пакета NuGet.

    ![Снимок экрана диалогового окна принятия условий лицензионного соглашения](../articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-05-nuget-license.png)

После установки пакета в консоли диспетчера пакетов появится подтверждение.
