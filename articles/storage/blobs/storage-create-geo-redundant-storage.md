---
title: Руководство по Создание высокодоступного приложения с помощью хранилища BLOB-объектов — служба хранилища Azure
description: Использование геоизбыточного хранилища с доступом на чтение для обеспечения высокой доступности данных приложений
services: storage
author: tamram
ms.service: storage
ms.topic: tutorial
ms.date: 01/03/2019
ms.author: tamram
ms.reviewer: artek
ms.custom: mvc
ms.subservice: blobs
ms.openlocfilehash: ff23e5e2c4f0b55121d5310c7fbf99b3ee3b1087
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65209669"
---
# <a name="tutorial-build-a-highly-available-application-with-blob-storage"></a>Руководство по Создание высокодоступного приложения с помощью хранилища BLOB-объектов

Это руководство представляет первую часть цикла. В нем вы узнаете, как обеспечить высокую доступность данных приложений в Azure.

Когда вы выполните приведенные здесь инструкции, у вас будет консольное приложение, которое передает и извлекает большие двоичные объекты из учетной записи хранения [геоизбыточного хранилища с доступом на чтение](../common/storage-redundancy-grs.md#read-access-geo-redundant-storage) (RA-GRS).

RA-GRS функционирует, реплицируя транзакции из основного региона в дополнительный. Репликация гарантирует, что данные в дополнительном регионе согласованы в конечном счете. Приложение использует [шаблон автоматического выключения](/azure/architecture/patterns/circuit-breaker), чтобы определить, к какой конечной точке подключаться, выполняя автоматическое переключение между конечными точками в результате имитации сбоев и операций восстановления.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

В первой части цикла вы узнаете, как выполнять такие задачи:

> [!div class="checklist"]
> * Создание учетной записи хранения
> * Задание строки подключения
> * Запуск консольного приложения

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим руководством:

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

* Установите [Visual Studio 2017](https://www.visualstudio.com/downloads/) с указанными ниже рабочими нагрузками:
  - **разработка Azure.**

  ![Разработка Azure (в разделе Web & Cloud (Сеть и облако))](media/storage-create-geo-redundant-storage/workloads.png)

# <a name="pythontabpython"></a>[Python](#tab/python)

* установите [Python](https://www.python.org/downloads/);
* Загрузите и установите [пакет SDK службы хранилища Azure для Python](https://github.com/Azure/azure-storage-python).

# <a name="java-v7-sdktabjava-v7"></a>[Пакет SDK для Java версии 7](#tab/java-v7)

* Установите и настройте [Maven](https://maven.apache.org/download.cgi) для работы из командной строки.
* Установите и настройте [JDK](https://www.oracle.com/technetwork/java/javase/downloads/index.html).

# <a name="java-v10-sdktabjava-v10"></a>[Пакет SDK для Java версии 10](#tab/java-v10)

* Установите и настройте [Maven](https://maven.apache.org/download.cgi) для работы из командной строки.
* Установите и настройте [JDK](https://www.oracle.com/technetwork/java/javase/downloads/index.html).

---

## <a name="sign-in-to-the-azure-portal"></a>Вход на портал Azure

Войдите на [портале Azure](https://portal.azure.com/).

## <a name="create-a-storage-account"></a>Создание учетной записи хранения

Учетная запись хранения предоставляет уникальное пространство имен для хранения ваших объектов данных в службе хранилища Azure и доступа к ним.

Выполните следующие действия, чтобы создать учетную запись хранения геоизбыточного хранилища с доступом на чтение:

1. Нажмите кнопку **Создать ресурс** в верхнем левом углу окна портала Azure.
2. Выберите **Хранилище** на странице **Создать**.
3. Выберите **Учетная запись хранения — BLOB-объект, файл, таблица, очередь** в разделе **Рекомендованные**.
4. Заполните форму учетной записи хранения следующей информацией, как показано в изображении ниже, и нажмите кнопку **Создать**:

   | Параметр       | Рекомендуемое значение | Описание |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **Имя** | mystorageaccount | Уникальное значение учетной записи хранения. |
   | **Модель развертывания** | Диспетчер ресурсов  | Resource Manager содержит новые функции.|
   | **Account kind** (Тип учетной записи) | Хранилище версии 2 | Дополнительные сведения о типах учетных записей см. в разделе [Типы учетных записей хранения](../common/storage-introduction.md#types-of-storage-accounts). |
   | **Производительность** | Стандартная | Значения Standard достаточно для примера сценария. |
   | **Репликация**| Геоизбыточное хранилище с доступом для чтения (RA-GRS) | Это значение необходимо для работы примера. |
   |**Подписка** | Ваша подписка |Дополнительные сведения о подписках см. [здесь](https://account.windowsazure.com/Subscriptions). |
   |**ResourceGroup** | myResourceGroup |Допустимые имена групп ресурсов см. в статье о [правилах и ограничениях именования](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions). |
   |**Местоположение.** | Восточная часть США | Выберите расположение. |

![создание учетной записи хранения](media/storage-create-geo-redundant-storage/createragrsstracct.png)

## <a name="download-the-sample"></a>Скачивание примера приложения

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

[Загрузите пример проекта](https://github.com/Azure-Samples/storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs/archive/master.zip) и извлеките (распакуйте) файл storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs.zip. Вы также можете использовать команду [git](https://git-scm.com/), чтобы загрузить копию приложения в среду разработки. Пример проекта содержит консольное приложение.

```bash
git clone https://github.com/Azure-Samples/storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs.git
```

# <a name="pythontabpython"></a>[Python](#tab/python)

[Загрузите пример проекта](https://github.com/Azure-Samples/storage-python-circuit-breaker-pattern-ha-apps-using-ra-grs/archive/master.zip) и извлеките (распакуйте) файл storage-python-circuit-breaker-pattern-ha-apps-using-ra-grs.zip. Вы также можете использовать команду [git](https://git-scm.com/), чтобы загрузить копию приложения в среду разработки. Пример проекта содержит основное приложение Python.

```bash
git clone https://github.com/Azure-Samples/storage-python-circuit-breaker-pattern-ha-apps-using-ra-grs.git
```

# <a name="java-v7-sdktabjava-v7"></a>[Пакет SDK для Java версии 7](#tab/java-v7)

[Скачайте пример проекта](https://github.com/Azure-Samples/storage-java-ha-ra-grs) и извлеките файл storage-java-ragrs.zip. Вы также можете использовать команду [git](https://git-scm.com/), чтобы загрузить копию приложения в среду разработки. Пример проекта содержит простое приложение Java.

```bash
git clone https://github.com/Azure-Samples/storage-java-ha-ra-grs.git
```

# <a name="java-v10-sdktabjava-v10"></a>[Пакет SDK для Java версии 10](#tab/java-v10)

[Скачайте пример проекта](https://github.com/Azure-Samples/storage-java-V10-ha-ra-grs) и извлеките файл storage-java-ragrs.zip. Вы также можете использовать команду [git](https://git-scm.com/), чтобы загрузить копию приложения в среду разработки. Пример проекта содержит простое приложение Java.

```bash
git clone https://github.com/Azure-Samples/storage-java-V10-ha-ra-grs
```

---

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

В приложении необходимо указать строку подключения для учетной записи хранения. Рекомендуем хранить эту строку подключения в переменной среды на локальном компьютере, где выполняется приложение. Чтобы создать переменную среды, выполните один из приведенных ниже примеров в зависимости от операционной системы.

Войдите в свою учетную запись хранения на портале Azure. В меню учетной записи хранения в разделе **Параметры** выберите **Ключи доступа**. Скопируйте **строку подключения** из основного или вторичного ключа. Замените \<yourconnectionstring\> фактической строкой подключения, выполнив одну из следующих команд в зависимости от операционной системы. Эта команда сохраняет переменную среды на локальном компьютере. В Windows переменная среды доступна только после перезагрузки **командной строки** или используемой оболочки. Замените **\<storageConnectionString\>** в примере ниже.

### <a name="linux"></a>Linux

```
export storageconnectionstring=\<yourconnectionstring\>
```

### <a name="windows"></a> Windows

```PowerShell
setx storageconnectionstring "\<yourconnectionstring\>"
```

# <a name="pythontabpython"></a>[Python](#tab/python)

В приложении необходимо указать строку подключения для учетной записи хранения. Рекомендуем хранить эту строку подключения в переменной среды на локальном компьютере, где выполняется приложение. Чтобы создать переменную среды, выполните один из приведенных ниже примеров в зависимости от операционной системы.

Войдите в свою учетную запись хранения на портале Azure. В меню учетной записи хранения в разделе **Параметры** выберите **Ключи доступа**. Скопируйте **строку подключения** из основного или вторичного ключа. Замените \<yourconnectionstring\> фактической строкой подключения, выполнив одну из следующих команд в зависимости от операционной системы. Эта команда сохраняет переменную среды на локальном компьютере. В Windows переменная среды доступна только после перезагрузки **командной строки** или используемой оболочки. Замените **\<storageConnectionString\>** в примере ниже.

### <a name="linux"></a>Linux

```
export storageconnectionstring=\<yourconnectionstring\>
```

### <a name="windows"></a> Windows

```PowerShell
setx storageconnectionstring "\<yourconnectionstring\>"
```

# <a name="java-v7-sdktabjava-v7"></a>[Пакет SDK для Java версии 7](#tab/java-v7)

В приложении необходимо указать строку подключения для учетной записи хранения. Рекомендуем хранить эту строку подключения в переменной среды на локальном компьютере, где выполняется приложение. Чтобы создать переменную среды, выполните один из приведенных ниже примеров в зависимости от операционной системы.

Войдите в свою учетную запись хранения на портале Azure. В меню учетной записи хранения в разделе **Параметры** выберите **Ключи доступа**. Скопируйте **строку подключения** из основного или вторичного ключа. Замените \<yourconnectionstring\> фактической строкой подключения, выполнив одну из следующих команд в зависимости от операционной системы. Эта команда сохраняет переменную среды на локальном компьютере. В Windows переменная среды доступна только после перезагрузки **командной строки** или используемой оболочки. Замените **\<storageConnectionString\>** в примере ниже.

### <a name="linux"></a>Linux

```
export storageconnectionstring=\<yourconnectionstring\>
```

### <a name="windows"></a> Windows

```PowerShell
setx storageconnectionstring "\<yourconnectionstring\>"
```

# <a name="java-v10-sdktabjava-v10"></a>[Пакет SDK для Java версии 10](#tab/java-v10)

Этот пример требует безопасного хранения имени и ключа учетной записи хранения. Храните их в переменных среды локального компьютера, на котором будет выполняться пример. Чтобы создать переменные среды, в зависимости от операционной системы, используйте либо пример для Linux, либо для Windows. В Windows переменная среды доступна только после перезагрузки **командной строки** или используемой оболочки.

### <a name="linux-example"></a>Пример Linux

```
export AZURE_STORAGE_ACCOUNT="<youraccountname>"
export AZURE_STORAGE_ACCESS_KEY="<youraccountkey>"
```

### <a name="windows-example"></a>Пример Windows

```
setx AZURE_STORAGE_ACCOUNT "<youraccountname>"
setx AZURE_STORAGE_ACCESS_KEY "<youraccountkey>"
```

---

## <a name="run-the-console-application"></a>Запуск консольного приложения

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

В Visual Studio нажмите клавишу **F5** или щелкните **Запустить**, чтобы начать отладку приложения. Visual Studio автоматически восстанавливает отсутствующие пакеты NuGet, если это настроено. Дополнительные сведения см. в разделе [Обзор восстановления пакетов](https://docs.microsoft.com/nuget/consume-packages/package-restore#package-restore-overview).

Окно консоли запускается, и приложение начинает работать. Приложение передает изображение **HelloWorld.png** из решения в учетную запись хранения. Приложение проверяет, было ли изображение реплицировано во вторичную конечную точку RA-GRS. Затем оно начинает скачивать изображение (до 999 раз). Каждая операция чтения помечается буквой **P** или **S**. **P** представляет основную конечную точку, а **S** — вторичную конечную точку.

![Консольное приложение выполняется](media/storage-create-geo-redundant-storage/figure3.png)

В примере кода задача `RunCircuitBreakerAsync` в файле `Program.cs` используется для скачивания изображения из учетной записи хранения с помощью метода [DownloadToFileAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.blob.cloudblob.downloadtofileasync?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudBlob_DownloadToFileAsync_System_String_System_IO_FileMode_Microsoft_WindowsAzure_Storage_AccessCondition_Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_Microsoft_WindowsAzure_Storage_OperationContext_). Перед скачиванием определяется [OperationContext](/dotnet/api/microsoft.azure.cosmos.table.operationcontext?view=azure-dotnet). Контекст операции определяет обработчики событий, которые срабатывают при успешном завершении скачивания или при сбое и повторной попытке.

# <a name="pythontabpython"></a>[Python](#tab/python)

Чтобы запустить приложение в терминале или в командной строке, перейдите к каталогу **circuitbreaker.py**, а затем введите `python circuitbreaker.py`. Приложение передает изображение **HelloWorld.png** из решения в учетную запись хранения. Приложение проверяет, было ли изображение реплицировано во вторичную конечную точку RA-GRS. Затем оно начинает скачивать изображение (до 999 раз). Каждая операция чтения помечается буквой **P** или **S**. **P** представляет основную конечную точку, а **S** — вторичную конечную точку.

![Консольное приложение выполняется](media/storage-create-geo-redundant-storage/figure3.png)

В примере кода метод `run_circuit_breaker` в файле `circuitbreaker.py` используется для скачивания изображения из учетной записи хранения с помощью метода [get_blob_to_path](https://azure.github.io/azure-storage-python/ref/azure.storage.blob.baseblobservice.html).

Для функции повтора объекта хранилища задана линейная политика повтора. Функция повтора определяет, следует ли повторять запрос, и указывает время ожидания (в секундах) перед повторным выполнением запроса. Задайте для **retry\_to\_secondary** значение true, если запрос следует повторять в случае сбоя исходного запроса к основной конечной точке. В примере приложения пользовательская политика повтора определяется в функции `retry_callback` объекта хранилища.

Перед скачиванием определяется функция объекта службы [retry_callback](https://docs.microsoft.com/python/api/azure.storage.common.storageclient.storageclient?view=azure-python) и [response_callback](https://docs.microsoft.com/python/api/azure.storage.common.storageclient.storageclient?view=azure-python). Эти функции определяют обработчики событий, которые срабатывают при успешном завершении скачивания или при сбое и повторной попытке.

# <a name="java-v7-sdktabjava-v7"></a>[Пакет SDK для Java версии 7](#tab/java-v7)

Вы можете запустить приложение, открыв терминал или командную строку в папке скачанного приложения. После этого введите `mvn compile exec:java`, чтобы запустить приложение. Затем приложение передаст изображение **HelloWorld.png** из каталога в вашу учетную запись хранения и проверит, реплицировано ли оно во вторичную конечную точку RA-GRS. По завершении проверки приложение начнет скачивать изображение и передавать результат в конечную точку, из которой выполняется скачивание.

Для функции повтора объекта хранилища задана линейная политика повтора. Функция повтора определяет, следует ли повторять запрос, и указывает время ожидания (в секундах) между повторными выполнениями запроса. Для свойства **LocationMode** класса **BlobRequestOptions** задано значение **PRIMARY\_THEN\_SECONDARY**. Это позволяет приложению автоматически переключаться на вторичное расположение, если не удается перейти в первичное расположение при попытке скачать файл **HelloWorld.png**.

# <a name="java-v10-sdktabjava-v10"></a>[Пакет SDK для Java версии 10](#tab/java-v10)

Чтобы запустить пример, используйте Maven в командной строке.

1. Откройте оболочку и перейдите в папку **storage-blobs-java-v10-quickstart** в клонированном каталоге.
2. Укажите `mvn compile exec:java`.

В каталоге по умолчанию этот пример создает тестовый файл (для пользователей Windows это каталог **AppData\Local\Temp**). В этом примере представлены следующие варианты команд, которые можно ввести:

- Введите **P** для выполнения операции put blob, которая передает временный файл в учетную запись хранения.
- Введите **L** для выполнения операции list blob, которая перечисляет большие двоичные объекты, которые находятся в контейнере в данный момент.
- Введите **G** для выполнения операции get blob, которая загружает файл из учетной записи хранения на локальный компьютер.
- Введите **D** для выполнения операции delete blob, которая удаляет большой двоичный объект из учетной записи хранения.
- Введите **E** для закрытия приложения. Будут также удалены все ресурсы, созданные примером.

В этом примере показаны выходные данные при запуске приложения на системе Windows.

```
Created quickstart container
Enter a command
(P)utBlob | (L)istBlobs | (G)etBlob | (D)eleteBlobs | (E)xitSample
# Enter a command :
P
Uploading the sample file into the container: https://<storageaccount>.blob.core.windows.net/quickstart
# Enter a command :
L
Listing blobs in the container: https://<storageaccount>.blob.core.windows.net/quickstart
Blob name: SampleBlob.txt
# Enter a command :
G
Get the blob: https://<storageaccount>.blob.core.windows.net/quickstart/SampleBlob.txt
The blob was downloaded to C:\Users\<useraccount>\AppData\Local\Temp\downloadedFile13097087873115855761.txt
# Enter a command :
D
Delete the blob: https://<storageaccount>.blob.core.windows.net/quickstart/SampleBlob.txt

# Enter a command :
>> Blob deleted: https://<storageaccount>.blob.core.windows.net/quickstart/SampleBlob.txt
E
Cleaning up the sample and exiting!
```

Вы управляете образцом, поэтому введите команды для выполнения кода. Во входных данных учитывается регистр.

---

## <a name="understand-the-sample-code"></a>Разбор примера кода

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

### <a name="retry-event-handler"></a>Обработчик события при повторном запуске

Обработчик событий `OperationContextRetrying` вызывается, когда скачивание завершается с ошибкой и настроено на повторный запуск. Если достигнуто максимальное количество попыток, определенных для приложения, значение параметра [LocationMode](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.locationmode?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_LocationMode) запроса изменяется на `SecondaryOnly`. В таком случае приложение принудительно повторно пытается скачать изображение из вторичной конечной точки. Эта конфигурация уменьшает время, затрачиваемое на запрос изображения, так как основная конечная точка не запрашивается бесконечно.

```csharp
private static void OperationContextRetrying(object sender, RequestEventArgs e)
{
    retryCount++;
    Console.WriteLine("Retrying event because of failure reading the primary. RetryCount = " + retryCount);

    // Check if we have had more than n retries in which case switch to secondary.
    if (retryCount >= retryThreshold)
    {

        // Check to see if we can fail over to secondary.
        if (blobClient.DefaultRequestOptions.LocationMode != LocationMode.SecondaryOnly)
        {
            blobClient.DefaultRequestOptions.LocationMode = LocationMode.SecondaryOnly;
            retryCount = 0;
        }
        else
        {
            throw new ApplicationException("Both primary and secondary are unreachable. Check your application's network connection. ");
        }
    }
}
```

### <a name="request-completed-event-handler"></a>Обработчик события при успешном выполнении запроса

Обработчик события `OperationContextRequestCompleted` вызывается при успешном выполнении запроса. Если приложение использует вторичную конечную точку, приложение запрашивает эту конечную точку до 20 раз. После 20 раза приложение обратно устанавливает для параметра [LocationMode](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.locationmode?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_LocationMode) значение `PrimaryThenSecondary` и повторно запрашивает основную конечную точку. При успешном выполнении запроса приложение продолжает выполнять чтение из основной конечной точки.

```csharp
private static void OperationContextRequestCompleted(object sender, RequestEventArgs e)
{
    if (blobClient.DefaultRequestOptions.LocationMode == LocationMode.SecondaryOnly)
    {
        // You're reading the secondary. Let it read the secondary [secondaryThreshold] times,
        //    then switch back to the primary and see if it's available now.
        secondaryReadCount++;
        if (secondaryReadCount >= secondaryThreshold)
        {
            blobClient.DefaultRequestOptions.LocationMode = LocationMode.PrimaryThenSecondary;
            secondaryReadCount = 0;
        }
    }
}
```

# <a name="pythontabpython"></a>[Python](#tab/python)

### <a name="retry-event-handler"></a>Обработчик события при повторном запуске

Обработчик событий `retry_callback` вызывается, когда скачивание завершается с ошибкой и настроено на повторный запуск. Если достигнуто максимальное количество попыток, определенных для приложения, значение параметра [LocationMode](https://docs.microsoft.com/python/api/azure.storage.common.models.locationmode?view=azure-python) запроса изменяется на `SECONDARY`. В таком случае приложение принудительно повторно пытается скачать изображение из вторичной конечной точки. Эта конфигурация уменьшает время, затрачиваемое на запрос изображения, так как основная конечная точка не запрашивается бесконечно.

```python
def retry_callback(retry_context):
    global retry_count
    retry_count = retry_context.count
    sys.stdout.write("\nRetrying event because of failure reading the primary. RetryCount= {0}".format(retry_count))
    sys.stdout.flush()

    # Check if we have more than n-retries in which case switch to secondary
    if retry_count >= retry_threshold:

        # Check to see if we can fail over to secondary.
        if blob_client.location_mode != LocationMode.SECONDARY:
            blob_client.location_mode = LocationMode.SECONDARY
            retry_count = 0
        else:
            raise Exception("Both primary and secondary are unreachable. "
                            "Check your application's network connection.")
```

### <a name="request-completed-event-handler"></a>Обработчик события при успешном выполнении запроса

Обработчик события `response_callback` вызывается при успешном выполнении запроса. Если приложение использует вторичную конечную точку, приложение запрашивает эту конечную точку до 20 раз. После 20 раза приложение обратно устанавливает для параметра [LocationMode](https://docs.microsoft.com/python/api/azure.storage.common.models.locationmode?view=azure-python) значение `PRIMARY` и повторно запрашивает основную конечную точку. При успешном выполнении запроса приложение продолжает выполнять чтение из основной конечной точки.

```python
def response_callback(response):
    global secondary_read_count
    if blob_client.location_mode == LocationMode.SECONDARY:

        # You're reading the secondary. Let it read the secondary [secondaryThreshold] times,
        # then switch back to the primary and see if it is available now.
        secondary_read_count += 1
        if secondary_read_count >= secondary_threshold:
            blob_client.location_mode = LocationMode.PRIMARY
            secondary_read_count = 0
```

# <a name="java-v7-sdktabjava-v7"></a>[Пакет SDK для Java версии 7](#tab/java-v7)

В Java не нужно определять обработчики обратных вызовов, если для свойства **LocationMode** класса **BlobRequestOptions** задано значение **PRIMARY\_THEN\_SECONDARY**. Это позволяет приложению автоматически переключаться на вторичное расположение, если не удается перейти в первичное расположение при попытке скачать файл **HelloWorld.png**.

```java
    BlobRequestOptions myReqOptions = new BlobRequestOptions();
    myReqOptions.setRetryPolicyFactory(new RetryLinearRetry(deltaBackOff, maxAttempts));
    myReqOptions.setLocationMode(LocationMode.PRIMARY_THEN_SECONDARY);
    blobClient.setDefaultRequestOptions(myReqOptions);

    blob.downloadToFile(downloadedFile.getAbsolutePath(),null,blobClient.getDefaultRequestOptions(),opContext);
```

# <a name="java-v10-sdktabjava-v10"></a>[Пакет SDK для Java версии 10](#tab/java-v10)

При использовании пакета SDK для Java версии 10 не нужно определять обработчики обратных вызовов. Этот пакет содержит ряд фундаментальных отличий от версии 7. Вместо LocationMode у нас есть дополнительный **конвейер**. Вы можете определить дополнительный конвейер через **RequestRetryOptions**. Если он определен, приложение сможет автоматически переключаться на него, если ему не удается получить доступ к данным через основной конвейер.

```java
// We create pipeline options here so that they can be easily used between different pipelines
PipelineOptions myOptions = new PipelineOptions();
myOptions.withRequestRetryOptions(new RequestRetryOptions(RetryPolicyType.EXPONENTIAL, 3, 10, 500L, 1000L, accountName + "-secondary.blob.core.windows.net"));
// We are using a default pipeline here, you can learn more about it at https://github.com/Azure/azure-storage-java/wiki/Azure-Storage-Java-V10-Overview
final ServiceURL serviceURL = new ServiceURL(new URL("https://" + accountName + ".blob.core.windows.net"), StorageURL.createPipeline(creds, myOptions));
```

---

## <a name="next-steps"></a>Дополнительная информация

В первой части этого цикла вы узнали о том, как сделать приложение доступным с помощью учетных записей хранения RA-GRS.

Перейдите ко второй части серии, чтобы узнать, как имитировать сбой и заставить приложение использовать вторичную конечную точку RA-GRS.

> [!div class="nextstepaction"]
> [Simulate a failure in accessing read-access redundant storage](storage-simulate-failure-ragrs-account-app.md) (Имитация сбоя при подключении к геоизбыточному хранилищу с доступом на чтение)
