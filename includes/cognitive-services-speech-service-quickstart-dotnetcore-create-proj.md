---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 12/12/2018
ms.author: erhopf
ms.openlocfilehash: 6e49db90aa9e9f933a190425afbafd15e0057fca
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/23/2019
ms.locfileid: "66146063"
---
1. Запустите Visual Studio 2017.

1. Убедитесь, что рабочая нагрузка **.NET cross-platform development** (Кроссплатформенная разработка .NET) доступна. Выберите **Tools**(Средства) > **Get Tools and Features** (Получить средства и компоненты) в строке меню Visual Studio, чтобы открыть установщик Visual Studio. Если эта рабочая нагрузка уже включена, закройте диалоговое окно.

   ![Снимок экрана Visual Studio Installer с выделенной вкладкой "Рабочие нагрузки"](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-net-core-workload.png)

   В противном случае установите флажок рядом с полем **Кроссплатформенная разработка .NET Core** и выберите **Изменить** в правом нижнем углу диалогового окна. Для установки нового компонента нужно некоторое время.

1. Создайте новое консольное приложение .NET Core Visual C#. В диалоговом окне **Создать проект** в левой области разверните **Установлено** > **Visual C#** > **.NET Core**. Затем выберите **Console App (.NET Core)** (Консольное приложение (.NET Core)). В качестве имени проекта введите *helloworld*.

   ![Снимок экрана диалогового окна нового проекта](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-dotnetcore-windows-01-new-console-app.png "Создание консольного приложения Visual C# (.NET Core)")

1. Установите и создайте ссылку на [пакет Speech SDK NuGet](https://aka.ms/csspeech/nuget). В обозревателе решений правой кнопкой мыши щелкните решение и выберите пункт **Manage NuGet Packages for Solution** (Управление пакетами NuGet для решения).

   ![Снимок экрана обозревателя решений, где выделен параметр "Управление пакетами NuGet для решения"](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-dotnetcore-windows-02-manage-nuget-packages.png "Управление пакетами NuGet для решения")

1. В верхнем правом углу в поле **Источник пакета** выберите **nuget.org**. Найдите пакет `Microsoft.CognitiveServices.Speech`, а затем установите его в проект **helloworld**.

   ![Снимок экрана диалогового окна управления пакетами для решения](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-dotnetcore-windows-03-nuget-install-1.0.0.png "Установка пакета NuGet")

1. Примите условия отображаемой лицензии, чтобы начать установку пакета NuGet.

   ![Снимок экрана диалогового окна принятия условий лицензионного соглашения](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-dotnetcore-windows-04-nuget-license.png "Принять условия лицензионного соглашения")

После установки пакета в консоли диспетчера пакетов появится подтверждение.
