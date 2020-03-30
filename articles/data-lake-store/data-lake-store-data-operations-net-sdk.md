---
title: '.NET SDK: Операции файловой системы на хранилище озер данных Azure Gen1'
description: Используйте Хранилище озера Лазурных Данных Gen1 .NET SDK для операций файловой системы data Lake Storage Gen1, таких как создание папок и т.д.
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 01/03/2020
ms.author: twooley
ms.openlocfilehash: 7e33ecbbb49fc2b0683d0757da36deec72796806
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75638907"
---
# <a name="filesystem-operations-on-data-lake-storage-gen1-using-the-net-sdk"></a>Операции файловой системы на хранении данных озера Gen1 с помощью .NET SDK

> [!div class="op_single_selector"]
> * [Пакет SDK для .NET](data-lake-store-data-operations-net-sdk.md)
> * [пакет SDK для Java](data-lake-store-get-started-java-sdk.md)
> * [REST API](data-lake-store-data-operations-rest-api.md)
> * [Python](data-lake-store-data-operations-python.md)
>
>

В этой статье вы узнаете, как выполнять операции файловой системы на Data Lake Storage Gen1 с помощью .NET SDK. К операциям файловой системы относятся операции создания папок в учетной записи Data Lake Storage 1-го поколения, передачи файлов, скачивания файлов и т. д.

Для получения инструкций о том, как выполнять операции по управлению счетами [Account management operations on Data Lake Storage Gen1 using .NET SDK](data-lake-store-get-started-net-sdk.md)на Data Lake Storage Gen1 с помощью .NET SDK, см.

## <a name="prerequisites"></a>Предварительные требования

* **Визуальная студия 2013 или выше**. Инструкции в этой статье используют Visual Studio 2019.

* **Подписка Azure**. См. страницу [бесплатной пробной версии Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Учетная запись Azure Data Lake Storage Gen1**. Для получения инструкций о том, как создать учетную запись, [см.](data-lake-store-get-started-portal.md)

## <a name="create-a-net-application"></a>Создание приложения .NET

На [сайте GitHub](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-get-started/tree/master/AdlsSDKGettingStarted) доступен пример кода, который используется для создания файлов в хранилище, объединения и скачивания файлов, а также для удаления файлов из хранилища. В этом разделе статьи рассматриваются основные части этого кода.

1. В Visual Studio выберите меню **файла,** **Новое**, а затем **проект**.
1. Выберите **консоль App (.NET Framework),** а затем выберите **Следующий**.
1. В окне**Имя проекта** введите `CreateADLApplication`, а затем выберите **Создать**.
1. Добавьте пакеты NuGet в проект.

   1. В обозревателе решений щелкните правой кнопкой мыши имя проекта и выберите пункт **Управление пакетами NuGet**.
   1. Во вкладке **NuGet Package Manager** убедитесь, что **источник пакета** настроен на **nuget.org.** Кроме того, убедитесь, что **включении предварительного выпуска** флажок выбран.
   1. Найдите и установите следующие пакеты NuGet:

      * `Microsoft.Azure.DataLake.Store`- В этой статье используется v1.0.0.
      * `Microsoft.Rest.ClientRuntime.Azure.Authentication`- В этой статье используется v2.3.1.

      Закройте **менеджер пакета NuGet.**

1. Откройте файл **Program.cs**, удалите существующий код и включите следующие инструкции, чтобы добавить ссылки на пространства имен.

    ```
    using System;
    using System.IO;using System.Threading;
    using System.Linq;
    using System.Text;
    using System.Collections.Generic;
    using System.Security.Cryptography.X509Certificates; // Required only if you're using an Azure AD application created with certificates
                
    using Microsoft.Rest;
    using Microsoft.Rest.Azure.Authentication;
    using Microsoft.Azure.DataLake.Store;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    ```

1. Объявите переменные, как показано ниже, и укажите значения вместо заполнителей. Кроме того, убедитесь, что локальный путь и имя файла, которые вы указываете, имеются на компьютере.

    ```
    namespace SdkSample
    {
        class Program
        {
            private static string _adlsg1AccountName = "<DATA-LAKE-STORAGE-GEN1-NAME>.azuredatalakestore.net";
        }
    }
    ```

В остальных разделах статьи можно увидеть, как использовать доступные методы .NET для проведения таких операций, как аутентификация, загрузка файлов и т.д.

## <a name="authentication"></a>Проверка подлинности

* Дополнительные сведения о проверке подлинности пользователей в приложении см. в статье [Аутентификация пользователей в Data Lake Store с помощью пакета SDK для .NET](data-lake-store-end-user-authenticate-net-sdk.md).
* Дополнительные сведения о проверке подлинности между службами в приложении см. в статье [Аутентификация между службами в Data Lake Store с помощью пакета SDK для .NET](data-lake-store-service-to-service-authenticate-net-sdk.md).

## <a name="create-client-object"></a>Создание клиентского объекта

Следующий фрагмент кода создает клиентский объект файловой системы Data Lake Storage 1-го поколения, который используется для передачи запросов к службе.

```
// Create client objects
AdlsClient client = AdlsClient.CreateClient(_adlsg1AccountName, adlCreds);
```

## <a name="create-a-file-and-directory"></a>Создание файла и каталога

Добавьте в приложение указанный ниже фрагмент кода. Этот фрагмент добавляет файл и любой родительский каталог, который не существует.

```
// Create a file - automatically creates any parent directories that don't exist
// The AdlsOutputStream preserves record boundaries - it does not break records while writing to the store

using (var stream = client.CreateFile(fileName, IfExists.Overwrite))
{
    byte[] textByteArray = Encoding.UTF8.GetBytes("This is test data to write.\r\n");
    stream.Write(textByteArray, 0, textByteArray.Length);

    textByteArray = Encoding.UTF8.GetBytes("This is the second line.\r\n");
    stream.Write(textByteArray, 0, textByteArray.Length);
}
```

## <a name="append-to-a-file"></a>Добавление данных в файл

Следующий фрагмент кода добавляет данные в имеющийся файл в учетной записи Data Lake Storage 1-го поколения.

```
// Append to existing file

using (var stream = client.GetAppendStream(fileName))
{
    byte[] textByteArray = Encoding.UTF8.GetBytes("This is the added line.\r\n");
    stream.Write(textByteArray, 0, textByteArray.Length);
}
```

## <a name="read-a-file"></a>Чтение файла

Следующий фрагмент кода считывает содержимое файла в Data Lake Storage 1-го поколения.

```
//Read file contents

using (var readStream = new StreamReader(client.GetReadStream(fileName)))
{
    string line;
    while ((line = readStream.ReadLine()) != null)
    {
        Console.WriteLine(line);
    }
}
```

## <a name="get-file-properties"></a>Получение свойств файла

Следующий фрагмент кода возвращает свойства, связанные с файлом или каталогом.

```
// Get file properties
var directoryEntry = client.GetDirectoryEntry(fileName);
PrintDirectoryEntry(directoryEntry);
```

Определение метода `PrintDirectoryEntry` доступно как часть образца [на GitHub](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-get-started/tree/master/AdlsSDKGettingStarted).

## <a name="rename-a-file"></a>Переименование файла

Следующий фрагмент кода переименовывает имеющийся файл в учетной записи Data Lake Storage 1-го поколения.

```
// Rename a file
string destFilePath = "/Test/testRenameDest3.txt";
client.Rename(fileName, destFilePath, true);
```

## <a name="enumerate-a-directory"></a>Перечисление каталогов

Следующий фрагмент перечисляет каталоги в учетной записи Data Lake Storage Gen1.

```
// Enumerate directory
foreach (var entry in client.EnumerateDirectory("/Test"))
{
    PrintDirectoryEntry(entry);
}
```

Определение метода `PrintDirectoryEntry` доступно как часть образца [на GitHub](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-get-started/tree/master/AdlsSDKGettingStarted).

## <a name="delete-directories-recursively"></a>Рекурсивное удаление каталогов

Следующий фрагмент удаляет каталог, и все его субдиректори, рекурсивно.

```
// Delete a directory and all its subdirectories and files
client.DeleteRecursive("/Test");
```

## <a name="samples"></a>Примеры

Вот несколько примеров, которые показывают, как использовать данные озера хранения Gen1 Файловая система SDK.

* [Базовый образец на GitHub](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-get-started/tree/master/AdlsSDKGettingStarted)
* [Расширенный образец на GitHub](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-samples)

## <a name="see-also"></a>См. также

* [Операции управления учетными записями в Data Lake Storage 1-го поколения c использованием пакета SDK для .NET](data-lake-store-get-started-net-sdk.md)
* [Библиотеки Azure Data Lake Store для .NET](https://docs.microsoft.com/dotnet/api/overview/azure/data-lake-store?view=azure-dotnet)

## <a name="next-steps"></a>Дальнейшие действия

* [Защита данных в Data Lake Storage Gen1](data-lake-store-secure-data.md)
