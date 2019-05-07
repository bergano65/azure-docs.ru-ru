---
title: Ведение журнала пакета SDK для распознавания речи — службы распознавания речи
titleSuffix: Azure Cognitive Services
description: Включите ведение журнала в пакете SDK для распознавания речи.
services: cognitive-services
author: amitkumarshukla
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: amishu
ms.openlocfilehash: e8f470647c218188705cfa65cba7077e62d1b2ac
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65148025"
---
# <a name="enable-logging-in-the-speech-sdk"></a>Включить ведение журнала в пакете SDK для распознавания речи

Ведение журнала, к файлу является дополнительным компонентом Speech SDK. Во время разработки ведения журнала предоставляет дополнительные сведения и диагностики из основных компонентов пакета SDK для распознавания речи. Его можно включить, задав свойство `Speech_LogFilename` для объекта конфигурации речи, чтобы расположение и имя файла журнала. Ведение журнала будет активироваться глобально, сразу после создания распознавателя создается из конфигурации и не может быть отключен после этого. Не удается изменить имя файла журнала во время выполнения, ведение журнала сеанса.

> [!NOTE]
> Ведение журнала доступно на всех поддерживаемых Speech SDK, языки программирования, за исключением JavaScript.

## <a name="sample"></a>Образец

Имя файла журнала указано в объекте конфигурации. Используя `SpeechConfig` в качестве примера при условии, что вы создали экземпляр вызывается `config`:

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

Распознаватель можно создать объекта конфигурации. Это позволит ведения журнала для всех распознаватели.

> [!NOTE]
> Если вы создаете `SpeechSynthesizer` объекта конфигурации, он будет не включить ведение журналов. Если хотя включено ведение журнала, вы также получите диагностики из `SpeechSynthesizer`.

## <a name="create-a-log-file-on-different-platforms"></a>Создать файл журнала на разных платформах

Для Windows или Linux файл журнала можно в любой путь, к которым у пользователя есть разрешение на запись. Разрешения на запись для файловой системы в других операционных системах может быть ограниченном или запрещенном доступе по умолчанию.

### <a name="universal-windows-platform-uwp"></a>Универсальная платформа Windows

Приложения универсальной платформы Windows должны иметь что местах файлы журналов в одном из расположений данных приложения (локальной перемещаемых и временные). Файл журнала создаются в папке локального приложения:

```csharp
StorageFolder storageFolder = ApplicationData.Current.LocalFolder;
StorageFile logFile = await storageFolder.CreateFileAsync("logfile.txt", CreationCollisionOption.ReplaceExisting);
config.SetProperty(PropertyId.Speech_LogFilename, logFile.Path);
```

Более о доступ к файлам разрешение для приложений универсальной платформы Windows доступно [здесь](https://docs.microsoft.com/windows/uwp/files/file-access-permissions).

### <a name="android"></a>Android

Можно сохранить файл журнала для внутреннего хранилища, внешнее хранилище или каталога кэша. Файлы, созданные во внутреннем хранилище или каталога кэша являются закрытыми для приложения. Рекомендуется, чтобы создать файл журнала во внешнем хранилище.

```java
File dir = context.getExternalFilesDir(null);
File logFile = new File(dir, "logfile.txt");
config.setProperty(PropertyId.Speech_LogFilename, logFile.getAbsolutePath());
```

В приведенном выше коде сохранит файл журнала во внешнее хранилище в корневом каталоге конкретного приложения. Пользователь может открыть файл с помощью диспетчера файлов (обычно в `Android/data/ApplicationName/logfile.txt`). Файл будет удален при удалении приложения.

Вам также необходимо запросить `WRITE_EXTERNAL_STORAGE` разрешение в файле манифеста:

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android" package="...">
  ...
  <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
  ...
</manifest>
```

Более о данных и файла хранилище для приложений Android доступно [здесь](https://developer.android.com/guide/topics/data/data-storage.html).

#### <a name="ios"></a>iOS

Доступны только каталогов в изолированной программной среде приложения. Файлы могут быть созданы в документы, библиотеки и временных каталогов. Файлы в каталоге документов могут быть сделаны доступными пользователю. В следующем фрагменте кода показано создание файла журнала в каталоге приложения документа:

```objc
NSString *filePath = [
  [NSSearchPathForDirectoriesInDomains(NSDocumentDirectory, NSUserDomainMask, YES) firstObject]
    stringByAppendingPathComponent:@"logfile.txt"];
[speechConfig setPropertyTo:filePath byId:SPXSpeechLogFilename];
```

Чтобы открыть созданный файл, добавьте приведенные ниже свойства для `Info.plist` список свойств приложения:

```xml
<key>UIFileSharingEnabled</key>
<true/>
<key>LSSupportsOpeningDocumentsInPlace</key>
<true/>
```

Более о iOS файловая система доступна [здесь](https://developer.apple.com/library/archive/documentation/FileManagement/Conceptual/FileSystemProgrammingGuide/FileSystemOverview/FileSystemOverview.html).

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Примеры на сайте GitHub](https://aka.ms/csspeech/samples)

