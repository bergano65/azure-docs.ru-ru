---
title: Речевой журнал SDK - Речевая служба
titleSuffix: Azure Cognitive Services
description: Узнайте о том, как включить вход в Рено СДК (СЗ, КЗ, Пайтон, Объектив-С, Ява).
services: cognitive-services
author: amitkumarshukla
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: amishu
ms.openlocfilehash: 707a0f801a739a7a91cee19635e609305cd8f021
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "74805796"
---
# <a name="enable-logging-in-the-speech-sdk"></a>Включить регистрацию в речи SDK

Регистрация в файл является дополнительной функцией для речи SDK. Во время разработки журнал предоставляет дополнительную информацию и диагностику из основных компонентов Speech SDK. Его можно заставить, `Speech_LogFilename` установив свойство на объекте конфигурации речи к местоположению и названию файла журнала. Регистрация будет активирована глобально, как только распознает создается из этой конфигурации и не может быть отключена впоследствии. Вы не можете изменить имя файла журнала во время сеанса ведения журнала.

> [!NOTE]
> Регистрация доступна с версии Speech SDK 1.4.0 на всех поддерживаемых языках программирования Speech SDK, за исключением JavaScript.

## <a name="sample"></a>Пример

Имя файла журнала указывается на объекте конфигурации. Взяв `SpeechConfig` в качестве примера и предполагая, `config`что вы создали экземпляр под названием:

```csharp
config.SetProperty(PropertyId.Speech_LogFilename, "LogfilePathAndName");
```

```java
config.setProperty(PropertyId.Speech_LogFilename, "LogfilePathAndName");
```

```C++
config->SetProperty(PropertyId::Speech_LogFilename, "LogfilePathAndName");
```

```Python
config.set_property(speechsdk.PropertyId.Speech_LogFilename, "LogfilePathAndName")
```

```objc
[config setPropertyTo:@"LogfilePathAndName" byId:SPXSpeechLogFilename];
```

Можно создать распознаватель из объекта конфигурации. Это позволит регистрать для всех распознавателей.

> [!NOTE]
> При создании `SpeechSynthesizer` из объекта конфигурации, он не будет включать в себя журнал. Если журнал включен, вы также получите `SpeechSynthesizer`диагностику от .

## <a name="create-a-log-file-on-different-platforms"></a>Создание файла журнала на разных платформах

Для Windows или Linux файл журнала может находиться на любом пути, на который пользователь имеет разрешение. Запись разрешений на файлы расположения системы в других операционных системах может быть ограничена или ограничена по умолчанию.

### <a name="universal-windows-platform-uwp"></a>Универсальная платформа Windows (UWP)

Приложения UWP должны размещать файлы журналов в одном из местоположений данных приложения (местные, роуминговые или временные). Файл журнала может быть создан в папке локального приложения:

```csharp
StorageFolder storageFolder = ApplicationData.Current.LocalFolder;
StorageFile logFile = await storageFolder.CreateFileAsync("logfile.txt", CreationCollisionOption.ReplaceExisting);
config.SetProperty(PropertyId.Speech_LogFilename, logFile.Path);
```

Подробнее о разрешении доступа к файлам для приложений UWP можно получить [здесь](https://docs.microsoft.com/windows/uwp/files/file-access-permissions).

### <a name="android"></a>Android

Можно сохранить файл журнала либо во внутреннем хранилище, внешнем хранилище или каталоге кэша. Файлы, созданные во внутреннем хранилище или каталоге кэша, являются конфиденциальными для приложения. Предпочтительно создать файл журнала во внешнем хранилище.

```java
File dir = context.getExternalFilesDir(null);
File logFile = new File(dir, "logfile.txt");
config.setProperty(PropertyId.Speech_LogFilename, logFile.getAbsolutePath());
```

Приведенный выше код сохранит файл журнала для внешнего хранилища в корне каталога, конкретном приложении. Пользователь может получить доступ к файлу `Android/data/ApplicationName/logfile.txt`с диспетчером файлов (обычно в). Файл будет удален, когда приложение не установлено.

Вы также должны `WRITE_EXTERNAL_STORAGE` запросить разрешение в файле манифеста:

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android" package="...">
  ...
  <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
  ...
</manifest>
```

Подробнее о данных и хранении файлов для приложений Android можно найти [здесь.](https://developer.android.com/guide/topics/data/data-storage.html)

#### <a name="ios"></a>iOS

Доступны только каталоги внутри песочницы приложения. Файлы могут быть созданы в документах, библиотеке и временных каталогах. Файлы в каталоге документов могут быть доступны пользователю. Следующий фрагмент кода показывает создание файла журнала в каталоге документов приложения:

```objc
NSString *filePath = [
  [NSSearchPathForDirectoriesInDomains(NSDocumentDirectory, NSUserDomainMask, YES) firstObject]
    stringByAppendingPathComponent:@"logfile.txt"];
[speechConfig setPropertyTo:filePath byId:SPXSpeechLogFilename];
```

Чтобы получить доступ к созданного файла, добавьте ниже приведенные свойства в `Info.plist` список свойств приложения:

```xml
<key>UIFileSharingEnabled</key>
<true/>
<key>LSSupportsOpeningDocumentsInPlace</key>
<true/>
```

Подробнее о iOS Файловая система доступна [здесь](https://developer.apple.com/library/archive/documentation/FileManagement/Conceptual/FileSystemProgrammingGuide/FileSystemOverview/FileSystemOverview.html).

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Примеры на сайте GitHub](https://aka.ms/csspeech/samples)
