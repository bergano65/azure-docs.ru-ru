---
title: Руководство по разработке модулей C для Linux (Azure IoT Edge) | Документация Майкрософт
description: В этом руководстве показано, как создать модуль IoT Edge c использованием кода C и развернуть его на устройстве Linux под управлением IoT Edge
services: iot-edge
author: shizn
manager: philmea
ms.author: xshi
ms.date: 11/07/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: f610ad50daadf5bef1f43f3991792869c7dae6af
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/09/2019
ms.locfileid: "73890586"
---
# <a name="tutorial-develop-a-c-iot-edge-module-for-linux-devices"></a>Руководство по Разработка модулей IoT Edge на C для устройств Linux

Для разработки и развертывания кода C на устройствах Linux с Azure IoT Edge используйте Visual Studio Code. 

Вы можете использовать модули IoT Edge для развертывания кода, который реализует вашу бизнес-логику непосредственно на устройствах IoT Edge. В этом руководстве рассматриваются создание и развертывание модуля IoT Edge, который фильтрует данные датчика. Из этого руководства вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * Использование Visual Studio Code для создания модуля IoT Edge c кодом C
> * Использование Visual Studio Code и Docker для создания образа Docker и его публикации в реестре контейнеров
> * Развертывание модуля на устройстве IoT Edge.
> * Просмотр сформированных данных

Модуль IoT Edge, создаваемый в этом руководстве, фильтрует данные температуры, созданные вашим устройством. Оно отправляет сообщения, только если температура превышает заданное пороговое значение. Такой тип пограничного анализа удобен для сокращения объема данных, передаваемых в облако и сохраняемых в нем.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="solution-scope"></a>Область действия решения

В этом руководстве описана разработка модуля на **C** с помощью **Visual Studio Code** и его развертывание на **устройстве Linux**. Если вы разрабатываете модули IoT Edge на C для устройств Windows, перейдите к [этому руководству](tutorial-c-module-windows.md).

В приведенной ниже таблице перечислены возможные варианты для разработки и развертывания модулей C для Linux. 

| C | Visual Studio Code | Visual Studio | 
| - | ------------------ | ------------- |
| **Linux AMD64** | ![Использование VS Code для модулей C в Linux AMD64](./media/tutorial-c-module/green-check.png) | ![Использование VS для модулей C в Linux AMD64](./media/tutorial-c-module/green-check.png) |
| **Linux ARM32** | ![Использование VS Code для модулей C в Linux ARM32](./media/tutorial-c-module/green-check.png) | ![Использование VS для модулей C в Linux ARM32](./media/tutorial-c-module/green-check.png) |

## <a name="prerequisites"></a>Предварительные требования

Предполагается, что перед началом работы с этим руководством вы прошли предыдущее, в рамках которого настроили окружение для разработки контейнеров Linux: [Tutorial: Develop IoT Edge modules for Linux devices](tutorial-develop-for-linux.md) (Руководство. Разработка модулей IoT Edge для устройств с Linux). После работы с ним у вас должны быть готовы все необходимые компоненты: 

* [Центр Интернета вещей](../iot-hub/iot-hub-create-through-portal.md) ценовой категории "Бесплатный" или "Стандартный" в Azure.
* [устройство Linux, на котором выполняется Azure IoT Edge](quickstart-linux.md);
* реестр контейнеров, например [Реестр контейнеров Azure](https://docs.microsoft.com/azure/container-registry/);
* средство [Visual Studio Code](https://code.visualstudio.com/), настроенное с помощью [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools);
* выпуск [Docker CE](https://docs.docker.com/install/), настроенный для выполнения контейнеров Linux.

Для разработки модуля IoT Edge на языке C установите на компьютере разработки следующие дополнительные компоненты: 

* [Расширение C/C++](https://marketplace.visualstudio.com/items?itemName=ms-vscode.cpptools) для Visual Studio Code.

## <a name="create-a-module-project"></a>Создание проекта модуля

Описанный ниже процесс позволяет создать проект модуля IoT Edge для языка C с использованием Visual Studio Code и расширения Azure IoT Tools. Когда шаблон проекта будет готов, добавьте новый код, чтобы модуль фильтровал сообщения по их сообщаемым свойствам. 

### <a name="create-a-new-project"></a>Создание нового проекта

Создайте шаблон решения C, которое можно настроить с помощью собственного кода.

1. Выберите **Представление** > **Палитра команд** для открытия палитры команд VS Code.

2. В палитре команд введите и выполните команду **Azure: Sign in**. Следуйте инструкциям, чтобы войти в свою учетную запись Azure. Если вы уже выполняли вход, этот шаг можно пропустить.

3. В палитре команд введите и выполните команду **Azure IoT Edge: New IoT Edge Solution** (Azure IoT Edge: создать решение IoT Edge). Следуйте инструкциям на экране в палитре команд для создания решения.

   | Поле | Значение |
   | ----- | ----- |
   | Выбор папки | Выберите расположение на компьютере разработчика для VS Code, чтобы создать файлы решения. |
   | Введите название решения. | Введите описательное имя решения или примите имя по умолчанию **EdgeSolution**. |
   | Выбор шаблона модуля | Выберите **модуль C**. |
   | Указание имени модуля | Присвойте модулю имя **CModule**. |
   | Указание репозитория изображений Docker для модуля | Репозиторий изображений включает в себя имя реестра контейнеров и имя образа контейнера. Образ контейнера предварительно заполняется именем, которое вы указали на последнем шаге. Замените **localhost:5000** на значение сервера входа из реестра контейнеров Azure. Вы можете извлечь сервер входа на странице "Обзор" реестра контейнеров на портале Azure. <br><br> Окончательный репозиторий образов выглядит так: \<имя_реестра\>.azurecr.io/cmodule. |
 
   ![Выбор репозитория образа Docker](./media/tutorial-c-module/repository.png)

### <a name="add-your-registry-credentials"></a>Добавление учетных данных реестра

Файл среды хранит учетные данные для реестра контейнеров и совместно использует их со средой выполнения IoT Edge. Среде выполнения нужны эти учетные данные, чтобы извлечь частный образ на устройство IoT Edge.

1. Откройте в обозревателе VS Code файл с расширением ENV.
2. Обновите поля с **именем пользователя** и **паролем**, скопированные из своего реестра контейнера Azure.
3. Сохраните этот файл.

### <a name="select-your-target-architecture"></a>Выбор целевой архитектуры

Сейчас Visual Studio Code позволяет разрабатывать модули C для устройств Linux AMD64 и Linux ARM32v7. Для каждого решения вам нужно выбрать одну целевую платформу, так как сборка и запуск контейнера для разных архитектур различается. По умолчанию используется Linux AMD64. 

1. Откройте палитру команд и выполните поиск **Azure IoT Edge: Set Default Target Platform for Edge Solution** (Azure IoT Edge: установить целевую платформу по умолчанию для решения Edge) или выберите значок ярлыка на боковой панели в нижней части окна. 

2. В палитре команд выберите целевую архитектуру из списка параметров. В рамках этого руководства мы используем в качестве устройства IoT Edge виртуальную машину Ubuntu. Поэтому сохраним значение по умолчанию **amd64**. 

### <a name="update-the-module-with-custom-code"></a>Обновление модуля с помощью пользовательского кода

Код стандартного модуля получает сообщения из очереди входящих сообщений и передает их через очередь исходящих сообщений. Давайте добавим еще немного кода, чтобы модуль обрабатывал сообщения на границе до передачи в Центр Интернета вещей. Обновите модуль таким образом, чтобы он анализировал данные о температуре, получаемые в каждом сообщении, и отправлял в Центр Интернета вещей только сообщения со сведениями о том, что температура превышает определенный порог. 

1. Данные с датчиков в этом сценарии поступают в формате JSON. Для фильтрации сообщений в формате JSON импортируйте библиотеку JSON для модуля C. Это руководство использует Parson.

   1. Скачайте [репозиторий GitHub для Parson](https://github.com/kgabis/parson). Скопируйте файлы **parson.c** и **parson.h** в папку **CModule**.

   2. Откройте **Модули** > **CModule** > **CMakeLists.txt**. В верхней части файла импортируйте файлы Parson в качестве библиотеки, называемой **my_parson**.

      ```
      add_library(my_parson
          parson.c
          parson.h
      )
      ```

   3. Добавьте `my_parson` в список библиотек в функции **target_link_libraries** файла CMakeLists.txt.

   4. Сохраните файл **CMakeLists.txt**.

   5. Откройте **Модули** > **CModule** > **main.c**. В нижней части списка содержатся операторы. Добавьте новый, чтобы включить `parson.h` для поддержки JSON:

      ```c
      #include "parson.h"
      ```

1. В файле **main.c** добавьте глобальную переменную с именем `temperatureThreshold` после включения раздела. Эта переменная задает значение, которое должно быть превышено измеренной температурой, чтобы данные были отправлены в Центр Интернета вещей.

    ```c
    static double temperatureThreshold = 25;
    ```

1. Найдите функцию `CreateMessageInstance` в main.c. Замените внутренний оператор if-else следующим кодом, добавив несколько строк с новыми функциональными возможностями: 

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

   Эти новые строки кода в инструкции else добавляют в сообщение новое свойство, которое помечает сообщение как оповещение. Этот код помечает все сообщения как оповещения, так как наша новая функция отправляет в центр Интернета вещей только сообщения со сведениями о высокой температуре. 

1. Полностью замените функцию `InputQueue1Callback` следующим кодом: Эта функция реализует фактический фильтр обмена сообщениями. При получении сообщения функция проверяет, превышает ли температура, указанная в сообщении, заданный порог. Если это так, функция перенаправляет сообщение через очередь исходящих сообщений. Если нет, сообщение игнорируется. 

    ```c
    static unsigned char *bytearray_to_str(const unsigned char *buffer, size_t len)
    {
        unsigned char *ret = (unsigned char *)malloc(len + 1);
        memcpy(ret, buffer, len);
        ret[len] = '\0';
        return ret;
    }

    static IOTHUBMESSAGE_DISPOSITION_RESULT InputQueue1Callback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
    {
        IOTHUBMESSAGE_DISPOSITION_RESULT result;
        IOTHUB_CLIENT_RESULT clientResult;
        IOTHUB_MODULE_CLIENT_LL_HANDLE iotHubModuleClientHandle = (IOTHUB_MODULE_CLIENT_LL_HANDLE)userContextCallback;

        unsigned const char* messageBody;
        size_t contentSize;

        if (IoTHubMessage_GetByteArray(message, &messageBody, &contentSize) == IOTHUB_MESSAGE_OK)
        {
            messageBody = bytearray_to_str(messageBody, contentSize);
        } else
        {
            messageBody = "<null>";
        }

        printf("Received Message [%zu]\r\n Data: [%s]\r\n",
                messagesReceivedByInput1Queue, messageBody);

        // Check if the message reports temperatures higher than the threshold
        JSON_Value *root_value = json_parse_string(messageBody);
        JSON_Object *root_object = json_value_get_object(root_value);
        double temperature;
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
        else
        {
            printf("Not sending message (%zu) to the next stage in pipeline.\r\n", messagesReceivedByInput1Queue);
            result = IOTHUBMESSAGE_ACCEPTED;
        }

        messagesReceivedByInput1Queue++;
        return result;
    }
    ```

1. Добавьте функцию `moduleTwinCallback`. Этот метод принимает изменения требуемых свойств из двойника модуля и соответствующим образом изменяет переменную **temperatureThreshold**. У каждого модуля есть собственный двойник модуля, что позволяет настроить код, выполняемый в модуле, непосредственно из облака.

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

1. Найдите функцию `SetupCallbacksForModule`. Замените эту функцию приведенным ниже кодом с инструкцией **else-if**, которая позволяет проверить обновление двойника модуля.

   ```c
   static int SetupCallbacksForModule(IOTHUB_MODULE_CLIENT_LL_HANDLE iotHubModuleClientHandle)
   {
       int ret;

       if (IoTHubModuleClient_LL_SetInputMessageCallback(iotHubModuleClientHandle, "input1", InputQueue1Callback, (void*)iotHubModuleClientHandle) != IOTHUB_CLIENT_OK)
       {
           printf("ERROR: IoTHubModuleClient_LL_SetInputMessageCallback(\"input1\")..........FAILED!\r\n");
           ret = MU_FAILURE;
       }
       else if (IoTHubModuleClient_LL_SetModuleTwinCallback(iotHubModuleClientHandle, moduleTwinCallback, (void*)iotHubModuleClientHandle) != IOTHUB_CLIENT_OK)
       {
           printf("ERROR: IoTHubModuleClient_LL_SetModuleTwinCallback(default)..........FAILED!\r\n");
           ret = MU_FAILURE;
       }
       else
       {
           ret = 0;
       }

       return ret;
   }
   ```

1. Сохраните файл main.c.

1. В обозревателе VS Code откройте файл **deployment.template.json** в рабочей области решения IoT Edge. 

1. Добавьте двойник модуля CModule в манифест развертывания. Вставьте следующее содержимое JSON в нижней части раздела `moduleContent` после двойника модуля `$edgeHub`:

   ```json
   "CModule": {
       "properties.desired":{
           "TemperatureThreshold":25
       }
   }
   ```

   ![Добавление двойника CModule в шаблон развертывания](./media/tutorial-c-module/module-twin.png)

1. Сохраните файл **deployment.template.json**.

## <a name="build-and-push-your-module"></a>Сборка и отправка модуля

В предыдущем разделе вы создали решение IoT Edge и добавили код в CModule, который будет отфильтровывать сообщения, где указанная температура компьютера находится в рамках допустимых ограничений. Теперь необходимо создать решение в качестве образа контейнера и передать его в реестр контейнеров.

1. Откройте терминал VS Code, последовательно выбрав **Вид** > **Терминал**.

1. Войдите в Docker. Для этого введите представленную ниже команду в окне терминала. Выполните вход в систему, используя имя пользователя, пароль и сервер входа из реестра контейнеров Azure. Вы можете получить эти значения в разделе **Ключи доступа** в разделе реестра на портале Azure.
     
   ```bash
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Возможно, появится предупреждение системы безопасности, в котором рекомендуется использовать `--password-stdin`. Для рабочих сценариев это лучшая методика, но мы не будем рассматривать ее в этом учебнике. Дополнительные сведения см. в описании команды [docker login](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) в справочнике.

2. В обозревателе VS Code щелкните правой кнопкой мыши файл **deployment.template.json** и выберите **Build and Push IoT Edge solution** (Создать и отправить решение IoT Edge).

   Эта команда сборки и отправки позволяет запустить три операции. Во-первых,в решении создается папка с именем **config**, которая содержит полный манифест развертывания на основе информации из шаблона развертывания и других файлов решения. Во-вторых, выполняется `docker build` для сборки образа контейнера на основе подходящего файла dockerfile для целевой архитектуры. В-третьих, выполняется `docker push` для отправки образа в реестр контейнеров.

## <a name="deploy-modules-to-device"></a>Развертывание модулей на устройстве

Разверните проект модуля на устройстве IoT Edge с помощью обозревателя Visual Studio Code и расширения Azure IoT Tools. У вас уже есть манифест развертывания, подготовленный для вашего сценария (файл **deployment.json** в папке config). Теперь вам осталось выбрать устройство для получения развертывания.

Убедитесь, что устройство IoT Edge работает. 

1. В обозревателе Visual Studio Code разверните раздел **Azure IoT Hub Devices** (Устройства Центра Интернета вещей Azure), чтобы отобразился список устройство Интернета вещей.

2. Щелкните имя устройства IoT Edge правой кнопкой мыши, а затем выберите **Create Deployment for Single Device** (Создание развертывания для одного устройства).

3. Выберите файл **deployment.json** в папке **config** и щелкните **Select Edge Deployment Manifest** (Выбрать манифест развертывания Edge). Не используйте файл deployment.template.json.

4. Нажмите кнопку "Обновить". Появится новый запущенный модуль **CModule**, модуль **SimulatedTemperatureSensor**, а также **$edgeAgent** и **$edgeHub**.

## <a name="view-generated-data"></a>Просмотр сформированных данных

Когда вы примените манифест развертывания к устройству IoT Edge, в среде выполнения IoT Edge на устройстве начнется сбор сведений о новом развертывании и выполнение операций. Работа всех выполняющихся на устройстве модулей, которые не включены в манифест развертывания, будет остановлена. А модули, которых нет на устройстве, будут запущены.

Сведения о состоянии устройства IoT Edge можно просмотреть в разделе **Azure IoT Hub Devices** (Устройства Центра Интернета вещей Azure) в обозревателе Visual Studio Code. Разверните раздел со сведениями об устройстве, чтобы просмотреть список развернутых и запущенных модулей.

1. В обозревателе Visual Studio Code щелкните имя устройства IoT Edge правой кнопкой мыши и выберите **Start Monitoring Built-in Event Endpoint** (Начать мониторинг строенной конечной точки событий).

2. Просмотрите сообщения, поступающие в Центр Интернета вещей. Для поступления сообщений может потребоваться некоторое время, так как устройство IoT Edge должно сначала получить новое развертывание и запустить все модули. После этого измененный нами код CModule ожидает, пока температура компьютера не достигнет 25 градусов, и лишь затем отправляет сообщения. С помощью кода также присваивается тип **Оповещение** всем сообщениям со сведениями о достижении порога температуры. 

   ![Просмотр поступающих сообщений в Центре Интернета вещей](./media/tutorial-c-module/view-d2c-message.png)

## <a name="edit-the-module-twin"></a>Обновление двойника модуля

Мы использовали в манифесте развертывания двойник модуля CModule, чтобы задать порог температуры в 25 градусов. С помощью двойника модуля вы можете изменять функциональные возможности без обновления кода модуля.

1. В Visual Studio Code разверните окно подробных сведений об устройстве IoT Edge, чтобы просмотреть список выполняющихся модулей. 

2. Щелкните **CModule** правой кнопкой мыши и выберите **Edit module twin** (Изменение двойника модуля). 

3. Найдите **TemperatureThreshold** в списке требуемых свойств. Измените его значение, увеличив температуру на 5 или 10 градусов относительно последней сообщаемой температуры. 

4. Сохраните файл двойника модуля.

5. Щелкните правой кнопкой мыши в любой точки на панели редактирования двойника модуля и выберите **Update module twin** (Обновление двойника модуля). 

5. Выполните мониторинг сообщений, поступающих с устройства в облако. Вы увидите, что поток сообщений прекратится, пока не будет достигнут новый порог температуры. 

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы планируете перейти к следующей рекомендуемой статье, можно сохранить созданные и повторно используемые ресурсы и конфигурации. Это же устройство IoT Edge также можно использовать в качестве тестового устройства.

В противном случае вы можете удалить локальные конфигурации и ресурсы Azure, созданные в рамках этой статьи, чтобы избежать расходов.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]


## <a name="next-steps"></a>Дополнительная информация

В этом руководстве вы создали модуль IoT Edge, который содержит код для фильтрации необработанных данных, созданных вашим устройством IoT Edge. Дополнительные сведения см. в статьях о [разработке модулей IoT Edge](module-development.md) и [разработке модулей с помощью Visual Studio Code](how-to-vs-code-develop-module.md). Перейдите к следующим руководствам, чтобы узнать, как Azure IoT Edge поможет развернуть облачные службы Azure для обработки данных на пограничном устройстве.

> [!div class="nextstepaction"]
> [Функции](tutorial-deploy-function.md)
> [Stream Analytics](tutorial-deploy-stream-analytics.md)
> [Машинное обучение](tutorial-deploy-machine-learning.md)
> [Служба пользовательского визуального распознавания](tutorial-deploy-custom-vision.md)

