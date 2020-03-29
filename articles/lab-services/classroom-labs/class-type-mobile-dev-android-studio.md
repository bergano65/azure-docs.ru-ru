---
title: Настройка лаборатории для обучения разработке мобильных приложений с Android Studio
titleSuffix: Azure Lab Services
description: Узнайте, как создать лабораторию для обучения класса разработки мобильных приложений данных, используюго Android Studio.  В статье также будут обсуждаться корректировки при использовании Android Studio на виртуальной машине в Azure.
services: lab-services
author: emaher
ms.service: lab-services
ms.topic: article
ms.date: 1/23/2020
ms.author: enewman
ms.openlocfilehash: 0c257589a2e93ac4c15a639e7156d0c0944b033c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76849790"
---
# <a name="set-up-a-lab-to-teach-data-mobile-application-development-with-android-studio"></a>Настройка лаборатории для обучения разработке мобильных приложений с Помощью Android Studio

В этой статье будет показан способ настройки вводного класса разработки мобильных приложений.  Этот класс посвящен мобильным приложениям Android, которые можно опубликовать в [Google Play Маркет](https://play.google.com/store/apps).  Учащиеся узнают, как использовать [Android Studio](https://developer.android.com/studio) для создания приложений.  [Эмулятор Visual Studio для Android](https://visualstudio.microsoft.com/vs/msft-android-emulator/) используется для локального тестирования приложения.

## <a name="lab-configuration"></a>Настройка лаборатории

Чтобы настроить эту лабораторию, вам нужна подписка Azure и лабораторный учет, чтобы начать работу. Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/), прежде чем начинать работу. Получив подписку Azure, можно создать новую учетную запись лаборатории в лабораторных службах Azure. Для получения дополнительной информации о [tutorial to set up a lab account](tutorial-setup-lab-account.md)создании новой учетной записи лаборатории, см.  Вы также можете использовать существующую учетную запись лаборатории.

Следуйте [настроить класс лаборатории учебник](tutorial-setup-classroom-lab.md) для создания новой лаборатории, а затем применить следующие настройки:

| размер виртуальной машины; | Изображение |
| -------------------- | ----- |
| Средний (Вложенная виртуализация) | Windows Server 2019 Datacenter |

## <a name="template-machine-configuration"></a>Конфигурация машины шаблона

Когда создание машины шаблона будет завершено, [запустите машину и подключитесь к ней](how-to-create-manage-template.md#update-a-template-vm) для выполнения следующих задач:

1. Добавить роль Hyper-V
2. Скачать и установить Java.  
3. Скачать и установить Visual Studio Emulator для Android.
4. Скачать и установить Android Studio.
5. Назначь visual Studio Эмулятор для Android Studio.

## <a name="add-hyper-v-role"></a>Добавить роль Hyper-V

Hyper-V должен быть включен для успешной установки Visual Studio Emulator для Android.  Следуйте инструкциям в [том, как включить вложенную виртуализацию в шаблоне виртуальной машины](how-to-enable-nested-virtualization-template-vm.md) статьи.

## <a name="install-java"></a>Установка Java

Android Studio требует Java.  Следуйте ниже, чтобы загрузить последнюю версию Java.

1. Перейдите на [страницу загрузки Java](https://www.java.com/download/). Нажмите кнопку **Java Скачать.**
2. На 64-битной Windows для веб-страницы Java нажмите кнопку с пометкой **«Согласен» и «Начни бесплатную загрузку».**
3. При попастанителе **установки Java** нажмите **«Установка».**
4. Подождите, пока название установки не изменится в **Java Setup - Complete.**  Нажмите **кнопку "Закрыть".**

## <a name="install-visual-studio-emulator-for-android"></a>Установка визуального эмулятора студии для Android

Чтобы протестировать локальное приложение Android, оно должно использовать виртуализированную версию устройства Android.  Есть несколько Эмуляторов Android доступны, которые позволят разработчику проверить свое приложение от своей машины.  Мы используем Visual Studio Emulator для Android, потому что это эмулятор, который поддерживает вложенную виртуализацию.  Поскольку Lab Service VM уже является виртуальной машиной, нам нужен эмулятор, поддерживающий вложенную виртуализацию.  Встроенный эмулятор для Android Studio не поддерживает вложенную виртуализацию.  Чтобы узнать, какие эмуляторы поддерживают вложенную виртуализацию, см. [аппаратное ускорение для производительности эмулятора (Hyper-V & HAXM).](https://docs.microsoft.com/xamarin/android/get-started/installation/android-emulator/hardware-acceleration)

Используйте следующие инструкции для загрузки и установки Visual Studio Emulator для Android.

1. Перейдите к [Visual Studio Эмулятор для Android](https://visualstudio.microsoft.com/vs/msft-android-emulator/) домашнюю страницу.
2. Нажмите **кнопку Скачать эмулятор.**
3. При загрузке vs_emulatorsetup.exe запустите исполняемый.
4. При попавании диалога о настройках Visual Studio нажмите кнопку **«Установка».**
5. Подождите, пока установщик завершится.  Нажмите кнопку **Restart Now,** чтобы перезапустить компьютер и закончить установку.

Запустите эмулятор, прежде чем развернуть приложение с помощью Android Studio.  Для получения дополнительной информации о Visual Studio Эмулятор для Android, см [Visual Studio Эмулятор для Android документации](https://docs.microsoft.com/visualstudio/cross-platform/visual-studio-emulator-for-android).

## <a name="install-android-studio"></a>Установка Android Studio

Следуйте инструкциям ниже, чтобы загрузить и установить [Android Studio](https://developer.android.com/studio).

1. Перейдите на [страницу загрузки Android Studio](https://developer.android.com/studio#downloads).  
    > [!NOTE]
    > Internet Explorer не поддерживается этим сайтом.
2. Нажмите на пакет Windows (64-разрядный) android Studio.
3. Читайте юридические термины, написанные во всплывающем материале.  Когда готовы продолжать, проверить **я прочитал и согласен с вышеуказанными условиями** флажок и нажмите **Скачать Android Studio для кнопки Windows.**
4. После того, как на Android Studio настройка исполняемая загружается, запустить исполняемый.
5. На **страницах настройки Android Studio** установки **Android Studio** нажмите **кнопку Next**.
6. На странице **настройки конфигурации** нажмите **далее**.
7. На странице **«Выбрать меню«Выбрать меню»** нажмите **«Установить».**
8. Подождите, пока установка будет завершена.
9. На полной странице **Установки** нажмите **далее**.
10. На странице **настройки Android Studio.**  Нажмите кнопку **Готово**.
11. Android Studio автоматически запускается после завершения настройки.
12. На **импорт нотрации Android От...** диалога выберите **не импортируйте настройки.** Нажмите кнопку **ОК**.
13. На **приветственной** странице **Android Studio Setup Wizard**, нажмите **далее**.
14. На странице **«Установка типа»** выберите **Стандартный**. Нажмите кнопку **Далее**.
15. На странице **Тема выбора избранного uI** выберите желаемую тему. Нажмите кнопку **Далее**.
16. На странице **Проверки настроек** нажмите **далее**.
17. На странице **загрузки компонентов** подождите, пока все компоненты будут загружены.  Нажмите кнопку **Готово**.

    > [!IMPORTANT]
    > Ожидается, что установка HAXM выйдет из строя.  HAXM не поддерживает вложенную виртуализацию, поэтому мы установили Visual Studio Emulator для Android ранее в этой статье.

18. **Добро пожаловать в** диалог Android Studio появится, когда мастер установки будет завершен.

## <a name="configure-android-studio-and-visual-studio-emulator-for-android"></a>Нанастройка Android Studio и visual Studio Emulator для Android

Android Studio почти готова к использованию.  Мы все еще должны сказать Visual Studio Эмулятор для Android, где Android SDK установлен.  Это сделает любые эмуляторы работает в Visual Studio для Android шоу в качестве целей развертывания для Android Studio отладки.

Нам нужно установить конкретный ключ реестра, чтобы сказать Visual Studio Эмулятор для Android, где Android Sdk находится.  Чтобы установить необходимый ключ реестра, запустите сценарий ниже.  Сценарий PowerShell ниже предполагает расположение установки по умолчанию для Android Sdk.  Если вы установили Android Sdk в другом `$androidSdkPath` месте, измените значение перед запуском скрипта.

```powershell
$androidSdkPath = Resolve-Path $(Join-Path "$($env:APPDATA)" "../Local/Android/Sdk")

$registryKeyPath = "HKLM:Software\WOW6432NODE\Android Sdk Tools"
New-Item -Path $registryKeyPath
New-ItemProperty -Path $registryKeyPath -Name Path -PropertyType String -Value $androidSdkPath
```

> [!IMPORTANT]
> Перезапуск Visual Studio Emulator для Android и Android Studio, так что новая настройка используется.

Запустите необходимую версию в Visual Studio Emulator.  Он будет отображаться в качестве цели развертывания для вашего приложения Android в студии Android.  Минимальная версия для проекта Android Studio должна поддерживать версию, работая в Visual Studio Emulator для Android.  Теперь вы готовы создавать и отлажовать проекты с помощью Android Studio и Visual Studio Emulator для Android.  Если у вас есть какие-либо проблемы, см Android эмулятор устранения неполадок.

## <a name="cost"></a>Стоимость

Если вы хотите оценить стоимость этой лаборатории, вы можете последовать примеру ниже.
Для класса из 25 студентов с 20 часами запланированного времени занятий и 10 часами квоты для домашних заданий или заданий, цена для лаборатории будет  

25 \* студентов (20 запланированных и 10 квот) часов - 55 лабораторных единиц - 0,01 USD в час - 412,5 USD

Более подробную информацию о ценах можно узнать о ценах на [лабораторную политику Azure Lab.](https://azure.microsoft.com/pricing/details/lab-services/)

## <a name="next-steps"></a>Дальнейшие действия

Следующие шаги являются общими для создания любой лаборатории.

- [Создание и управление шаблоном](how-to-create-manage-template.md)
- [Добавление пользователей](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Установленная квота](how-to-configure-student-usage.md#set-quotas-for-users)
- [Установить расписание](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Ссылки на регистрацию по электронной почте студентам](how-to-configure-student-usage.md#send-invitations-to-users)
