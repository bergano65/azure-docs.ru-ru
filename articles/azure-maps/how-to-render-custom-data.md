---
title: Рендеринг пользовательских данных на карте raster Карты Microsoft Azure
description: В этой статье вы узнаете, как сделать пользовательские данные на карте raster с помощью службы статического изображения Microsoft Azure Maps.
author: philmea
ms.author: philmea
ms.date: 01/23/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: b8d47b69b4aba14c86fb09176b662aee7d5482d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335523"
---
# <a name="render-custom-data-on-a-raster-map"></a>Рендеринг пользовательских данных на карте raster

В этой статье объясняется, как использовать [службу статического изображения](https://docs.microsoft.com/rest/api/maps/render/getmapimage)с функциональностью композиции изображения, чтобы позволить наложения на верхней части карты raster. Композиция изображения включает в себя возможность получить raster плитки обратно, с дополнительными данными, как пользовательские pushpins, этикетки, и геометрии наложения.

Для визуализации пользовательских накладок pushpins, меток и геометрии можно использовать приложение Postman. Для хранения и визуализации накладок можно использовать [API службы данных](https://docs.microsoft.com/rest/api/maps/data) Azure Maps.

> [!Tip]
> Часто гораздо более рентабельно использовать Web SDK Azure Maps Web sDK для отображения простой карты на веб-странице, чем для использования службы статического изображения. Веб SDK использует плитки карты, и если пользователь не панорамирует и не масштабирует карту, они часто генерируют только часть транзакции на нагрузку карты. Обратите внимание, что веб-SDK Azure Maps имеет варианты отключения панорамирования и масштабирования. Кроме того, веб-SDK Azure Maps предоставляет более широкий набор вариантов визуализации данных, чем веб-служба статичной карты.  

## <a name="prerequisites"></a>Предварительные требования

### <a name="create-an-azure-maps-account"></a>создание учетной записи службы Azure Maps

Для завершения процедур в этой статье сначала необходимо создать учетную запись Azure Maps и получить ключ к учетной записи карт. Следуйте инструкциям в [создании учетной записи](quick-demo-map-app.md#create-an-account-with-azure-maps) для создания подписки на учетную запись Azure Maps и следуйте инструкциям по [созданию основного ключа](quick-demo-map-app.md#get-the-primary-key-for-your-account) для получения основного ключа для вашей учетной записи. Дополнительные сведения о проверке подлинности в Azure Maps см. в [этой статье](./how-to-manage-authentication.md).


## <a name="render-pushpins-with-labels-and-a-custom-image"></a>Рендер pushpins с этикетками и пользовательским изображением

> [!Note]
> Процедура в этом разделе требует учетной записи Azure Maps в ценовом уровне S0 или S1.

Уровень учетной записи Azure Maps S0 поддерживает только один экземпляр `pins` параметра. Это позволяет визуализировать до пяти pushpins, указанных в запросе URL, с пользовательским изображением.

Чтобы визуализировать pushpins с метками и пользовательским изображением, выполните следующие действия:

1. Создайте коллекцию для хранения запросов. В приложении Почтальон выберите **Новый**. В **окне «Создайте новое»** выберите **Collection**. Назовите коллекцию и выберите кнопку **«Создание».** 

2. Чтобы создать запрос, выберите **Новый** снова. В окне **«Создать новое»** выберите **Запрос**. Введите **имя запроса** для pushpins. Выберите коллекцию, созданную на предыдущем этапе, в качестве местоположения для сохранения запроса. Затем выберите **Сохранить**.
    
    ![Создать запрос в Postman](./media/how-to-render-custom-data/postman-new.png)

3. Выберите метод GET HTTP на вкладке builder и введите следующий URL для создания запроса GET.

    ```HTTP
    https://atlas.microsoft.com/map/static/png?subscription-key={subscription-key}&api-version=1.0&layer=basic&style=main&zoom=12&center=-73.98,%2040.77&pins=custom%7Cla15+50%7Cls12%7Clc003b61%7C%7C%27CentralPark%27-73.9657974+40.781971%7C%7Chttps%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FAzureMapsCodeSamples%2Fmaster%2FAzureMapsCodeSamples%2FCommon%2Fimages%2Ficons%2Fylw-pushpin.png
    ```
    Вот полученное изображение:

    ![Пользовательский pushpin с этикеткой](./media/how-to-render-custom-data/render-pins.png)


## <a name="get-data-from-azure-maps-data-storage"></a>Получение данных из хранилища данных Azure Maps

> [!Note]
> Процедура в этом разделе требует учетной записи Azure Maps в ценовом уровне S1.

Вы также можете получить информацию о месте пути и контакта, используя [API загрузки данных.](https://docs.microsoft.com/rest/api/maps/data/uploadpreview) Выполните следующие действия, чтобы отправить данные пути и кнопок.

1. В приложении Postman откройте новую вкладку в коллекции, созданной в предыдущем разделе. Выберите метод POST HTTP на вкладке builder и введите следующий URL, чтобы сделать запрос POST:

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={subscription-key}&api-version=1.0&dataFormat=geojson
    ```

2. На вкладке **Params** введите следующие пары ключей/значений, которые используются для URL-адреса запроса POST. Замените `subscription-key` значение ключом подписки Azure Maps.
    
    ![Ключевые/значение params в Почтальон](./media/how-to-render-custom-data/postman-key-vals.png)

3. На вкладке **Body** выберите формат необработанного ввода и выберите JSON в качестве формата ввода из списка выпадающих данных. Предоставьте этот JSON в качестве данных, которые будут загружены:
    
    ```JSON
    {
      "type": "FeatureCollection",
      "features": [
        {
          "type": "Feature",
          "properties": {},
          "geometry": {
            "type": "Polygon",
            "coordinates": [
              [
                [
                  -73.98235,
                  40.76799
                ],
                [
                  -73.95785,
                  40.80044
                ],
                [
                  -73.94928,
                  40.7968
                ],
                [
                  -73.97317,
                  40.76437
                ],
                [
                  -73.98235,
                  40.76799
                ]
              ]
            ]
          }
        },
        {
          "type": "Feature",
          "properties": {},
          "geometry": {
            "type": "LineString",
            "coordinates": [
              [
                -73.97624731063843,
                40.76560773817073
              ],
              [
                -73.97914409637451,
                40.766826609362575
              ],
              [
                -73.98513078689575,
                40.7585866048861
              ]
            ]
          }
        }
      ]
    }
    ```

4. Выберите **Отправить** и просмотреть заголовок ответа. Если запрос выполнен успешно, заголовок Location будет содержать URI для проверки текущего состояния запроса на отправку. Статус URI будет следующего формата.  

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0
   ```

5. Копируйте свой статус URI и привяжите к нему параметр подписки с значением ключа подписки Azure Maps. Используйте тот же ключ подписки учетной записи, который использовался для загрузки данных. Формат статуса URI должен выглядеть как формат, приведенный ниже:

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0&subscription-key={Subscription-key}
   ```

6. Чтобы получить udId, откройте новую вкладку в приложении Почтальон. Выберите метод GET HTTP на вкладке builder. Сделайте запрос GET в статусе URI. Если загрузка данных прошла успешно, вы получите udId в органе реагирования. Копируйте udId.

   ```JSON
   {
      "udid" : "{udId}"
   }
   ```

7. Используйте `udId` значение, полученное от API загрузки данных, для визуализации объектов на карте. Для этого откройте новую вкладку в созданной вами коллекции в предыдущем разделе. Выберите метод GET HTTP на вкладке builder, замените «ключ-подписка» и «udId» на ваши значения и введите этот URL,чтобы сделать запрос GET:

    ```HTTP
    https://atlas.microsoft.com/map/static/png?subscription-key={subscription-key}&api-version=1.0&layer=basic&style=main&zoom=12&center=-73.96682739257812%2C40.78119135317995&pins=default|la-35+50|ls12|lc003C62|co9B2F15||'Times Square'-73.98516297340393 40.758781646381024|'Central Park'-73.96682739257812 40.78119135317995&path=lc0000FF|fc0000FF|lw3|la0.80|fa0.30||udid-{udId}
    ```

    Вот изображение ответа:

    ![Получение данных из хранилища данных Azure Maps](./media/how-to-render-custom-data/uploaded-path.png)

## <a name="render-a-polygon-with-color-and-opacity"></a>Рендеринг полигонс и непрозрачность

> [!Note]
> Процедура в этом разделе требует учетной записи Azure Maps в ценовом уровне S1.


Вы можете изменить внешний вид многоугольника с помощью модификаторов стиля с [параметром пути](https://docs.microsoft.com/rest/api/maps/render/getmapimage#uri-parameters).

1. В приложении Postman откройте новую вкладку в созданной ранее коллекции. Выберите метод GET HTTP на вкладке builder и введите следующий URL для настройки запроса GET для визуализации полигона с цветом и непрозрачностью:
    
    ```HTTP
    https://atlas.microsoft.com/map/static/png?api-version=1.0&style=main&layer=basic&sku=S1&zoom=14&height=500&Width=500&center=-74.040701, 40.698666&path=lc0000FF|fc0000FF|lw3|la0.80|fa0.50||-74.03995513916016 40.70090237454063|-74.04082417488098 40.70028420372218|-74.04113531112671 40.70049568385827|-74.04298067092896 40.69899904076542|-74.04271245002747 40.69879568992435|-74.04367804527283 40.6980961582905|-74.04364585876465 40.698055487620714|-74.04368877410889 40.698022951066996|-74.04168248176573 40.696444909137|-74.03901100158691 40.69837271818651|-74.03824925422668 40.69837271818651|-74.03809905052185 40.69903971085914|-74.03771281242369 40.699340668780984|-74.03940796852112 40.70058515602143|-74.03948307037354 40.70052821920425|-74.03995513916016 40.70090237454063
    &subscription-key={subscription-key}
    ```

    Вот изображение ответа:

    ![Рендеринг непрозрачный полигон](./media/how-to-render-custom-data/opaque-polygon.png)


## <a name="render-a-circle-and-pushpins-with-custom-labels"></a>Рендеринг круг и pushpins с пользовательскими этикетками

> [!Note]
> Процедура в этом разделе требует учетной записи Azure Maps в ценовом уровне S1.


Вы можете изменить внешний вид контактов, добавив модификаторы стиля. Например, чтобы сделать pushpins и их этикетки больше или меньше, используйте `sc` модификатор "масштаб стиля". Этот модификатор принимает значение, превышающее ноль. Значение 1 — это стандартный масштаб. Значения, превышающие 1, будут увеличивать кнопки, а меньше 1 будут их уменьшать. Для получения дополнительной [информации](https://docs.microsoft.com/rest/api/maps/render/getmapimage#uri-parameters)о модификаторах стиля см.


Выполните следующие действия, чтобы визуализировать круг и pushpins с пользовательскими метками:

1. В приложении Postman откройте новую вкладку в созданной ранее коллекции. Выберите метод GET HTTP на вкладке builder и введите этот URL, чтобы сделать запрос GET:

    ```HTTP
    https://atlas.microsoft.com/map/static/png?api-version=1.0&style=main&layer=basic&zoom=14&height=700&Width=700&center=-122.13230609893799,47.64599069048016&path=lcFF0000|lw2|la0.60|ra1000||-122.13230609893799 47.64599069048016&pins=default|la15+50|al0.66|lc003C62|co002D62||'Microsoft Corporate Headquarters'-122.14131832122801  47.64690503939462|'Microsoft Visitor Center'-122.136828 47.642224|'Microsoft Conference Center'-122.12552547454833 47.642940335653996|'Microsoft The Commons'-122.13687658309935  47.64452336193245&subscription-key={subscription-key}
    ```

    Вот изображение ответа:

    ![Рендеринг круг с пользовательскими pushpins](./media/how-to-render-custom-data/circle-custom-pins.png)

2. Чтобы изменить цвет pushpins с последнего шага, измените модификатор стиля "co". Посмотрите `pins=default|la15+50|al0.66|lc003C62|co002D62|`на , текущий цвет будет указан как #002D62 в CSS. Допустим, вы хотите изменить его на #41d42a. Напишите новое цветовое значение после спецификации "co", как это: `pins=default|la15+50|al0.66|lc003C62|co41D42A|`. Сделайте новый запрос GET:

    ```HTTP
    https://atlas.microsoft.com/map/static/png?api-version=1.0&style=main&layer=basic&zoom=14&height=700&Width=700&center=-122.13230609893799,47.64599069048016&path=lcFF0000|lw2|la0.60|ra1000||-122.13230609893799 47.64599069048016&pins=default|la15+50|al0.66|lc003C62|co41D42A||'Microsoft Corporate Headquarters'-122.14131832122801  47.64690503939462|'Microsoft Visitor Center'-122.136828 47.642224|'Microsoft Conference Center'-122.12552547454833 47.642940335653996|'Microsoft The Commons'-122.13687658309935  47.64452336193245&subscription-key={subscription-key}
    ```

    Вот изображение ответа после изменения цвета контактов:

    ![Рендеринг круг с обновленными pushpins](./media/how-to-render-custom-data/circle-updated-pins.png)

Аналогичным образом можно изменить, добавить и удалить другие модификаторы стиля.

## <a name="next-steps"></a>Дальнейшие действия


* Ознакомьтесь с документацией по [API получения изображения карты в службе Azure Maps](https://docs.microsoft.com/rest/api/maps/render/getmapimage).
* Чтобы узнать больше об службе [service documentation](https://docs.microsoft.com/rest/api/maps/data)данных Azure Maps, см.

