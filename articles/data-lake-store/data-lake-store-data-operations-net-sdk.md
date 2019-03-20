---
title: Пакет SDK для .NET. Операции файловой системы в Azure Data Lake Storage 1-го поколения | Документация Майкрософт
description: Выполнение операций файловой системы в Data Lake Storage 1-го поколения, таких как создание папок и т. д., с помощью пакета SDK для .NET.
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: nitinme
ms.openlocfilehash: 0a6cfc5ca9a0b788075f4472649a6e1bcdf87637
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58119502"
---
# <a name="filesystem-operations-on-azure-data-lake-storage-gen1-using-net-sdk"></a>Операции файловой системы в Azure Data Lake Storage 1-го поколения с использованием пакета SDK для .NET
> [!div class="op_single_selector"]
> * [ПАКЕТ SDK .NET](data-lake-store-data-operations-net-sdk.md)
> * [Пакет SDK для Java](data-lake-store-get-started-java-sdk.md)
> * [REST API](data-lake-store-data-operations-rest-api.md)
> * [Python](data-lake-store-data-operations-python.md)
>
>

В этой статье содержатся сведения о выполнении операций файловой системы в Data Lake Storage 1-го поколения с использованием SDK для .NET. К операциям файловой системы относятся операции создания папок в учетной записи Data Lake Storage 1-го поколения, передачи файлов, скачивания файлов и т. д.

Дополнительные сведения о том, как выполнять операции управления учетными записями в Data Lake Storage 1-го поколения с помощью пакета SDK для .NET, см. в [этой статье](data-lake-store-get-started-net-sdk.md).

## <a name="prerequisites"></a>Технические условия
* **Visual Studio 2013, 2015 или 2017**. В инструкциях ниже используется Visual Studio 2017.

* **Подписка Azure**. См. страницу [бесплатной пробной версии Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Учетная запись Azure Data Lake Storage 1-го поколения**. Инструкции по созданию учетной записи см. в статье [Начало работы с Azure Data Lake Storage 1-го поколения с помощью портала Azure](data-lake-store-get-started-portal.md).

## <a name="create-a-net-application"></a>Создание приложения .NET
На [сайте GitHub](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-get-started/tree/master/AdlsSDKGettingStarted) доступен пример кода, который используется для создания файлов в хранилище, объединения и скачивания файлов, а также для удаления файлов из хранилища. В этом разделе статьи рассматриваются основные части этого кода.

1. Откройте Visual Studio и создайте консольное приложение.
2. В меню **Файл** выберите команду **Создать**, а затем — **Проект**.
3. В окне **Новый проект**введите или выберите следующие значения.

   | Свойство | Значение |
   | --- | --- |
   | Категория |Templates/Visual C#/Windows |
   | Шаблон |Консольное приложение |
   | ИМЯ |CreateADLApplication |

4. Нажмите кнопку **ОК** , чтобы создать проект.

5. Добавьте пакеты NuGet в проект.

   1. В обозревателе решений щелкните правой кнопкой мыши имя проекта и выберите пункт **Управление пакетами NuGet**.
   2. На вкладке **Диспетчер пакетов NuGet** в поле **Источник пакета** выберите **nuget.org** и установите флажок **Включить предварительные выпуски**.
   3. Найдите и установите следующие пакеты NuGet:

      * `Microsoft.Azure.DataLake.Store` — в этом руководстве используется версия 1.0.0.
      * `Microsoft.Rest.ClientRuntime.Azure.Authentication` — в этом руководстве используется версия 2.3.1.
    
      Закройте **диспетчер пакетов NuGet**.

6. Откройте файл **Program.cs**, удалите существующий код и включите следующие инструкции, чтобы добавить ссылки на пространства имен.

        using System;
        using System.IO;using System.Threading;
        using System.Linq;
        using System.Text;
        using System.Collections.Generic;
        using System.Security.Cryptography.X509Certificates; // Required only if you are using an Azure AD application created with certificates
                
        using Microsoft.Rest;
        using Microsoft.Rest.Azure.Authentication;
        using Microsoft.Azure.DataLake.Store;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;

7. Объявите переменные, как показано ниже, и укажите значения вместо заполнителей. Кроме того, убедитесь, что локальный путь и имя файла, которые вы указываете, имеются на компьютере.

        namespace SdkSample
        {
            class Program
            {
                private static string _adlsg1AccountName = "<DATA-LAKE-STORAGE-GEN1-NAME>.azuredatalakestore.net";        
            }
        }

В остальных разделах статьи рассматривается использование доступных методов .NET при выполнении таких операций, как проверка подлинности, отправка файла и т. д.

## <a name="authentication"></a>Authentication

* Дополнительные сведения о проверке подлинности пользователей в приложении см. в статье [Аутентификация пользователей в Data Lake Store с помощью пакета SDK для .NET](data-lake-store-end-user-authenticate-net-sdk.md).
* Дополнительные сведения о проверке подлинности между службами в приложении см. в статье [Аутентификация между службами в Data Lake Store с помощью пакета SDK для .NET](data-lake-store-service-to-service-authenticate-net-sdk.md).


## <a name="create-client-object"></a>Создание клиентского объекта
Следующий фрагмент кода создает клиентский объект файловой системы Data Lake Storage 1-го поколения, который используется для передачи запросов к службе.

    // Create client objects
    AdlsClient client = AdlsClient.CreateClient(_adlsg1AccountName, adlCreds);

## <a name="create-a-file-and-directory"></a>Создание файла и каталога
Добавьте в приложение указанный ниже фрагмент кода. Этот фрагмент кода добавляет файл, а также любой родительский каталог, которые еще не добавлены.

    // Create a file - automatically creates any parent directories that don't exist
    // The AdlsOutputStream preserves record boundaries - it does not break records while writing to the store
    using (var stream = client.CreateFile(fileName, IfExists.Overwrite))
    {
        byte[] textByteArray = Encoding.UTF8.GetBytes("This is test data to write.\r\n");
        stream.Write(textByteArray, 0, textByteArray.Length);

        textByteArray = Encoding.UTF8.GetBytes("This is the second line.\r\n");
        stream.Write(textByteArray, 0, textByteArray.Length);
    }

## <a name="append-to-a-file"></a>Добавление данных в файл
Следующий фрагмент кода добавляет данные в имеющийся файл в учетной записи Data Lake Storage 1-го поколения.

    // Append to existing file
    using (var stream = client.GetAppendStream(fileName))
    {
        byte[] textByteArray = Encoding.UTF8.GetBytes("This is the added line.\r\n");
        stream.Write(textByteArray, 0, textByteArray.Length);
    }

## <a name="read-a-file"></a>Чтение файла
Следующий фрагмент кода считывает содержимое файла в Data Lake Storage 1-го поколения.

    //Read file contents
    using (var readStream = new StreamReader(client.GetReadStream(fileName)))
    {
        string line;
        while ((line = readStream.ReadLine()) != null)
        {
            Console.WriteLine(line);
        }
    }

## <a name="get-file-properties"></a>Получение свойств файла
Следующий фрагмент кода возвращает свойства, связанные с файлом или каталогом.

    // Get file properties
    var directoryEntry = client.GetDirectoryEntry(fileName);
    PrintDirectoryEntry(directoryEntry);

Определение метода `PrintDirectoryEntry` доступно в примере [на сайте GitHub](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-get-started/tree/master/AdlsSDKGettingStarted). 

## <a name="rename-a-file"></a>Переименование файла
Следующий фрагмент кода переименовывает имеющийся файл в учетной записи Data Lake Storage 1-го поколения.

    // Rename a file
    string destFilePath = "/Test/testRenameDest3.txt";
    client.Rename(fileName, destFilePath, true);

## <a name="enumerate-a-directory"></a>Перечисление каталогов
Следующий фрагмент кода перечисляет каталоги в учетной записи Data Lake Storage 1-го поколения.

    // Enumerate directory
    foreach (var entry in client.EnumerateDirectory("/Test"))
    {
        PrintDirectoryEntry(entry);
    }

Определение метода `PrintDirectoryEntry` доступно в примере [на сайте GitHub](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-get-started/tree/master/AdlsSDKGettingStarted).

## <a name="delete-directories-recursively"></a>Рекурсивное удаление каталогов
Следующий фрагмент кода удаляет каталог и все его подкаталоги рекурсивно.

    // Delete a directory and all its subdirectories and files
    client.DeleteRecursive("/Test");

## <a name="samples"></a>Примеры
Ниже приведено несколько примеров использования пакета SDK для файловой системы Data Lake Storage 1-го поколения.
* [Базовый пример на сайте GitHub](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-get-started/tree/master/AdlsSDKGettingStarted)
* [Расширенный пример на сайте GitHub](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-samples)

## <a name="see-also"></a>См. также
* [Операции управления учетными записями в Data Lake Storage 1-го поколения c использованием пакета SDK для .NET](data-lake-store-get-started-net-sdk.md)
* [Библиотеки Azure Data Lake Store для .NET](https://docs.microsoft.com/dotnet/api/overview/azure/data-lake-store?view=azure-dotnet)

## <a name="next-steps"></a>Дальнейшие действия
* [Защита данных в Data Lake Storage Gen1](data-lake-store-secure-data.md)
