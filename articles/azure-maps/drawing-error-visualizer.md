---
title: Использование визуализатора ошибок рисования в Azure Maps
description: В этой статье представлены сведения о том, как визуализировать предупреждения и ошибки, возвращаемые API преобразования в Creator.
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/12/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: b3f9451a5ffd13c67232107d8db1e2da4a3891ec
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86524749"
---
# <a name="using-the-azure-maps-drawing-error-visualizer"></a>Использование визуализатора ошибок рисования в Azure Maps

Визуализатор ошибок рисования представляет собой изолированное веб-приложение, в котором отображаются [предупреждения и ошибки пакета рисования](drawing-conversion-error-codes.md), обнаруженные в процессе преобразования. Веб-приложение визуализатора ошибок содержит статическую страницу, которую можно использовать без подключения к Интернету.  С помощью визуализатора ошибок можно исправлять ошибки и устранять предупреждения в соответствии с [требованиями к пакету рисунков](drawing-requirements.md). [API преобразования в Azure Maps](https://docs.microsoft.com/rest/api/maps/conversion) возвращает ответ со ссылкой на визуализатор ошибок только в том случае, если обнаружена ошибка.

## <a name="prerequisites"></a>Предварительные требования

Перед скачиванием визуализатора ошибок рисования необходимо выполнить следующие действия:

1. [Создать учетную запись Azure Maps.](quick-demo-map-app.md#create-an-azure-maps-account)
2. [Получить первичный ключ подписки](quick-demo-map-app.md#get-the-primary-key-for-your-account), который иногда называется первичным ключом или ключом подписки.
3. [Создайте ресурс Creator](how-to-manage-creator.md).

В этом руководстве используется приложение [Postman](https://www.postman.com/), но вы можете выбрать другую среду разработки API.

## <a name="download"></a>Скачивание

1. Отправьте пакет рисования в службу Azure Maps Creator и получите `udid` для отправленного пакета. Инструкции по отправке пакета см. в [этой статье](tutorial-creator-indoor-maps.md#upload-a-drawing-package).

2. После отправки пакета рисунков мы применим к нему `udid`, чтобы преобразовать этот пакет в данные схемы. Инструкции по преобразованию пакета см. в [этой статье](tutorial-creator-indoor-maps.md#convert-a-drawing-package).

    >[!NOTE]
    >Если процесс преобразования завершится успешно, вы не получите ссылку на средство визуализации ошибок.

3. В приложении Postman на вкладке **Headers** (Заголовки) для отклика найдите свойство `diagnosticPackageLocation`, возвращаемое API преобразования. Этот ответ в формате JSON должен выглядеть примерно так:

    ```json
    {
        "operationId": "77dc9262-d3b8-4e32-b65d-74d785b53504",
        "created": "2020-04-22T19:39:54.9518496+00:00",
        "status": "Failed",
        "resourceLocation": "https://atlas.microsoft.com/conversion/{conversionId}?api-version=1.0",
        "properties": {
            "diagnosticPackageLocation": "https://atlas.microsoft.com/mapData/ce61c3c1-faa8-75b7-349f-d863f6523748?api-version=1.0"
        }
    }
    ```

4. Скачайте визуализатор ошибок пакета рисования, отправив запрос `HTTP-GET` по URL-адресу `diagnosticPackageLocation`.

## <a name="setup"></a>Настройка

В скачанном ZIP-пакете, который вы получите по ссылке `diagnosticPackageLocation`, есть два файла.

* _VisualizationTool.zip._ Содержит исходный код, мультимедийные ресурсы и веб-страницу для визуализатора ошибок рисования.
* _ConversionWarningsAndErrors.json._ Содержит форматированный список предупреждений, ошибок и дополнительных сведений, используемых визуализатором ошибок рисования.

Распакуйте папку _VisualizationTool.zip_. Она содержит следующие элементы:

* папка _assets_ с изображениями и файлами мультимедиа;
* папка _static_ с исходным кодом;
* файл _index.html_ с веб-приложением.

Откройте файл _index.html_ в любом из перечисленных ниже браузеров с учетом номера версии. Вы можете использовать другую версию, если она обеспечивает поведение, совместимое с указанной версией.

* Microsoft Edge 80;
* Safari 13;
* Chrome 80;
* Firefox 74.

## <a name="using-the-drawing-error-visualizer-tool"></a>Использование средства визуализации ошибок рисования

После запуска средства визуализации ошибок рисования отобразится страница отправки. На этой странице есть поле для перетаскивания. Это же поле для перетаскивания выполняет и роль кнопки, которая запускает диалоговое окно проводника.

:::image type="content" source="./media/drawing-errors-visualizer/start-page.png" alt-text="Приложение визуализатора ошибок рисования — начальная страница":::

Файл _ConversionWarningsAndErrors.json_ помещен в корень папки downloaded. Чтобы передать файл _ConversionWarningsAndErrors.json_, можно перетащить его в соответствующее поле либо щелкнуть поле и найти файл в диалоговом окне проводника.

:::image type="content" source="./media/drawing-errors-visualizer/loading-data.gif" alt-text="Приложение визуализатора ошибок рисования — перетаскивание для загрузки данных":::

Когда завершится отправка файла _ConversionWarningsAndErrors.json_, вы увидите список ошибок и предупреждений пакета рисунков. Для всех ошибок и предупреждений указывается информация о слое и уровне, а также текст сообщения. Чтобы просмотреть подробные сведения об ошибке или предупреждении, щелкните ссылку **сведения** . После этого отобразится подраздел, который можно отозвать. Вы можете перейти к любой ошибке, чтобы получить дополнительные сведения о ее устранении.

:::image type="content" source="./media/drawing-errors-visualizer/errors.png" alt-text="Приложение визуализатора ошибок рисования — ошибки и предупреждения":::

## <a name="next-steps"></a>Дальнейшие действия

Когда [пакет рисунков будет соответствовать всем требованиям](drawing-requirements.md), вы можете преобразовать этот пакет рисунков в набор данных с помощью [службы наборов данных в Azure Maps](https://docs.microsoft.com/rest/api/maps/conversion). После этого можно перейти в веб-модуль схем помещений для разработки приложения. Дополнительные сведения см. в следующих статьях:

> [!div class="nextstepaction"]
> [Коды ошибок в пакете преобразования рисунков](drawing-conversion-error-codes.md)

> [!div class="nextstepaction"]
> [Creator для схем помещений](creator-indoor-maps.md)

> [!div class="nextstepaction"]
> [Использование модуля схем помещений](how-to-use-indoor-module.md)

> [!div class="nextstepaction"]
> [Реализация динамических стилей для схем помещений](indoor-map-dynamic-styling.md)