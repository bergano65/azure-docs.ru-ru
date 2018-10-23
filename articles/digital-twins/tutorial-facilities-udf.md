---
title: Мониторинг пространства с помощью Azure Digital Twins | Документация Майкрософт
description: В этом руководстве вы узнаете, как подготовить пространственные ресурсы и контролировать условия работы с помощью Azure Digital Twins.
services: digital-twins
author: dsk-2015
ms.service: digital-twins
ms.topic: tutorial
ms.date: 10/15/2018
ms.author: dkshir
ms.openlocfilehash: 1e5cb18b4e526cd0a0607f5bc93788fcf07430e1
ms.sourcegitcommit: 6361a3d20ac1b902d22119b640909c3a002185b3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/17/2018
ms.locfileid: "49364241"
---
# <a name="tutorial-provision-your-building-and-monitor-working-conditions-with-azure-digital-twins"></a>Руководство по подготовке примера здания и мониторинге условий работы с помощью Azure Digital Twins

В этом руководстве описано, как использовать Azure Digital Twins для отслеживания желаемых температурных условий и уровня комфорта в необходимых пространствах. [Настроив пример задания](tutorial-facilities-setup.md), вы можете подготовить его и запустить пользовательские функции на основе данных датчика, используя шаги в этом руководстве.

Из этого руководства вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * определение условий для мониторинга;
> * создание определяемой пользователем функции;
> * моделирование данных датчика;
> * получение результатов выполнения определяемой пользователем функции.

## <a name="prerequisites"></a>Предварительные требования

В этом руководстве предполагается, что вы [настроили экземпляр Azure Digital Twins](tutorial-facilities-setup.md). Прежде чем продолжить, убедитесь, что у вас есть:
- [Учетная запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Работающий экземпляр Digital Twins. 
- [Примеры Digital Twins на C#](https://github.com/Azure-Samples/digital-twins-samples-csharp), скачанные и извлеченные на ваш рабочий компьютер. 
- [Пакет SDK для .NET Core версии 2.1.403 или более поздней](https://www.microsoft.com/net/download) на компьютере разработки. Он нужен для создания и запуска примера. Выполните команду `dotnet --version`, чтобы проверить, установлена ли необходимая версия. 
- [Visual Studio Code](https://code.visualstudio.com/) для изучения примера кода. 

## <a name="define-conditions-to-monitor"></a>Определение условий для мониторинга
Вы можете определить набор конкретных условий, называемых **сопоставителями** (Matcher), которые нужно отслеживать в данных устройств или датчиков. Затем вы можете определить *определяемые пользователем функции*, которые выполняют пользовательскую логику для данных, поступающих из ваших пространств и устройств, когда выполняются условия, указанные сопоставителями. Дополнительные сведения см. в статье [Обработка данных и определяемые пользователем функции](concepts-user-defined-functions.md). 

В примере проекта **_occupancy-quickstart_** откройте файл **_src\actions\provisionSample.yaml_** в Visual Studio Code. Обратите внимание на раздел, который начинается с типа **matchers**. Каждая запись в этом разделе создает сопоставитель с указанным **именем**, который будет отслеживать датчик типа **dataTypeValue**. Обратите внимание, как этот раздел относится к пространству с именем *Focus Room A1*, который имеет узел **devices**, содержащий несколько **датчиков**. Для подготовки сопоставителя, который будет отслеживать один из этих датчиков, его значение **dataTypeValue** должно соответствовать **dataType** этого датчика. 

Добавьте следующий сопоставитель под существующими, убедившись, что ключи согласованы, а пространства не заменены символами табуляции:

```yaml
      - name: Matcher Temperature
        dataTypeValue: Temperature
```

Сопоставитель будет отслеживать датчик *SAMPLE_SENSOR_TEMPERATURE*, который вы добавили в [первом руководстве](tutorial-facilities-setup.md). Обратите внимание, что эти строки также присутствуют в файле *provisionSample.yaml* в закомментированном виде. Чтобы раскомментировать их, просто удалите символ # в начале каждой строки. 

<a id="udf" />

## <a name="create-a-user-defined-function"></a>Создание определяемой пользователем функции
Определяемые пользователем функции позволяют настроить обработку данных датчиков. Они представляют собой настраиваемый код JavaScript, который может выполняться в вашем экземпляре Digital Twins, когда возникают конкретные условия, описанные в сопоставителях. Вы можете создать *сопоставители* и *определяемые пользователем функции* для каждого датчика, который нужно отслеживать. Дополнительные сведения см. в статье [Обработка данных и определяемые пользователем функции](concepts-user-defined-functions.md). 

В примере файла *provisionSample.yaml* найдите раздел, начинающийся с типа **userdefinedfunctions**. В этом разделе описывается определяемая пользователем функция с заданным **именем**, которая выполняет действия по отношению к списку сопоставителей в разделе **matcherNames**. Обратите внимание, что вы можете предоставить свой собственный файл JavaScript для определяемой пользователем функции в качестве **скрипта**. Обратите также внимание на раздел с именем **roleassignments**. Он назначает роль *Администратор пространства* для определяемой пользователем функции. Благодаря этому функция может получать доступ к событиям, поступающим из любого из подготовленных пространств. 

1. Настройте определяемую пользователем функцию таким образом, чтобы она включала сопоставитель температуры, добавив или раскомментировав следующую строку в узле `matcherNames` файла *provisionSample.yaml*:

    ```yaml
            - Matcher Temperature
    ```

1. Откройте файл **_src\actions\userDefinedFunctions\availability.js_** в редакторе. Это файл, на который ссылается элемент **script** в файле *provisionSample.yaml*. Определяемая пользователем функция в этом файле ищет условия, когда в комнате не обнаружено движения, а уровни углекислого газа ниже 1000 ppm. Измените файл JavaScript, чтобы отслеживать температуру в дополнение к другим условиям. Добавьте следующие строки кода для отслеживания условий, когда в комнате отсутствует движение, уровни углекислого газа ниже 1000 ppm, а температура ниже 78 градусов по Фаренгейту.

   > [!NOTE]
   > В этом разделе изменяется файл *src\actions\userDefinedFunctions\availability.js*, чтобы вы могли подробно узнать один из способов написания определяемой пользователем функции. Однако вы можете напрямую использовать файл [src\actions\userDefinedFunctions\availabilityForTutorial.js](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/userDefinedFunctions/availabilityForTutorial.js) в своей настройке. Этот файл содержит все изменения, необходимые для этого руководства. Если вместо этого вы будете использовать этот файл, не забудьте использовать правильное имя файла для ключа **_script_** в [src\actions\provisionSample.yaml](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/provisionSample.yaml).

    1. В верхней части файла добавьте следующие строки для температуры под комментарием `// Add your sensor type here`:

        ```JavaScript
            var temperatureType = "Temperature";
            var temperatureThreshold = 78;
        ```
   
    1. Добавьте следующие строки после оператора, который определяет `var motionSensor`, под комментарием `// Add your sensor variable here`:

        ```JavaScript
            var temperatureSensor = otherSensors.find(function(element) {
                return element.DataType === temperatureType;
            });
        ```
    
    1. Добавьте следующие строки после оператора, который определяет `var carbonDioxideValue`, под комментарием `// Add your sensor latest value here`:

        ```JavaScript
            var temperatureValue = getFloatValue(temperatureSensor.Value().Value);
        ```
    
    1. Удалите следующие строки кода под комментарием `// Modify this line to monitor your sensor value`: 

        ```JavaScript
            if(carbonDioxideValue === null || motionValue === null) {
                sendNotification(telemetry.SensorId, "Sensor", "Error: Carbon dioxide or motion are null, returning");
                return;
            }
        ```
       
       Замените их следующими строками:

        ```JavaScript
            if(carbonDioxideValue === null || motionValue === null || temperatureValue === null){
                sendNotification(telemetry.SensorId, "Sensor", "Error: Carbon dioxide, motion, or temperature are null, returning");
                return;
            }
        ```
    
    1. Удалите следующие строки кода под комментарием `// Modify these lines as per your sensor`:

        ```JavaScript
            var availableFresh = "Room is available and air is fresh";
            var noAvailableOrFresh = "Room is not available or air quality is poor";
        ```

       Замените их следующими строками:

        ```JavaScript
            var alert = "Room with fresh air and comfortable temperature is available.";
            var noAlert = "Either room is occupied, or working conditions are not right.";
        ```
    
    1. Удалите следующий блок *if-else* под комментарием `// Modify this code block for your sensor`:

        ```JavaScript
            // If carbonDioxide less than threshold and no presence in the room => log, notify and set parent space computed value
            if(carbonDioxideValue < carbonDioxideThreshold && !presence) {
                log(`${availableFresh}. Carbon Dioxide: ${carbonDioxideValue}. Presence: ${presence}.`);
                setSpaceValue(parentSpace.Id, spaceAvailFresh, availableFresh);

                // Set up custom notification for air quality
                parentSpace.Notify(JSON.stringify(availableFresh));
            }
            else {
                log(`${noAvailableOrFresh}. Carbon Dioxide: ${carbonDioxideValue}. Presence: ${presence}.`);
                setSpaceValue(parentSpace.Id, spaceAvailFresh, noAvailableOrFresh);

                // Set up custom notification for air quality
                parentSpace.Notify(JSON.stringify(noAvailableOrFresh));
            }
        ```
      
       Замените его следующим блоком *if-else*:

        ```JavaScript
            // If sensor values are within range and room is available
            if(carbonDioxideValue < carbonDioxideThreshold && temperatureValue < temperatureThreshold && !presence) {
                log(`${alert}. Carbon Dioxide: ${carbonDioxideValue}. Temperature: ${temperatureValue}. Presence: ${presence}.`);

                // log, notify and set parent space computed value
                setSpaceValue(parentSpace.Id, spaceAvailFresh, alert);

                // Set up notification for this alert
                parentSpace.Notify(JSON.stringify(alert));
            }
            else {
                log(`${noAlert}. Carbon Dioxide: ${carbonDioxideValue}. Temperature: ${temperatureValue}. Presence: ${presence}.`);
    
                // log, notify and set parent space computed value
                setSpaceValue(parentSpace.Id, spaceAvailFresh, noAlert);
            }
        ```
        
        Измененная определяемая пользователем функция будет отслеживать условие, когда комната становится свободной, а объем углекислого газа и температура в ней находятся в допустимых пределах. Она будет генерировать уведомление с выражением `parentSpace.Notify(JSON.stringigy(alert));`, когда это условие будет выполнено. Функция установит значение отслеживаемого пространства независимо от того, выполнено ли условие, и выдаст соответствующее сообщение.
    
    1. Сохраните файл. 
    
1. Откройте окно командной строки и перейдите к папке **_occupancy-quickstart\src_**. Выполните следующую команду, чтобы подготовить пространственный интеллектуальный граф и определяемую пользователем функцию. 

    ```cmd/sh
    dotnet run ProvisionSample
    ```

   > [!IMPORTANT]
   > Чтобы предотвратить несанкционированный доступ к вашему API управления Digital Twins, приложение **_occupancy-quickstart_** требует, чтобы вы вошли в систему с учетными данными своей учетной записи Azure. Приложение сохраняет ваши учетные данные на короткий период, поэтому вам может не потребоваться выполнять вход в систему при каждом запуске. Когда эта программа выполняется в первый раз, а также после того, как срок хранения учетных данных истечет, вы будете направлены на страницу входа и вам будет предоставлен код сеанса для ввода на этой странице. Следуйте указаниям по входу в учетную запись Azure.


1. Как только ваша учетная запись будет аутентифицирована, приложение начнет создавать пример пространственного графа, как указано в файле *provisionSample.yaml*. Подождите, пока подготовка будет завершена. Это займет несколько минут. Затем читайте сообщения в командном окне и следите за тем, как создается ваш пространственный граф. Обратите внимание, как приложение создает центр Интернета вещей в корневом узле или `Venue`. 

1. Из выходных данных в окне команд скопируйте значение `ConnectionString` в разделе `Devices` в буфер обмена. Это значение понадобится для имитации подключения к устройству в следующем разделе.

    ![Пример подготовки](./media/tutorial-facilities-udf/run-provision-sample.png)

> [!TIP]
> Если вы получите сообщение об ошибке, подобное "Операция ввода-вывода была прервана из-за завершения потока или запроса приложения", в процессе подготовки, попробуйте запустить команду снова. Это может произойти, если время ожидания активности HTTP-клиента истекло из-за проблемы с сетью.

<a id="simulate" />

## <a name="simulate-sensor-data"></a>Моделирование данных датчика
В этом разделе вы будете использовать проект с именем *device-connectivity* в примере, чтобы имитировать данные датчиков для обнаружения движения, температуры и уровня углекислого газа. Этот проект генерирует случайные значения для датчиков и отправляет их в центр Интернета вещей, используя строку подключения устройства.

1. В отдельном окне команд перейдите к примеру Digital Twins, а затем в папку **_device-connectivity_**.

1. Запустите эту команду, чтобы убедиться, что зависимости для проекта верны:

    ```cmd/sh
    dotnet restore
    ```

1. Откройте файл *appSettings.json* в редакторе и измените следующие значения:
    1. *DeviceConnectionString*: присвойте значение `ConnectionString`, выведенное в окне выходных данных в предыдущем разделе. Обязательно полностью скопируйте строку в кавычках, чтобы симулятор правильно соединился с центром Интернета вещей.

    1. Идентификатор *HardwareId* в массиве *Sensors*: так как вы имитируете события датчиков, подготовленных для вашего экземпляра Digital Twins, идентификатор оборудования и имена датчиков в этом файле должны совпадать с узлом `sensors` файла *provisionSample.yaml*. Добавьте новую запись для датчика температуры. Узел **Sensors** в файле *appSettings.json* должен выглядеть следующим образом:

        ```JSON
        "Sensors": [{
          "DataType": "Motion",
          "HardwareId": "SAMPLE_SENSOR_MOTION"
        },{
          "DataType": "CarbonDioxide",
          "HardwareId": "SAMPLE_SENSOR_CARBONDIOXIDE"
        },{
          "DataType": "Temperature",
          "HardwareId": "SAMPLE_SENSOR_TEMPERATURE"
        }]
        ```

1. Запустите эту команду, чтобы начать имитировать события устройства, связанные с температурой, движением и углекислым газом:

    ```cmd/sh
    dotnet run
    ```

   > [!NOTE] 
   > Так как пример моделирования не связывается с вашим экземпляром Digital Twin напрямую, аутентификация не требуется.

## <a name="get-results-of-user-defined-function"></a>Получение результатов выполнения определяемой пользователем функции
Определяемая пользователем функция запускается каждый раз, когда ваш экземпляр получает данные устройства и датчика. В этом разделе к экземпляру Digital Twins отправляется запрос на получение результатов выполнения определяемой пользователем функции. Вы почти в реальном времени будете узнавать, когда комната свободна, воздух свежий, а температура правильная. 

1. Откройте окно командной строки, которое вы использовали для подготовки примера, или новое окно и снова перейдите в папку **_occupancy-quickstart\src_** примера. 

1. Выполните следующую команду и войдите в систему при появлении запроса:

    ```cmd/sh
    dotnet run GetAvailableAndFreshSpaces
    ```

В окне вывода будет показано, как определяемая пользователем функция выполняется и перехватывает события из имитированного устройства. 

   ![Выполнение определяемой пользователем функции](./media/tutorial-facilities-udf/udf-running.png)

В зависимости от того, выполнено ли отслеживаемое условие, определяемая пользователем функция устанавливает значение пространства с соответствующим сообщением (приведенным в [разделе выше](#udf)), которое функция `GetAvailableAndFreshSpaces` выводит на консоль. 

## <a name="clean-up-resources"></a>Очистка ресурсов

Если на этом этапе вы хотите прекратить изучение Azure Digital Twins, можно удалить ресурсы, созданные в этом руководстве:

1. На [портале Azure](http://portal.azure.com) в меню слева щелкните **Все ресурсы**, выберите группу ресурсов Digital Twins и **удалите** ее.
2. Если необходимо, можно также удалить примеры приложений на рабочем компьютере. 


## <a name="next-steps"></a>Дополнительная информация

Теперь, когда вы подготовили пространства и создали платформу для активации пользовательских уведомлений, вы можете перейти к любому из следующих руководств. 

> [!div class="nextstepaction"]
> [Руководство по получению уведомлений от пространств Azure Digital Twins с использованием Logic Apps](tutorial-facilities-events.md)

или

> [!div class="nextstepaction"]
> [Руководство по визуализации и анализу событий пространств Azure Digital Twins с помощью Аналитики временных рядов](tutorial-facilities-analyze.md)
