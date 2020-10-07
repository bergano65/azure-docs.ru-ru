---
title: Потоковая передача видеофайлов с помощью Служб мультимедиа Azure — Node.js | Документация Майкрософт
description: Выполните инструкции, приведенные в этом руководстве, чтобы создать учетную запись Служб мультимедиа Azure, закодировать файл и выполнить его потоковую передачу в Проигрыватель мультимедиа Azure.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
keywords: azure media services, stream
ms.service: media-services
ms.workload: media
ms.topic: tutorial
ms.custom: mvc, devx-track-js
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: 38207ac47d2e58c689f1933a6ade6d5c86cd44ad
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91268678"
---
# <a name="tutorial-encode-a-remote-file-based-on-url-and-stream-the-video---nodejs"></a>Руководство по кодированию удаленного файла на основе URL-адреса и потоковой передачи видео с помощью Node.js

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Из этого руководства вы узнаете, как без труда кодировать видео и запускать его потоковую передачу в разных браузерах и на разных устройствах с помощью Служб мультимедиа Azure. Содержимое входных данных можно указывать с помощью URL-адресов протоколов HTTP, URL-адресов SAS или путей к файлам, находящимся в хранилище BLOB-объектов Azure.

Пример в этой статье предназначен для кодирования содержимого, которое доступно через URL-адрес HTTPS. Обратите внимание, что в настоящее время AMS версии 3 не поддерживает кодирование блочной передачи по URL-адресам HTTPS.

Изучив это руководство, вы сможете выполнить потоковую передачу видео.  

![Воспроизведение видео](./media/stream-files-nodejs-quickstart/final-video.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Предварительные требования

- Установите [Node.js](https://nodejs.org/en/download/)
- [Создание учетной записи Служб мультимедиа](./create-account-howto.md).<br/>Запишите значения, которые вы использовали в качестве имени группы ресурсов и имени учетной записи Служб мультимедиа.
- Выполните действия, описанные в статье [Доступ к API Служб мультимедиа Azure с помощью Azure CLI](./access-api-howto.md), и сохраните учетные данные. Эти данные понадобятся для доступа к API.

## <a name="download-and-configure-the-sample"></a>Скачивание и настройка примера

Клонируйте репозиторий GitHub, содержащий пример потоковой передачи данных Node.js, на компьютер с помощью следующей команды:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-node-tutorials.git
 ```

Этот пример находится в папке [StreamFilesSample](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/master/AMSv3Samples/StreamFilesSample).

Откройте файл [index.js](https://github.com/Azure-Samples/media-services-v3-node-tutorials/blob/master/AMSv3Samples/StreamFilesSample/index.js#L25) в скачанном проекте. Замените значения `endpoint config` учетными данными, которые вы получили, выполнив действия из руководства по [получению доступа к API](./access-api-howto.md).

Этот пример выполняет следующие действия:

1. Создает **преобразование** (сначала проверяет, существует ли указанное преобразование). 
2. Создает выходной **ресурс**, который используется в качестве выходных данных **задания** кодирования.
3. Создает входные данные для **задания**, основанные на URL-адресе HTTPS.
4. Отправляет **задание** кодирования с использованием входных и выходных данных, созданных ранее.
5. проверка состояния задания;
6. Создает **указатель потоковой передачи**.
7. компиляция URL-адресов потоковой передачи.

## <a name="run-the-sample-app"></a>Запуск примера приложения

1. Это приложение скачивает закодированные файлы. Создайте папку для выходных файлов и измените значение переменной **outputFolder** в файле [index.js](https://github.com/Azure-Samples/media-services-v3-node-tutorials/blob/master/AMSv3Samples/StreamFilesSample/index.js#L39).
1. Откройте **командную строку**, перейдите в каталог с примером и выполните следующие команды.

    ```
    npm install 
    node index.js
    ```

Вы должны увидеть примерно такой результат:

![Снимок экрана: окно командной строки с результатами выполнения примера приложения StreamFileSample, в которых указаны URL-адреса трех файлах, скачанных в локальную папку](./media/stream-files-nodejs-quickstart/run.png)

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

Если вам больше не нужны ресурсы в группе ресурсов, включая Службы мультимедиа и учетные записи хранения, созданные в рамках этого руководства, удалите группу ресурсов.

Выполните следующую команду CLI:

```azurecli
az group delete --name amsResourceGroup
```

## <a name="see-also"></a>См. также раздел

[Коды ошибок задания](/rest/api/media/jobs/get#joberrorcode).

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Основные понятия служб мультимедиа Azure](concepts-overview.md)
