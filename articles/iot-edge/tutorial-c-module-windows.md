---
title: Учебник по разработке модулей C для Windows (Azure IoT Edge) | Документация Майкрософт
description: В этом учебнике показано, как создать модуль IoT Edge c кодом C и развернуть его на устройстве с Windows под управлением IoT Edge
services: iot-edge
author: shizn
manager: philmea
ms.author: xshi
ms.date: 05/28/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: d1beae9ebd86035e075f75b088618ec55595e876
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/26/2019
ms.locfileid: "70035683"
---
# <a name="tutorial-develop-a-c-iot-edge-module-for-windows-devices"></a>Руководство по Разработка модулей IoT Edge на языке C для устройств с Windows

Для разработки и развертывания кода C на устройствах Windows с Azure IoT Edge используйте Visual Studio. 

Вы можете использовать модули Azure IoT Edge для развертывания кода, который реализует бизнес-логику непосредственно на устройствах IoT Edge. В этом руководстве рассматриваются создание и развертывание модуля IoT Edge, который фильтрует данные датчика. Из этого руководства вы узнаете, как выполнять следующие задачи:    

> [!div class="checklist"]
> * Создать модуль IoT Edge на основе пакета SDK для C с помощью Visual Studio.
> * Использовать Visual Studio и Docker для создания образа Docker и его публикации в реестре.
> * Развертывать модуль на устройстве IoT Edge.
> * Просматривать сформированные данные.

Модуль IoT Edge, создаваемый в этом руководстве, фильтрует данные температуры, которые создаются устройством. Оно отправляет сообщения, только если температура превышает заданное пороговое значение. Такой тип пограничного анализа удобен для сокращения объема данных, передаваемых в облако и сохраняемых в нем. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="solution-scope"></a>Область действия решения

В этом руководстве демонстрируется разработка модуля на языке **C** с помощью **Visual Studio 2019** и его развертывание на **устройстве Windows**. Если вы разрабатываете модули для устройств Linux, вам нужно другое руководство [по разработке модуля IoT Edge на языке C для устройств с Linux](tutorial-c-module.md). 

В следующей таблице перечислены возможные варианты для разработки и развертывания модулей C для устройств с Windows: 

| C | Visual Studio Code | Visual Studio 2017 или Visual Studio 2019 | 
| -- | ------------------ | ------------------ |
| **Windows AMD64** |  | ![Разработка модулей C для WinAMD64 в Visual Studio](./media/tutorial-c-module/green-check.png) |

## <a name="prerequisites"></a>Предварительные требования

Перед началом работы с этим учебником вы должны были пройти предыдущий учебник, в котором настроили среду разработки для разработки контейнеров Windows: [Tutorial: Develop IoT Edge modules for Windows devices](tutorial-develop-for-windows.md) (Учебник: разработка модулей IoT Edge для устройств с Windows) После работы с ним у вас должны быть готовы все необходимые компоненты: 

* [Центр Интернета вещей](../iot-hub/iot-hub-create-through-portal.md) ценовой категории "Бесплатный" или "Стандартный" в Azure.
* [Устройство с Windows, на котором выполняется Azure IoT Edge](quickstart.md).
* реестр контейнеров, например [Реестр контейнеров Azure](https://docs.microsoft.com/azure/container-registry/);
* Настроенная среда [Visual Studio 2019](https://docs.microsoft.com/visualstudio/install/install-visual-studio) с расширением [Azure IoT Edge Tools](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools).
* Приложение [Docker Desktop](https://docs.docker.com/docker-for-windows/install/), настроенное для запуска контейнеров Windows.
* Установите пакет SDK Azure IoT для C для Windows X64 с помощью vcpkg:

   ```powershell
   git clone https://github.com/Microsoft/vcpkg
   cd vcpkg
   .\bootstrap-vcpkg.bat
   .\vcpkg install azure-iot-sdk-c:x64-windows
   .\vcpkg --triplet x64-windows integrate install
   ```
   
> [!TIP]
> Если вы используете Visual Studio 2017 (15.7 или более поздней версии), скачайте и установите [Azure IoT Edge Tools](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) для Visual Studio 2017 из marketplace Visual Studio.

## <a name="create-a-module-project"></a>Создание проекта модуля

На следующих этапах показано, как создать проект модуля IoT Edge на основе пакета SDK для С, используя Visual Studio и расширение Azure IoT Edge Tools. После создания шаблона проекта добавьте новый код, чтобы модуль фильтровал сообщения по их сообщаемым свойствам. 

### <a name="create-a-new-project"></a>Создание нового проекта

Создайте шаблон решения C, которое можно настроить с помощью собственного кода.

1. Запустите Visual Studio 2019 и выберите **Создать проект**.

2. В окне нового проекта найдите проект **IoT Edge** и выберите для него тип **Azure IoT Edge (Windows amd64)** . Щелкните **Далее**. 

   ![Создание проекта Azure IoT Edge](./media/tutorial-c-module-windows/new-project.png)

3. В окне настроек проекта присвойте проекту и решению описательное имя, например **CTutorialApp**. Щелкните **Создать**, чтобы создать проект. 

   ![Настройка нового проекта Azure IoT Edge](./media/tutorial-c-module-windows/configure-project.png)

4. В приложении и окне модуля IoT Edge настройте проект, указав следующие значения: 

   | Поле | Значение |
   | ----- | ----- |
   | Выберите шаблон | Выберите **Модуль C**. | 
   | Имя проекта модуля | Присвойте модулю имя **CModule**. | 
   | Репозиторий образа Docker | Репозиторий изображений включает в себя имя реестра контейнеров и имя образа контейнера. Образ контейнера предварительно заполняется значением имени проекта модуля. Замените **localhost:5000** на значение сервера входа из реестра контейнеров Azure. Вы можете извлечь сервер входа на странице "Обзор" реестра контейнеров на портале Azure. <br><br> Окончательный репозиторий образов выглядит так: \<имя_реестра\>.azurecr.io/cmodule. |

   ![Настройка целевого устройства, типа модуля и реестра контейнеров для проекта](./media/tutorial-c-module-windows/add-application-and-module.png)

5. Нажмите кнопку **ОК**, чтобы применить изменения. 

### <a name="add-your-registry-credentials"></a>Добавление учетных данных реестра

Манифест развертывания совместно использует учетные данные для реестра контейнеров со средой выполнения IoT Edge. Среде выполнения нужны эти учетные данные, чтобы извлечь частный образ на устройство IoT Edge. Используйте учетные данные из раздела **Ключи доступа** в реестре контейнеров Azure. 

1. В обозревателе решений Visual Studio откройте файл **deployment.template.json**. 

2. Найдите свойство **registryCredentials** в требуемых свойствах $edgeAgent. 

3. Обновите свойство, указав свои учетные данные в следующем формате: 

   ```json
   "registryCredentials": {
     "<registry name>": {
       "username": "<username>",
       "password": "<password>",
       "address": "<registry name>.azurecr.io"
     }
   }
   ```

4. Сохраните файл deployment.template.json. 

### <a name="update-the-module-with-custom-code"></a>Обновление модуля с помощью пользовательского кода

Код стандартного модуля получает сообщения из очереди входящих сообщений и передает их через очередь исходящих сообщений. Давайте добавим еще немного кода, чтобы модуль обрабатывал сообщения на границе до передачи в Центр Интернета вещей. Обновите модуль таким образом, чтобы он анализировал данные о температуре, получаемые в каждом сообщении, и отправлял в Центр Интернета вещей только сообщения со сведениями о том, что температура превышает определенный порог. 


1. Данные с датчиков в этом сценарии поступают в формате JSON. Для фильтрации сообщений в формате JSON импортируйте библиотеку JSON для модуля C. Это руководство использует Parson.

   1. Скачайте [репозиторий GitHub для Parson](https://github.com/kgabis/parson). Скопируйте файлы **parson.c** и **parson.h** в проект **CModule**.

   2. В Visual Studio откройте файл **CMakeLists.txt** из папки проекта CModule. В верхней части файла импортируйте файлы Parson в качестве библиотеки, называемой **my_parson**.

      ```
      add_library(my_parson
          parson.c
          parson.h
      )
      ```

   3. Добавьте **my_parson** в список библиотек в разделе **target_link_libraries** файла CMakeLists.txt.

   4. Сохраните файл **CMakeLists.txt**.

   5. Откройте **CModule** > **main.c**. В нижней части списка содержатся операторы. Добавьте новый, чтобы включить `parson.h` для поддержки JSON:

      ```c
      #include "parson.h"
      ```

2.  В файл **main.c** добавьте глобальную переменную с именем `temperatureThreshold` рядом с переменной messagesReceivedByInput1Queue. Эта переменная задает значение, которое должно быть превышено измеренной температурой, чтобы данные были отправлены в Центр Интернета вещей.

    ```c
    static double temperatureThreshold = 25;
    ```

3. Найдите функцию `CreateMessageInstance` в main.c. Замените внутренний оператор if-else следующим кодом, добавив несколько строк с новыми функциональными возможностями: 

   ```c
   if ((messageInstance->messageHandle = IoTHubMessage_Clone(message)) == NULL)
   {
       free(messageInstance);
       messageInstance = NULL;
   }
   else
   {
       messageInstance->messageTrackingId = messagesReceivedByInput1Queue;
       MAP_HANDLE propMap = IoTHubMessage_Properties(messageInstance->messageHandle);
       if (Map_AddOrUpdate(propMap, "MessageType", "Alert") != MAP_OK)
       {
          printf("ERROR: Map_AddOrUpdate Failed!\r\n");
       }
   }
   ```

   Эти новые строки кода в инструкции else добавляют в сообщение новое свойство, которое помечает сообщение как оповещение. Этот код помечает все сообщения как оповещения, так как наша новая функция отправляет в Центр Интернета вещей только те сообщения, в которых сообщается о высокой температуре. 

4. Найдите функцию `InputQueue1Callback` и замените всю функцию следующим кодом. Эта функция реализует фактический фильтр обмена сообщениями. При получении сообщения функция проверяет, превышает ли температура, указанная в сообщении, заданный порог. Если это так, функция перенаправляет сообщение через очередь исходящих сообщений. Если нет, сообщение игнорируется. 

    ```c
    static IOTHUBMESSAGE_DISPOSITION_RESULT InputQueue1Callback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
    {
        IOTHUBMESSAGE_DISPOSITION_RESULT result;
        IOTHUB_CLIENT_RESULT clientResult;
        IOTHUB_MODULE_CLIENT_LL_HANDLE iotHubModuleClientHandle = (IOTHUB_MODULE_CLIENT_LL_HANDLE)userContextCallback;

        unsigned const char* messageBody;
        size_t contentSize;

        if (IoTHubMessage_GetByteArray(message, &messageBody, &contentSize) != IOTHUB_MESSAGE_OK)
        {
            messageBody = "<null>";
        }

        printf("Received Message [%zu]\r\n Data: [%s]\r\n",
                messagesReceivedByInput1Queue, messageBody);

        // Check if the message reports temperatures higher than the threshold
        JSON_Value *root_value = json_parse_string(messageBody);
        JSON_Object *root_object = json_value_get_object(root_value);
        double temperature;

        // If temperature exceeds threshold, send to output1
        if (json_object_dotget_value(root_object, "machine.temperature") != NULL && (temperature = json_object_dotget_number(root_object, "machine.temperature")) > temperatureThreshold)
        {
            printf("Machine temperature %f exceeds threshold %f\r\n", temperature, temperatureThreshold);
            // This message should be sent to next stop in the pipeline, namely "output1".  What happens at "outpu1" is determined
            // by the configuration of the Edge routing table setup.
            MESSAGE_INSTANCE *messageInstance = CreateMessageInstance(message);
            if (NULL == messageInstance)
            {
                result = IOTHUBMESSAGE_ABANDONED;
            }
            else
            {
                printf("Sending message (%zu) to the next stage in pipeline\n", messagesReceivedByInput1Queue);

                clientResult = IoTHubModuleClient_LL_SendEventToOutputAsync(iotHubModuleClientHandle, messageInstance->messageHandle, "output1", SendConfirmationCallback, (void *)messageInstance);
                if (clientResult != IOTHUB_CLIENT_OK)
                {
                    IoTHubMessage_Destroy(messageInstance->messageHandle);
                    free(messageInstance);
                    printf("IoTHubModuleClient_LL_SendEventToOutputAsync failed on sending msg#=%zu, err=%d\n", messagesReceivedByInput1Queue, clientResult);
                    result = IOTHUBMESSAGE_ABANDONED;
                }
                else
                {
                    result = IOTHUBMESSAGE_ACCEPTED;
                }
            }
        }
        // If message does not exceed threshold, do not forward
        else
        {
            printf("Not sending message (%zu) to the next stage in pipeline.\r\n", messagesReceivedByInput1Queue);
            result = IOTHUBMESSAGE_ACCEPTED;
        }

        messagesReceivedByInput1Queue++;
        return result;
    }
    ```

5. Добавьте функцию `moduleTwinCallback`. Этот метод принимает изменения требуемых свойств из двойника модуля и соответствующим образом изменяет переменную **temperatureThreshold**. У каждого модуля есть собственный двойник модуля, что позволяет настроить код, выполняемый в модуле, непосредственно из облака.

    ```c
    static void moduleTwinCallback(DEVICE_TWIN_UPDATE_STATE update_state, const unsigned char* payLoad, size_t size, void* userContextCallback)
    {
        printf("\r\nTwin callback called with (state=%s, size=%zu):\r\n%s\r\n",
            MU_ENUM_TO_STRING(DEVICE_TWIN_UPDATE_STATE, update_state), size, payLoad);
        JSON_Value *root_value = json_parse_string(payLoad);
        JSON_Object *root_object = json_value_get_object(root_value);
        if (json_object_dotget_value(root_object, "desired.TemperatureThreshold") != NULL) {
            temperatureThreshold = json_object_dotget_number(root_object, "desired.TemperatureThreshold");
        }
        if (json_object_get_value(root_object, "TemperatureThreshold") != NULL) {
            temperatureThreshold = json_object_get_number(root_object, "TemperatureThreshold");
        }
    }
    ```

6. Найдите функцию `SetupCallbacksForModule`. Замените эту функцию приведенным ниже кодом с инструкцией **else-if**, которая позволяет проверить обновление двойника модуля. 

   ```c
   static int SetupCallbacksForModule(IOTHUB_MODULE_CLIENT_LL_HANDLE iotHubModuleClientHandle)
   {
       int ret;

       if (IoTHubModuleClient_LL_SetInputMessageCallback(iotHubModuleClientHandle, "input1", InputQueue1Callback, (void*)iotHubModuleClientHandle) != IOTHUB_CLIENT_OK)
       {
           printf("ERROR: IoTHubModuleClient_LL_SetInputMessageCallback(\"input1\")..........FAILED!\r\n");
           ret = 1;
       }
       else if (IoTHubModuleClient_LL_SetModuleTwinCallback(iotHubModuleClientHandle, moduleTwinCallback, (void*)iotHubModuleClientHandle) != IOTHUB_CLIENT_OK)
       {
           printf("ERROR: IoTHubModuleClient_LL_SetModuleTwinCallback(default)..........FAILED!\r\n");
           ret = 1;
       }
       else
       {
           ret = 0;
       }

       return ret;
   }
   ```

7. Сохраните файл main.c.

8. Откройте файл **deployment.template.json**. 

9. Добавьте двойник модуля CModule в манифест развертывания. Вставьте следующее содержимое JSON в нижней части раздела `moduleContent` после двойника модуля `$edgeHub`:

   ```json
   "CModule": {
       "properties.desired":{
           "TemperatureThreshold":25
       }
   }
   ```

   ![Добавление двойника CModule в шаблон развертывания](./media/tutorial-c-module-windows/module-twin.png)

1. Сохраните файл **deployment.template.json**.

## <a name="build-and-push-your-module"></a>Сборка и отправка модуля

В предыдущем разделе вы создали решение IoT Edge и добавили в **CModule** код для фильтрации сообщений, в которых указана температура компьютера ниже допустимого порогового значения. Теперь необходимо создать решение в качестве образа контейнера и передать его в реестр контейнеров. 

1. Используйте следующую команду, чтобы войти в Docker на компьютере разработки. Выполните вход в систему, используя имя пользователя, пароль и сервер входа из реестра контейнеров Azure. Вы можете получить эти значения в разделе **Ключи доступа** в разделе реестра на портале Azure.

   ```cmd
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Возможно, появится предупреждение системы безопасности, в котором рекомендуется использовать `--password-stdin`. Для рабочих сценариев это лучшая методика, но мы не будем рассматривать ее в этом учебнике. Дополнительные сведения см. в описании команды [docker login](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) в справочнике.

2. В обозревателе решений Visual Studio щелкните правой кнопкой мыши имя проекта, который хотите скомпилировать. Имя по умолчанию — **AzureIotEdgeApp1**, и так как вы создаете модуль Windows, должно быть расширение **Windows.Amd64**. 

3. Выберите **Build and Push IoT Edge Modules** (Создание и отправка модулей IoT Edge). 

   Команда сборки и отправки запускает три операции. Во-первых, она создает в решении папку с именем **config**, которая содержит полный манифест развертывания на основе сведений из шаблона развертывания и других файлов решения. Во-вторых, она выполняет команду `docker build` для сборки образа контейнера на основе подходящего файла dockerfile для целевой архитектуры. В-третьих, выполняется `docker push` для отправки образа в реестр контейнеров. 

## <a name="deploy-modules-to-device"></a>Развертывание модулей на устройстве

Разверните проект модуля на устройство IoT Edge с помощью обозревателя Visual Studio Cloud Explorer и расширения Azure IoT Edge Tools. У вас уже есть манифест развертывания, подготовленный для вашего сценария (файл **deployment.json** в папке config). Теперь вам осталось выбрать устройство для получения развертывания.

Убедитесь, что устройство IoT Edge запущено и работает. 

1. В Visual Studio Cloud Explorer разверните ресурсы, чтобы увидеть список устройств IoT. 

2. Щелкните правой кнопкой мыши имя устройства IoT Edge, на котором необходимо выполнить развертывание. 

3. Выберите **Создать развертывание**.

4. В проводнике выберите файл **deployment.windows amd64** из папки config решения. 

5. Обновите Cloud Explorer для просмотра развернутых модулей, перечисленных для устройства. 


## <a name="view-generated-data"></a>Просмотр сформированных данных

Когда вы примените манифест развертывания к устройству IoT Edge, в среде выполнения IoT Edge на устройстве начнется сбор сведений о новом развертывании и выполнение операций. Работа всех выполняющихся на устройстве модулей, которые не включены в манифест развертывания, будет остановлена. А модули, которых нет на устройстве, будут запущены. 

Расширение IoT Edge Tools можно использовать для просмотра сообщений по мере их поступления в Центр Интернета вещей. 

1. В Visual Studio Cloud Explorer выберите имя устройства IoT Edge. 

2. Из списка **Actions** (Действия) выберите **Start Monitoring Built-in Event Endpoint** (Начать мониторинг встроенной конечной точки событий). 

3. Просмотрите сообщения, поступающие в Центр Интернета вещей. Для поступления сообщений может потребоваться некоторое время, так как устройство IoT Edge должно сначала получить новое развертывание и запустить все модули. После этого измененный нами код CModule ожидает, пока температура компьютера не достигнет 25 градусов, и лишь затем отправляет сообщения. С помощью кода также присваивается тип **Оповещение** всем сообщениям со сведениями о достижении порога температуры. 

   ![Просмотр поступающих сообщений в Центре Интернета вещей](./media/tutorial-c-module-windows/view-d2c-message.png)

## <a name="edit-the-module-twin"></a>Изменение двойника модуля

Мы использовали двойник модуля CModule, чтобы задать порог температуры 25 градусов. С помощью двойника модуля вы можете изменять функциональные возможности без необходимости обновлять код модуля.

1. В Visual Studio откройте файл **deployment.windows-amd64.json**. (Не файл deployment.template. Если вы не видите манифест развертывания в файле конфигурации в обозревателе решений, щелкните значок **Show all files** (Показать все файлы) на панели инструментов обозревателя.)

2. Найдите двойник модуля CModule и измените значение параметра **temperatureThreshold** на новое, которое будет на 5–10 градусов выше, чем последнее полученное значение температуры. 

3. Сохраните файл **deployment.windows-amd64.json**.

4. Выполните шаги по развертыванию еще раз, чтобы применить к устройству обновленный манифест развертывания. 

5. Выполните мониторинг сообщений, поступающих с устройства в облако. Вы увидите, что поток сообщений прекратится, пока не будет достигнут новый порог температуры. 

## <a name="clean-up-resources"></a>Очистка ресурсов 

Если вы планируете перейти к следующей рекомендуемой статье, можно сохранить созданные и повторно используемые ресурсы и конфигурации. Это же устройство IoT Edge также можно использовать в качестве тестового устройства. 

В противном случае вы можете удалить локальные конфигурации и ресурсы Azure, созданные в рамках этой статьи, чтобы избежать расходов. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Дополнительная информация

В этом руководстве вы создали модуль IoT Edge с кодом для фильтрации необработанных данных, созданных устройством IoT Edge. Дополнительные сведения о создании собственных модулей см. в статьях [о разработке модулей IoT Edge](module-development.md) и [о разработке модулей с помощью Visual Studio](how-to-visual-studio-develop-module.md). Ознакомьтесь со следующими учебниками, чтобы узнать, как развернуть облачные службы Azure для обработки и анализа данных на пограничном устройстве с помощью Azure IoT Edge.

> [!div class="nextstepaction"]
> [Функции](tutorial-deploy-function.md)
> [Stream Analytics](tutorial-deploy-stream-analytics.md)
> [Машинное обучение](tutorial-deploy-machine-learning.md)
> [Служба пользовательского визуального распознавания](tutorial-deploy-custom-vision.md)
