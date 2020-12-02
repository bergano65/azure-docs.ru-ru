---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 10/15/2020
ms.author: erhopf
ms.openlocfilehash: 3f1a24db459cabd65d9ce17b89105c3b9ab8abb7
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/26/2020
ms.locfileid: "96188494"
---
1. Запустите Visual Studio 2019.

1. Убедитесь, что рабочая нагрузка **.NET cross-platform development** (Кроссплатформенная разработка .NET) доступна. Выберите **Инструменты** > **Get Tools and Features** (Получить средства и компоненты) в строке меню Visual Studio, чтобы открыть Visual Studio Installer. Если эта рабочая нагрузка уже включена, закройте диалоговое окно.

   ![Снимок экрана Visual Studio Installer с выделенной вкладкой "Рабочие нагрузки"](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-net-core-workload.png)

   В противном случае установите флажок рядом с полем **Кроссплатформенная разработка .NET Core** и выберите **Изменить** в правом нижнем углу диалогового окна. Для установки нового компонента нужно некоторое время.

1. Создайте новое консольное приложение .NET Core Visual C#. В диалоговом окне **Создать проект** в левой области разверните **Установлено** > **Visual C#**  >  **.NET Core**. Затем выберите **Console App (.NET Core)** (Консольное приложение (.NET Core)). В качестве имени проекта введите *helloworld*.

   ![Снимок экрана: диалоговое окно нового проекта](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-dotnetcore-windows-01-new-console-app.png "Создание консольного приложения Visual C# (.NET Core)")

1. Установите и создайте ссылку на [пакет Speech SDK NuGet](https://aka.ms/csspeech/nuget). В обозревателе решений правой кнопкой мыши щелкните решение и выберите пункт **Manage NuGet Packages for Solution** (Управление пакетами NuGet для решения).

   ![Снимок экрана обозревателя решений, где выделено управление пакетами NuGet для решения](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-dotnetcore-windows-02-manage-nuget-packages.png "Управление пакетами NuGet для решения")

1. В верхнем правом углу в поле **Источник пакета** выберите **nuget.org**. Найдите пакет `Microsoft.CognitiveServices.Speech`, а затем установите его в проект **helloworld**.

   ![Снимок экрана: диалоговое окно "Управление пакетами для решения"](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-dotnetcore-windows-03-nuget-install-1.0.0.png "Установка пакета NuGet")

1. Примите условия отображаемой лицензии, чтобы начать установку пакета NuGet.

   ![Снимок экрана диалогового окна "Принятие условий лицензионного соглашения"](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-dotnetcore-windows-04-nuget-license.png "Принятие лицензии")

После установки пакета в консоли диспетчера пакетов появится подтверждение.
