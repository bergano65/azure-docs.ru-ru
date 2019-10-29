---
author: trrwilson
ms.service: cognitive-services
ms.topic: include
ms.date: 5/23/2019
ms.author: travisw
ms.openlocfilehash: 5c0b6400c6dc346597329c82e82801403a888a4d
ms.sourcegitcommit: 9a4296c56beca63430fcc8f92e453b2ab068cc62
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2019
ms.locfileid: "72675562"
---
1. Запустите Android Studio и в окне **приветствия** выберите **Start a new Android Studio project** (Создать проект Android Studio).

    ![Снимок экрана окна приветствия Android Studio](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-01-start-new-android-studio-project.png)

1. Откроется окно мастера **Выбор проекта**. Выберите **Phone and Tablet** (Телефон и планшет) и **Empty Activity** (Пустое действие) в поле выбора действия. Щелкните **Далее**.

   ![Снимок экрана мастера Choose your project (Выбор проекта)](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-02-target-android-devices.png)

1. На экране **Configure your project** (Настройка проекта) введите *Quickstart* (Быстрое начало) в поле **Name** (Имя), *samples.speech.cognitiveservices.microsoft.com* в поле **Package name** (Имя пакета). Затем выберите каталог проекта. Для параметра **Minimum API level** (Минимальный уровень API) задайте значение **API 23: Android 6.0 (Marshmallow)** . Оставьте остальные флажки снятыми и выберите **Готово**.

   ![Снимок экрана мастера Configure your project (Настройка проекта)](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-03-create-android-project.png)

Android Studio требуется несколько минут, чтобы подготовить новый проект Android. Затем настройте для проекта пакет SDK для службы "Речь" Azure Cognitive Services и использование Java 8.

[!INCLUDE [License notice](cognitive-services-speech-service-license-notice.md)]

Текущая версия пакета SDK для службы "Речь" Cognitive Services — 1.7.0.

Пакет SDK службы "Речь" для Android входит в состав [AAR (библиотека Android)](https://developer.android.com/studio/projects/android-library), которая содержит необходимые библиотеки и требуемые разрешения Android.
Он доступен в репозитории Maven по адресу https:\//csspeechstorage.blob.core.windows.net/maven/.

Настройте проект для использования пакета SDK службы "Речь". Откройте окно **Project Structure** (Структура проекта), выбрав в строке меню Android Studio **Файл** > **Project Structure** (Структура проекта). В окне **Project Structure** (Структура проекта) внесите следующие изменения:

1. В списке в левой части окна выберите **Project** (Проект). Измените параметры **Default Library Repository** (Репозиторий библиотек по умолчанию), добавив запятую и URL-адрес репозитория Maven в одиночных кавычках: 'https:\//csspeechstorage.blob.core.windows.net/maven/'

   ![Снимок экрана окна Project Structure (Структура проекта)](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-06-add-maven-repository.png)

1. На этом же экране слева выберите **app** (приложение). Затем в верхней части окна выберите вкладку **Зависимости**. Нажмите зеленый знак плюс ( **+** ) и выберите в раскрывающемся меню **Library dependency** (Зависимость библиотек).

   ![Снимок экрана зависимости библиотек](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-07-add-module-dependency.png)

1. В появившемся окне введите имя и версию пакета SDK службы "Речь" для Android, *com.microsoft.cognitiveservices.speech:client-sdk:1.7.0*. Нажмите кнопку **ОК**.
   Пакет SDK службы "Речь" должен быть добавлен в список зависимостей, как показано ниже:

   ![Снимок экрана пакета SDK службы "Речь" в списке зависимостей](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-08-dependency-added-1.0.0.png)

1. Выберите вкладку **Свойства**. Выберите значение **1.8** для обоих параметров **Source Compability** (Совместимость источника) и **Target Compatibility** (Совместимость цели).

   ![Снимок экрана совместимости источника и цели](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-09-dependency-added.png)

1. Чтобы закрыть окно **Project Structure** (Структура проекта) и применить изменения к проекту, нажмите кнопку **OК**.
