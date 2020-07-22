---
title: Настройка конечных веб-точек (Предварительная версия)
titleSuffix: Azure Cognitive Services
description: Настройка конечных веб-точек для пользовательских команд
services: cognitive-services
author: xiaojul
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: xiaojul
ms.openlocfilehash: 0197bb81fdba8bab20742d95aebaa2028bb90c18
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/07/2020
ms.locfileid: "86027687"
---
# <a name="set-up-web-endpoints"></a>Настройка конечных веб-точек

В этой статье вы узнаете, как настроить конечные веб-точки в приложении настраиваемых команд, которое позволяет выполнять HTTP-запросы из клиентского приложения. Вам предстоит выполнить следующие задачи:

- Настройка конечных веб-точек в приложении пользовательских команд
- Вызов конечных веб-точек в приложении пользовательских команд
- Получение ответа на веб-конечные точки 
- Интегрируйте ответ веб-конечных точек в пользовательские полезные данные JSON, отправляйте и визуализируйте его из клиентского приложения SDK для Windows UWP для распознавания речи

## <a name="prerequisites"></a>Предварительные требования
> [!div class = "checklist"]
> * [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)
> * Ключ подписки Azure для службы речи: [получите бесплатную](get-started.md) или создайте ее на [портал Azure](https://portal.azure.com)
> * Ранее [созданное приложение пользовательских команд](quickstart-custom-commands-application.md)
> * Клиентское приложение с включенным речевым ПАКЕТом: [практические руководства: завершение действий с клиентским приложением](./how-to-custom-commands-setup-speech-sdk.md)

## <a name="setup-web-endpoints"></a>Настройка конечных веб-точек

1. Откройте созданное ранее приложение пользовательских команд. 
1. Перейдите в раздел "веб-конечные точки" и щелкните "создать конечную точку веб-сайта".

   > [!div class="mx-imgBorder"]
   > ![Новая конечная точка веб-сайта](media/custom-commands/setup-web-endpoint-new-endpoint.png)

   | Параметр | Рекомендуемое значение | Описание |
   | ------- | --------------- | ----------- |
   | name | упдатедевицестате | Имя для конечной веб-точки. |
   | URL-адрес | https://webendpointexample.azurewebsites.net/api/DeviceState | URL-адрес конечной точки, с которой вы хотите связаться с пользовательским командным приложением. |
   | Метод | POST | Разрешенные взаимодействия (например, GET, POST) с конечной точкой.|
   | Заголовки | Ключ: приложение, значение: Возьмите первые 8 цифр в идентификатор приложения. | Параметры заголовка для включения в заголовок запроса.|

    > [!NOTE]
    > - Пример веб-конечной точки, созданной с помощью [функции Azure](https://docs.microsoft.com/azure/azure-functions/), которая подключается к базе данных, сохраняющей состояние устройства телевизора и вентилятора.
    > - Предлагаемый заголовок необходим только для конечной точки примера.
    > - Чтобы убедиться, что значение заголовка уникально в нашем примере конечной точки, Возьмите первые 8 разрядов приложения
    > - В реальном мире конечная точка веб-сайта может быть конечной точкой [центра Интернета вещей](https://docs.microsoft.com/azure/iot-hub/about-iot-hub) , управляющего Вашими устройствами.

1. Нажмите кнопку **Сохранить**.

## <a name="call-web-endpoints"></a>Вызов конечных веб-точек

1. Перейдите к команде **турнонофф** , выберите **конфирматионреспонсе** в разделе правило завершения, а затем щелкните **Добавить действие**.
1. В разделе **новый тип действия**выберите **вызвать конечную веб-точку** .
1. В поле **изменить действие — конечные точки**выберите **упдатедевицестате**, то есть созданная конечная точка веб-сайта.  
1. В поле **Конфигурация**введите следующие значения: 
   > [!div class="mx-imgBorder"]
   > ![Параметры действия вызова конечных веб-точек](media/custom-commands/setup-web-endpoint-edit-action-parameters.png)

   | Параметр | Рекомендуемое значение | Описание |
   | ------- | --------------- | ----------- |
   | Конечные точки | упдатедевицестате | Конечная веб-точка, которую вы хотите вызвать в этом действии. |
   | Параметры запроса | элемент = {Субжектдевице} &&значение = {Онофф} | Параметры запроса, добавляемые в URL-адрес конечной точки.  |
   | Содержимое текста | Н/Д | Содержимое текста запроса. |

    > [!NOTE]
    > - Предлагаемые параметры запроса требуются только для конечной точки примера.

1. В списке **успешно — действие для выполнения**выберите **Отправить речевое реагирование**.
    
    В **простом редакторе**введите `{SubjectDevice} is {OnOff}` .
   
   > [!div class="mx-imgBorder"]
   > ![Вызов действия конечных веб-точек в случае успеха](media/custom-commands/setup-web-endpoint-edit-action-on-success-send-response.png)

   | Параметр | Рекомендуемое значение | Описание |
   | ------- | --------------- | ----------- |
   | Действие для выполнения | Отправка речевого ответа | Действие, выполняемое, если запрос к конечной точке веб-сайта выполнен |
   
   > [!NOTE]
   > - Можно также напрямую обращаться к полям в HTTP-ответе с помощью `{YourWebEndpointName.FieldName}` . Например: `{UpdateDeviceState.TV}`

1. В **меню сбой — действие для выполнения**выберите команду **Отправить речевое ответ** .

    В **простом редакторе**введите `Sorry, {WebEndpointErrorMessage}` .

   > [!div class="mx-imgBorder"]
   > ![Вызов действия конечных веб-точек в случае сбоя](media/custom-commands/setup-web-endpoint-edit-action-on-fail.png)

   | Параметр | Рекомендуемое значение | Описание |
   | ------- | --------------- | ----------- |
   | Действие для выполнения | Отправка речевого ответа | Действие, выполняемое при сбое запроса к веб-конечной точке |

   > [!NOTE]
   > - Аргумент `{WebEndpointErrorMessage}` является необязательным. Вы можете удалить его, если не хотите предоставлять сообщение об ошибке.
   > - В нашем примере конечной точки мы отправляем обратный HTTP-ответ с подробными сообщениями об ошибках для распространенных ошибок, таких как отсутствующие параметры заголовка. 

### <a name="try-it-out-in-test-portal"></a>Попробуйте на портале тестирования
- При успешном завершении \
Сохранение, обучение и тестирование
   > [!div class="mx-imgBorder"]
   > ![Вызов действия конечных веб-точек в случае успеха](media/custom-commands/setup-web-endpoint-on-success-response.png)
- При сбое ответа \
Удаление одного из параметров запроса, сохранение, переобучение и тестирование
   > [!div class="mx-imgBorder"]
   > ![Вызов действия конечных веб-точек в случае успеха](media/custom-commands/setup-web-endpoint-on-fail-response.png)

## <a name="integrate-with-client-application"></a>Интеграция с клиентским приложением

В окне [Пошаговое руководство. Отправка действия в клиентское приложение (Предварительная версия)](./how-to-custom-commands-send-activity-to-client.md)Добавлено действие **Send в действие клиента** . Действие отправляется клиентскому приложению независимо от того, является ли **вызов конечной точки веб-узла** успешным или нет.
Однако в большинстве случаев вы хотите отправить действие в клиентское приложение только после успешного вызова конечной веб-точки. В этом примере это происходит, когда состояние устройства успешно обновляется.

1. Удалите действие **Send (отправить) к** добавленному ранее действию клиента.
1. Изменить конечную веб-точку вызова: 
    1. Убедитесь, что в **конфигурации**в **параметрах запроса** задано`item={SubjectDevice}&&value={OnOff}`
    1. В случае **успешного**выполнения измените **действие** , чтобы выполнить для **отправки действия клиенту** .
    1. Скопируйте приведенный ниже код JSON в **содержимое действия** .
   ```json
   {
     "type": "event",
     "name": "UpdateDeviceState",
     "state": "{OnOff}",
     "device": "{SubjectDevice}"
   }
   ```
    > [!div class="mx-imgBorder"]
    > ![Отправка действия при успешном выполнении](media/custom-commands/setup-web-endpoint-edit-action-on-success-send-activity.png)
   
Теперь вы отправляете действие клиенту только при успешном выполнении запроса к конечной точке веб-сайта.

### <a name="create-visuals-for-syncing-device-state"></a>Создание визуальных элементов для синхронизации состояния устройства
Добавьте следующий код XML к `MainPage.xaml` `"EnableMicrophoneButton"` блоку.

```xml
<Button x:Name="SyncDeviceStateButton" Content="Sync Device State"
        Margin="0,10,10,0" Click="SyncDeviceState_ButtonClicked"
        Height="35"/>
<Button x:Name="EnableMicrophoneButton" ......
        .........../>
```

### <a name="sync-device-state"></a>Синхронизировать состояние устройства 

В `MainPage.xaml.cs` добавьте ссылку `using Windows.Web.Http;` . Добавьте в класс `MainPage` приведенный далее код. Этот метод отправит запрос GET в конечную точку примера и извлечет текущее состояние устройства для приложения. Не забудьте изменить `<your_app_name>` на то, что использовалось в **заголовке** в конечной веб-точке пользовательской команды

```C#
private async void SyncDeviceState_ButtonClicked(object sender, RoutedEventArgs e)
{
    //Create an HTTP client object
    var httpClient = new HttpClient();

    //Add a user-agent header to the GET request. 
    var your_app_name = "<your-app-name>";

    Uri endpoint = new Uri("https://webendpointexample.azurewebsites.net/api/DeviceState");
    var requestMessage = new HttpRequestMessage(HttpMethod.Get, endpoint);
    requestMessage.Headers.Add("app", $"{your_app_name}");

    try
    {
        //Send the GET request
        var httpResponse = await httpClient.SendRequestAsync(requestMessage);
        httpResponse.EnsureSuccessStatusCode();
        var httpResponseBody = await httpResponse.Content.ReadAsStringAsync();
        dynamic deviceState = JsonConvert.DeserializeObject(httpResponseBody);
        var TVState = deviceState.TV.ToString();
        var FanState = deviceState.Fan.ToString();
        await CoreApplication.MainView.CoreWindow.Dispatcher.RunAsync(
            CoreDispatcherPriority.Normal,
            () =>
            {
                State_TV.Text = TVState;
                State_Fan.Text = FanState;
            });
    }
    catch (Exception ex)
    {
        NotifyUser(
            $"Unable to sync device status: {ex.Message}");
    }
}
```

## <a name="try-it-out"></a>Попробуйте!

1. Запуск приложения
1. Щелкните синхронизировать состояние устройства.
Если вы проверили приложение с помощью `turn on tv` в предыдущем разделе, ТВ-передачи будут отображаться как "вкл.".
    > [!div class="mx-imgBorder"]
    > ![Синхронизировать состояние устройства](media/custom-commands/setup-web-endpoint-sync-device-state.png)
1. Выберите включить микрофон
1. Нажмите кнопку "разговор"
1. Пишут`turn on the fan`
1. Визуальное состояние вентилятора должно измениться на "вкл."
    > [!div class="mx-imgBorder"]
    > ![Включить вентилятор](media/custom-commands/setup-web-endpoint-turn-on-fan.png)

## <a name="next-steps"></a>Дальнейшие шаги

> [!div class="nextstepaction"]
> [Включение процесса непрерывной интеграции и чтения для приложения пользовательских команд](./how-to-custom-commands-deploy-cicd.md)