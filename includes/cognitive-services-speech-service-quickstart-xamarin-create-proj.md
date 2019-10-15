---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 09/13/2019
ms.author: erhopf
ms.openlocfilehash: 0e4e67710c98b80dce2b0d55a86869625f3942d2
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/03/2019
ms.locfileid: "71837450"
---
Чтобы создать проект Visual Studio для разработки кроссплатформенного мобильного приложения .NET с помощью Xamarin необходимо настроить параметры разработки Visual Studio, создать проект, выбрать целевую архитектуру и установить пакет SDK службы "Речь".

### <a name="set-up-visual-studio-development-options"></a>Настройка параметров разработки Visual Studio

Для начала убедитесь, что вы правильно настроили Visual Studio для кросс-платформенной разработки мобильных приложений с помощью .NET:

1. Откройте Visual Studio 2019.

1. В строке меню Visual Studio выберите **Инструменты** > **Get Tools and Features** (Получить инструменты и компоненты), чтобы открыть Visual Studio Installer и просмотреть диалоговое окно **Идет изменение**.

   ![Вкладка "Рабочие нагрузки", диалоговое окно "Идет изменение", Visual Studio Installer](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-xamarin-workload.png)

1. На вкладке **Загрузки**  в **Windows** найдите рабочую нагрузку **Разработка мобильных приложений с помощью .NET**. Если флажок рядом с этой рабочей нагрузкой уже выбран, закройте диалоговое окно **Идет изменение** и перейдите к шагу 5.

1. Установите флажок **Mobile Development with .NET** (Разработка мобильного приложения с помощью .NET), выберите **Modify** (Изменить), а затем в диалоговом окне **Before we get started** (Перед началом) выберите **Далее**, чтобы установить рабочую нагрузку разработки мобильного приложения с помощью .NET. Установка нового компонента может занять некоторое время.

1. Закройте Visual Studio Installer.

### <a name="create-the-project"></a>Создание проекта

1. В строке меню Visual Studio выберите **Файл** > **Создать** > **Проект**, чтобы открыть окно **Создание проекта**.

   ![Создание проекта Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-xamarin-create-new-project.png)

1. Найдите и выберите **Mobile app (Xamarin Forms)** (Мобильное приложение (Формы Xamarin)).

1. Выберите **Далее**, чтобы отобразить экран **Настроить новый проект**. 

   ![Настройка нового проекта Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-xamarin-configure-your-new-project.png)

1. В поле **Имя проекта** введите `helloworld`.

1. В поле **Расположение** перейдите и выберите или создайте папку для сохранения проекта.

1. Выберите **Создать**, чтобы открыть окно **New Mobile App Xamarin Forms Project** (Новый проект мобильного приложения форм Xamarin).

   ![Диалоговое окно "Новый проект приложения для универсальной платформы Windows" Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-xamarin-new-xamarin-project.png)

1. Выберите **Пустой** шаблон

1. В разделе **Платформа** установите флажки **Android**, **iOS** и **Windows (UWP)** .

1. Нажмите кнопку **ОК**. Вы возвращаетесь в интегрированную среду разработки Visual Studio. Проект создан и отображается в области **Обозреватель решений**.

   ![Проект helloworld Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-xamarin-helloworld.png)

Теперь выберите архитектуру целевой платформы и проект запуска. На панели инструментов Visual Studio найдите раскрывающийся список **Платформы решения**. (Если вы не видите его, выберите **Просмотр** > **Панели инструментов** > **Стандартная**, чтобы отобразить панель инструментов с элементом **Платформы решения**.) Если вы используете 64-битную версию Windows, выберите **x64** в раскрывающемся списке. В 64-битной версии Windows также можно запускать 32-битные приложения, поэтому при желании можно выбрать вариант **x86**. В раскрывающемся списке **Start-up Projects** (Проекты запуска) выберите helloworld.UWP (универсальные приложения для Windows).

### <a name="install-the-speech-sdk"></a>Установка пакета SDK службы "Речь"

Установите [пакет NuGet SDK службы "Речь"](https://aka.ms/csspeech/nuget) и сделайте на него ссылку в своем проекте:

1. В **Обозревателе решений** правой кнопкой мыши щелкните решение и выберите пункт **Manage NuGet Packages for Solution** (Управление пакетами NuGet для решения) для перехода в окно **NuGet — решение**.

1. Щелкните **Обзор**.

   ![Снимок экрана диалогового окна "Управление пакетами для решения"](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-nuget-solution-browse.png)

1. В списке **Источник пакета** выберите **nuget.org**.

1. В поле **Поиска** введите`Microsoft.CognitiveServices.Speech`, а затем выберите этот пакет после появления в результатах поиска.

   ![Снимок экрана диалогового окна "Управление пакетами для решения"](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-xamarin-nuget-install.png)
   > Примечание. Для библиотеки iOS в пакете NuGet Microsoft.CognitiveServices.Speech не включен bitcode. Если для приложения требуется библиотека с поддержкой bitcode, используйте пакет NuGet Microsoft.CognitiveServices.Speech.Xamarin.iOS для проекта iOS.

1. В области "Состояние пакета" рядом с результатами поиска выберите все проекты. **helloworld**, **helloworld.Android**, **helloworld.iOS** и **helloworld.UWP**.

1. Щелкните **Установить**.

1. В диалоговом окне **Просмотр изменений** нажмите **ОК**.

1. В диалоговом окне **Принятие лицензии** просмотрите лицензию, а затем выберите **Я принимаю** и установите ссылку на пакет SDK для распознавания речи для всех проектов. После успешного завершения установки может отобразиться следующее предупреждение для helloworld.iOS. Это известная проблема, которая не должна повлиять на функциональность приложения.

> Не удалось разрешить ссылку "C:\Users\Default\.nuget\packages\microsoft.cognitiveservices.speech\1.7.0\build\Xamarin.iOS\libMicrosoft.CognitiveServices.Speech.core.a". Если эта ссылка требуется для кода, могут возникнуть ошибки при компиляции.
