---
title: Потоковая передача видеофайлов с помощью Служб мультимедиа Azure — .NET | Документация Майкрософт
description: Следуйте инструкциям этого краткого руководства, чтобы создать учетную запись Служб мультимедиа Azure, закодировать файл и передать его потоком в Проигрыватель мультимедиа Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
keywords: azure media services, stream
ms.service: media-services
ms.workload: media
ms.topic: quickstart
ms.custom: mvc
ms.date: 02/20/2019
ms.author: juliako
ms.openlocfilehash: 2556942119fdd4f58f6bd47bc88160dd2e13753d
ms.sourcegitcommit: 7723b13601429fe8ce101395b7e47831043b970b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/21/2019
ms.locfileid: "56585566"
---
# <a name="quickstart-stream-video-files---net"></a>Краткое руководство. Потоковая передача видеофайлов с помощью .NET

Это краткое руководство показывает, как можно легко кодировать и начинать потоковую передачу видео в разных браузерах и на разных устройствах с помощью Служб мультимедиа Azure. Содержимое входных данных можно указывать с помощью URL-адресов протоколов HTTP, URL-адресов SAS или путей к файлам, находящимся в хранилище BLOB-объектов Azure.
Пример в этой статье предназначен для кодирования содержимого, которое доступно через URL-адрес HTTPS. Обратите внимание, что в настоящее время AMS версии 3 не поддерживает кодирование блочной передачи по URL-адресам HTTPS.

Изучив это краткое руководство, вы сможете выполнить потоковую передачу видео.  

![Воспроизведение видео](./media/stream-files-dotnet-quickstart/final-video.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Предварительные требования

- Вы можете скачать [Visual Studio Community 2017](https://www.visualstudio.com/thank-you-downloading-visual-studio/?sku=Community&rel=15) бесплатно, если у вас нет Visual Studio.
- [Создание учетной записи Служб мультимедиа](create-account-cli-how-to.md).<br/>Запишите значения, которые вы использовали в качестве имени группы ресурсов и имени учетной записи Служб мультимедиа.
- Выполните действия, описанные в статье [Доступ к API Служб мультимедиа Azure с помощью Azure CLI](access-api-cli-how-to.md), и сохраните учетные данные. Эти данные понадобятся для доступа к API.

## <a name="download-and-configure-the-sample"></a>Скачивание и настройка примера

Клонируйте репозиторий GitHub, содержащий пример потоковой передачи данных .NET, на компьютер с помощью следующей команды:  

 ```bash
 git clone http://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts.git
 ```

Этот пример находится в папке [EncodeAndStreamFiles](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/tree/master/AMSV3Quickstarts/EncodeAndStreamFiles).

Откройте файл [appsettings.json](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/appsettings.json) в скачанном проекте. Замените значения учетными данными, которые вы получили, выполнив действия из статьи [Доступ к API Служб мультимедиа Azure с помощью Azure CLI](access-api-cli-how-to.md).

Этот пример выполняет следующие действия:

1. Создает **преобразование** (сначала проверяет, существует ли указанное преобразование). 
2. Создает выходной **ресурс**, который используется в качестве выходных данных **задания** кодирования.
3. Создает входные данные для **задания**, основанные на URL-адресе HTTPS.
4. Отправляет **задание** кодирования с использованием входных и выходных данных, созданных ранее.
5. проверка состояния задания;
6. Создает **указатель потоковой передачи**.
7. компиляция URL-адресов потоковой передачи.

Чтобы получить сведения о назначении всех функций в образце, ознакомьтесь с кодом и просмотрите комментарии в [этом исходном файле](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs).

## <a name="run-the-sample-app"></a>Запуск примера приложения

После запуска приложения отображаются URL-адреса, которые можно использовать для воспроизведения видео с помощью различных протоколов. 

1. Нажмите клавиши CTRL+F5, чтобы выполнить приложение *EncodeAndStreamFiles*.
2. Выберите протокол **HLS** Apple (заканчивается на *manifest(format=m3u8-aapl)*) и скопируйте URL-адрес потоковой передачи в консоли.

![Выходные данные](./media/stream-files-tutorial-with-api/output.png)

В [исходном коде](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs) образца вы увидите, каким образом создан URL-адрес. Чтобы создать его, необходимо сцепить имя узла конечной точки потоковой передачи и путь указателя потоковой передачи.  

## <a name="test-with-azure-media-player"></a>Тестирование с помощью Проигрывателя мультимедиа Azure

Для тестирования потоковой передачи в этой статье используется Проигрыватель мультимедиа Azure. 

> [!NOTE]
> Если проигрыватель размещен на сайте HTTPS, обновите URL-адрес до HTTPS.

1. Откройте браузер и перейдите по ссылке [https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/).
2. В поле **URL:** (URL-адрес:) вставьте одно из значений URL-адресов потоковой передачи, полученных при работе приложения. 
 
     URL-адрес можно указать в формате HLS, Dash или Smooth, а Проигрыватель мультимедиа Azure автоматически выберет соответствующий протокол потоковой передачи для воспроизведения на устройстве.
3. Щелкните **Update Player** (Обновить проигрыватель).

Проигрыватель мультимедиа Azure можно использовать для тестирования, но его нельзя применять в рабочей среде. 

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вам больше не нужны ресурсы в группе ресурсов, включая Службы мультимедиа и учетные записи хранения, созданные в рамках этого краткого руководства, удалите группу ресурсов.

Выполните следующую команду CLI:

```azurecli
az group delete --name amsResourceGroup
```

## <a name="examine-the-code"></a>Анализ кода

Чтобы получить сведения о назначении всех функций в образце, ознакомьтесь с кодом и просмотрите комментарии в [этом исходном файле](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs).

В руководстве по [передаче, кодированию и потоковой передаче файлов](stream-files-tutorial-with-api.md) представлен более сложный пример потоковой передачи и подробные пояснения. 

### <a name="job-error-codes"></a>Коды ошибок задания

См. статью о [кодах ошибок](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode).

## <a name="multithreading"></a>Многопоточность

Пакеты SDK для Служб мультимедиа Azure версии 3 не являются потокобезопасными. При работе с многопоточным приложением необходимо создать объект AzureMediaServicesClient для каждого потока.

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Руководство. Отправка, кодирование и потоковая передача видео с помощью API](stream-files-tutorial-with-api.md)
