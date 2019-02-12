---
title: Руководство по созданию пользовательского модуля с использованием C — Azure IoT Edge | Документация Майкрософт
description: В этом руководстве показано, как создать модуль IoT Edge c кодом C и развернуть его на пограничном устройстве
services: iot-edge
author: shizn
manager: philmea
ms.author: xshi
ms.date: 01/04/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: 798cf405c222a443dbbd3a316d20c482daf4429f
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/01/2019
ms.locfileid: "55563258"
---
# <a name="tutorial-develop-a-c-iot-edge-module-and-deploy-to-your-simulated-device"></a>Руководство. Разработка модуля IoT Edge с кодом C и его развертывание на имитированном устройстве

Вы можете использовать модули IoT Edge для развертывания кода, который реализует вашу бизнес-логику непосредственно на устройствах IoT Edge. В этом руководстве рассматриваются создание и развертывание модуля IoT Edge, который фильтрует данные датчика. Из этого руководства вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * Использование Visual Studio Code для создания модуля IoT Edge c кодом C
> * Использование Visual Studio Code и Docker для создания образа Docker и его публикации в реестре контейнеров
> * Развертывание модуля на устройстве IoT Edge.
> * Просмотр сформированных данных


Модуль IoT Edge, создаваемый в этом руководстве, фильтрует данные температуры, созданные вашим устройством. Оно отправляет сообщения, только если температура превышает заданное пороговое значение. Такой тип пограничного анализа удобен для сокращения объема данных, передаваемых в облако и сохраняемых в нем.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Предварительные требования

Устройство Azure IoT Edge.

* В качестве устройства Azure IoT Edge можно использовать компьютер, на котором ведется разработка, или виртуальную машину. Для этого выполните действия, описанные в кратком руководстве для устройств [Linux](quickstart-linux.md) или [Windows](quickstart.md). 
* Модули C для Azure IoT Edge не поддерживают контейнеры Windows. Если ваше устройство IoT Edge — компьютер Windows, настройте его для использования контейнеров Linux. Сведения о различиях установки контейнеров Windows и Linux см. в статье [Установка среды выполнения Azure IoT Edge в Windows](how-to-install-iot-edge-windows.md).

Облачные ресурсы.

* [Центр Интернета вещей](../iot-hub/iot-hub-create-through-portal.md) ценовой категории "Бесплатный" или "Стандартный" в Azure.

Ресурсы разработки.

* [Visual Studio Code](https://code.visualstudio.com/).
* [Расширение C/C++](https://marketplace.visualstudio.com/items?itemName=ms-vscode.cpptools) для Visual Studio Code.
* [Средства Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) для Visual Studio Code.
* [Docker CE](https://docs.docker.com/install/).

## <a name="create-a-container-registry"></a>Создание реестра контейнеров

В этом руководстве описано, как с помощью средств Azure IoT для Visual Studio Code создать модуль и **образ контейнера** из файлов. Затем вы отправите этот образ в **реестр**, содержащий ваши образы и управляющий ими. Наконец, вы развернете свой образ из реестра для выполнения на устройстве IoT Edge.

Для хранения образов контейнеров можно использовать любые реестры, совместимые с Docker. Две популярные службы реестров Docker — [Реестр контейнеров Azure](https://docs.microsoft.com/azure/container-registry/) и [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags). В этом руководстве используется реестр контейнеров Azure.

Если вы еще не создали реестр контейнеров, выполните эти действия, чтобы создать реестр в Azure:

1. На [портале Azure](https://portal.azure.com) выберите **Создать ресурс** > **Контейнеры** > **Реестр контейнеров**.

2. Предоставьте следующие значения для создания реестра контейнеров:

   | Поле | Значение |
   | ----- | ----- |
   | Имя реестра | Укажите уникальное имя. |
   | Подписка | Из раскрывающегося списка выберите подписку. |
   | Группа ресурсов | Мы рекомендуем использовать одну группу ресурсов для всех тестовых ресурсов, которые вы создаете во время работы с краткими руководствами по IoT Edge. Например, **IoTEdgeResources**. |
   | Расположение | Выберите расположение рядом с вами. |
   | Пользователь-администратор | Установите значение **Включить**. |
   | SKU | Выберите **Базовый**. |

5. Нажмите кнопку **Создать**.

6. После создания реестра контейнеров перейдите к нему, а затем выберите **Ключи доступа**.

7. Скопируйте значения **Сервер входа**, **Имя пользователя** и **Пароль**. Они потребуются позже в этом руководстве для предоставления доступа к реестру контейнеров.

## <a name="create-an-iot-edge-module-project"></a>Создание проекта модуля IoT Edge
Ниже показано, как создать проект модуля IoT Edge на основе .NET Сore 2.0 с использованием Visual Studio Code и средств Azure IoT.

### <a name="create-a-new-solution"></a>Создание решения

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

Окно VS Code загружает рабочую область решения IoT Edge. Рабочая область решения содержит пять компонентов верхнего уровня. Папка **modules** содержит код C для модуля, а также файлы Dockerfile для создания модуля в качестве образа контейнера. Файл **\.env** хранит учетные данные реестра контейнеров. Файл **deployment.template.json** содержит сведения, которые среда выполнения IoT Edge использует для развертывания модулей на устройстве. Файл **deployment.debug.template.json** содержит отладочные версии модулей. Работая с этим руководством, вы не будете изменять папку **\.vscode** или файл **\.gitignore**.

Если вы не указывали реестр контейнеров при создании решения, но приняли значение по умолчанию localhost:5000, у вас не будет файла \.env.

<!--
   ![C solution workspace](./media/tutorial-c-module/workspace.png)
-->

### <a name="add-your-registry-credentials"></a>Добавление учетных данных реестра

Файл среды хранит учетные данные для реестра контейнеров и совместно использует их со средой выполнения IoT Edge. Среде выполнения нужны эти учетные данные, чтобы извлечь частный образ на устройство IoT Edge.

1. Откройте в обозревателе VS Code файл с расширением ENV.
2. Обновите поля с **именем пользователя** и **паролем**, скопированные из своего реестра контейнера Azure.
3. Сохраните этот файл.

### <a name="update-the-module-with-custom-code"></a>Обновление модуля с помощью пользовательского кода

Добавьте код к модулю C, позволяющий ему считывать данные с датчика, проверьте, превысила ли температура компьютера безопасный порог, и передайте эту информацию в Центр Интернета вещей.

5. Данные с датчиков в этом сценарии поступают в формате JSON. Для фильтрации сообщений в формате JSON импортируйте библиотеку JSON для модуля C. Это руководство использует Parson.

   1. Скачайте [репозиторий GitHub для Parson](https://github.com/kgabis/parson). Скопируйте файлы **parson.c** и **parson.h** в папку **CModule**.

   2. Откройте **Модули** > **CModule** > **CMakeLists.txt**. В верхней части файла импортируйте файлы Parson в качестве библиотеки, называемой **my_parson**.

      ```
      add_library(my_parson
          parson.c
          parson.h
      )
      ```

   3. Добавьте **my_parson** в список библиотек в функции **target_link_libraries** файла CMakeLists.txt.

   4. Сохраните файл **CMakeLists.txt**.

   5. Откройте **Модули** > **CModule** > **main.c**. В нижней части списка содержатся операторы. Добавьте новый, чтобы включить `parson.h` для поддержки JSON:

      ```c
      #include "parson.h"
      ```

6. В файле **main.c** добавьте глобальную переменную с именем `temperatureThreshold` после включения раздела. Эта переменная задает значение, которое должно быть превышено измеренной температурой, чтобы данные были отправлены в Центр Интернета вещей.

    ```c
    static double temperatureThreshold = 25;
    ```

7. Полностью замените функцию `CreateMessageInstance` следующим кодом: Эта функция выделяет контекст для обратного вызова.

    ```c
    static MESSAGE_INSTANCE* CreateMessageInstance(IOTHUB_MESSAGE_HANDLE message)
    {
        MESSAGE_INSTANCE* messageInstance = (MESSAGE_INSTANCE*)malloc(sizeof(MESSAGE_INSTANCE));
        if (NULL == messageInstance)
        {
            printf("Failed allocating 'MESSAGE_INSTANCE' for pipelined message\r\n");
        }
        else
        {
            memset(messageInstance, 0, sizeof(*messageInstance));

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
        }

        return messageInstance;
    }
    ```

8. Полностью замените функцию `InputQueue1Callback` следующим кодом: Эта функция реализует фактический фильтр обмена сообщениями.

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

9. Добавьте функцию `moduleTwinCallback`. Этот метод принимает изменения требуемых свойств из двойника модуля и соответствующим образом изменяет переменную **temperatureThreshold**. У каждого модуля есть собственный двойник модуля, что позволяет настроить код, выполняемый в модуле, непосредственно из облака.

    ```c
    static void moduleTwinCallback(DEVICE_TWIN_UPDATE_STATE update_state, const unsigned char* payLoad, size_t size, void* userContextCallback)
    {
        printf("\r\nTwin callback called with (state=%s, size=%zu):\r\n%s\r\n",
            ENUM_TO_STRING(DEVICE_TWIN_UPDATE_STATE, update_state), size, payLoad);
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

10. Замените функцию `SetupCallbacksForModule` следующим кодом:

    ```c
    static int SetupCallbacksForModule(IOTHUB_MODULE_CLIENT_LL_HANDLE iotHubModuleClientHandle)
    {
        int ret;

        if (IoTHubModuleClient_LL_SetInputMessageCallback(iotHubModuleClientHandle, "input1", InputQueue1Callback, (void*)iotHubModuleClientHandle) != IOTHUB_CLIENT_OK)
        {
            printf("ERROR: IoTHubModuleClient_LL_SetInputMessageCallback(\"input1\")..........FAILED!\r\n");
            ret = __FAILURE__;
        }
        else if (IoTHubModuleClient_LL_SetModuleTwinCallback(iotHubModuleClientHandle, moduleTwinCallback, (void*)iotHubModuleClientHandle) != IOTHUB_CLIENT_OK)
        {
            printf("ERROR: IoTHubModuleClient_LL_SetModuleTwinCallback(default)..........FAILED!\r\n");
            ret = __FAILURE__;
        }
        else
        {
            ret = 0;
        }

        return ret;
    }
    ```

11. Сохраните файл main.c.

12. В обозревателе VS Code откройте файл **deployment.template.json** в рабочей области решения IoT Edge. Этот файл указывает агенту IoT Edge на модуль для развертывания, в нашем случае это **tempSensor** и **CModule**, а центру IoT Edge на то, как маршрутизировать сообщения между ними. Расширение Visual Studio Code автоматически заполняет шаблон развертывания большинством нужной информации, но проверьте, все ли подходит для вашего решения: 

   1. На панели состояния VS Code для устройства IoT Edge указана платформа по умолчанию **amd64**, т. е. модуль **CModule** использует версию образа для Linux amd64. При необходимости измените платформу по умолчанию на панели состояния с **amd64** на **arm32v7** в соответствии с архитектурой вашего устройства IoT Edge. 

      ![Снимок экрана обновления модуля платформы](./media/tutorial-c-module/image-platform.png)

   2. Убедитесь, что шаблон содержит правильное имя модуля, а не имя **SampleModule** по умолчанию, которое можно изменить при создании решения IoT Edge.

   3. В разделе **registryCredentials** хранятся ваши учетные данные реестра Docker, чтобы агент IoT Edge мог извлекать ваш образ модуля. Фактическое имя пользователя хранится в файле ENV, который Git игнорирует. Если это еще не сделано, добавьте ваши учетные данные в файл с расширением .env.  

   4. Для получения дополнительных сведений о манифестах развертывания см. статью [Сведения о развертывании модулей и установлении маршрутов в IoT Edge](module-composition.md).

13. Добавьте двойник модуля CModule в манифест развертывания. Вставьте следующее содержимое JSON в нижней части раздела `moduleContent` после двойника модуля `$edgeHub`:

    ```json
        "CModule": {
            "properties.desired":{
                "TemperatureThreshold":25
            }
        }
    ```

   ![Добавление двойника CModule в шаблон развертывания](./media/tutorial-c-module/module-twin.png)

14. Сохраните файл **deployment.template.json**.

## <a name="build-and-push-your-solution"></a>Сборка и отправка решения

В предыдущем разделе вы создали решение IoT Edge и добавили код в CModule, который будет отфильтровывать сообщения, где указанная температура компьютера находится в рамках допустимых ограничений. Теперь необходимо создать решение в качестве образа контейнера и передать его в реестр контейнеров.

1. Откройте интегрированный терминал VS Code, выберите **Вид** > **Интегрированный терминал**.

1. Войдите в Docker, введя следующую команду в окне интегрированного терминала Visual Studio Code. Необходимо войти с помощью учетных данных Реестра контейнеров Azure. Таким образом можно отправить образ модуля в реестр.
     
   ```csh/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```
   Укажите имя пользователя, пароль и сервер входа, скопированные из Реестра контейнеров Azure при его создании. Или получите их снова из раздела **Access keys** (Ключи доступа) в разделе реестра на портале Azure.

2. В обозревателе VS Code щелкните правой кнопкой мыши файл **deployment.template.json** и выберите **Build and Push IoT Edge solution** (Создать и отправить решение IoT Edge).

Когда вы указываете службе Visual Studio Code создать решение, она сначала создает файл `deployment.json` в новой папке **config**. Сведения о файле deployment.json собираются из обновленного файла шаблона, файла ENV, который использовался для хранения учетных данных реестра контейнера, и файла module.json в папке CModule.

Затем Visual Studio Code выполняет две команды в интегрированном терминале: `docker build` и `docker push`. Они создают ваш код, упаковывают `CModule.dll` в контейнеры и передают в реестр контейнеров, который вы указали при инициализации решения.

Полный адрес образа контейнера с тегом можно увидеть в окне интегрированного терминала VS Code. Адрес образа создается на основе информации из файла `module.json` в формате **\<repository\>:\<version\>-\<platform\>**. В этом руководстве он должен выглядеть так: **myregistry.azurecr.io/cmodule:0.0.1-amd64**.

>[!TIP]
>Если при попытке создания и отправки вашего модуля появляется сообщение об ошибке, выполните следующие проверки.
>* Вы вошли в Docker в Visual Studio Code, используя учетные данные из реестра контейнеров? Эти учетные данные отличаются от тех, которые вы используете для входа на портал Azure.
>* Правильный ли ваш репозиторий контейнеров? Откройте **модули** > **cmodule** > **module.json** и найдите поле **репозитория**. Репозиторий образов должен выглядеть так: **\<имя_реестра\>.azurecr.io/cmodule**. 
>* Вы создаете контейнеры того же типа, что и для вашего компьютера для разработки? Visual Studio Code по умолчанию использует контейнеры amd64 Linux. Если на вашем компьютере для разработки используются контейнеры arm32v7, обновите платформу в синей полосе состояния в нижней части окна VS Code, чтобы она соответствовала вашей платформе контейнеров. Модули C невозможно создать как контейнеры Windows. 

## <a name="deploy-and-run-the-solution"></a>Развертывание и запуск решения

При использовании руководства по настройке устройства IoT Edge модуль был развернут с помощью портала Azure. Модули также можно развертывать с помощью расширения "Набор средств для Центра Интернета вещей Azure" (прежнее название — "Набор средств для Интернета вещей Azure") для Visual Studio Code. У вас уже есть манифест развертывания, подготовленный для вашего сценария (см. файл **deployment.json**). Теперь вам осталось выбрать устройство для получения развертывания.

1. В палитре команд VS Code выполните команду **Центр Интернета вещей Azure: Select IoT Hub** (Центр Интернета вещей Azure: выбрать Центр Интернета вещей).

2. Выберите подписку и Центр Интернета вещей, содержащий устройство IoT Edge, которое нужно настроить.

3. В обозревателе VS Code разверните раздел **Azure IoT Hub Devices** (Устройства Центра Интернета вещей Azure).

4. Щелкните имя устройства IoT Edge правой кнопкой мыши, а затем выберите **Create Deployment for Single Device** (Создание развертывания для одного устройства).

   ![Создание развертывания для одного устройства](./media/tutorial-c-module/create-deployment.png)

5. Выберите файл **deployment.json** в папке **config** и щелкните **Select Edge Deployment Manifest** (Выбрать манифест развертывания Edge). Не используйте файл deployment.template.json.

6. Нажмите кнопку "Обновить". Появится новый модуль **CModule**, работающий вместе с модулями **TempSensor**, **$edgeAgent** и **$edgeHub**.

## <a name="view-generated-data"></a>Просмотр сформированных данных

Когда вы примените манифест развертывания к устройству IoT Edge, в среде выполнения IoT Edge на устройстве начнется сбор сведений о новом развертывании и выполнение операций. Работа всех выполняющихся на устройстве модулей, которые не включены в манифест развертывания, будет остановлена. А модули, которых нет на устройстве, будут запущены.

Сведения о состоянии устройства IoT Edge можно просмотреть в разделе **Azure IoT Hub Devices** (Устройства Центра Интернета вещей Azure) в обозревателе Visual Studio Code. Разверните раздел со сведениями об устройстве, чтобы просмотреть список развернутых и запущенных модулей.

На самом устройстве IoT Edge можно просмотреть состояние развертывания модулей с помощью команды `iotedge list`. Вы увидите четыре модуля: два модуля среды выполнения IoT Edge, tempSensor и пользовательский модуль, который вы создали в рамках этого руководства. Запуск всех модулей может занять несколько минут, поэтому выполните команду повторно, если сразу отображаются не все модули.

Чтобы просмотреть сообщения, создаваемые любым модулем, используйте команду `iotedge logs <module name>`.

Сообщения можно просматривать после их поступления в центр Интернета вещей с помощью Visual Studio Code.

1. Для отслеживания данных, поступающих в Центр Интернета вещей, нажмите кнопку **...** и выберите **Start Monitoring D2C Messages** (Начать мониторинг сообщений D2C).
2. Для отслеживания сообщений D2C для конкретного устройства щелкните его правой кнопкой мыши в списке и выберите **Start Monitoring D2C Messages** (Начать мониторинг сообщений D2C).
3. Чтобы перестать отслеживать данные, в палитре команд выполните команду **Azure IoT Hub: Stop monitoring D2C message** (Центр Интернета вещей: остановить мониторинг сообщений).
4. Для просмотра или обновления двойника модуля щелкните модуль правой кнопкой мыши в списке и выберите **Edit module twin** (Редактирование двойника модуля). Чтобы обновить двойник модуля, сохраните файл JSON двойника, затем щелкните правой кнопкой мыши область редактора и выберите **Update Module Twin** (Обновить двойник модуля).
5. Чтобы просмотреть журналы Docker, можно установить [Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) для VS Code и найти свои запущенные модули локально в обозревателе Docker. В контекстном меню выберите пункт **Show Logs** (Показывать журналы) для просмотра в интегрированном терминале.

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы планируете перейти к следующей рекомендуемой статье, можно сохранить созданные и повторно используемые ресурсы и конфигурации. Это же устройство IoT Edge также можно использовать в качестве тестового устройства.

В противном случае чтобы избежать расходов, можно удалить локальные конфигурации и ресурсы Azure, созданные в рамках этой статьи.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

[!INCLUDE [iot-edge-clean-up-local-resources](../../includes/iot-edge-clean-up-local-resources.md)]


## <a name="next-steps"></a>Дополнительная информация

В этом руководстве вы создали модуль IoT Edge, который содержит код для фильтрации необработанных данных, созданных вашим устройством IoT Edge. Дополнительные сведения о создании собственных модулей см. в разделе [Use Visual Studio Code to develop and debug C modules for Azure IoT Edge](how-to-develop-c-module.md) (Использование Visual Studio Code для разработки и отладки модулей C для IoT Edge Azure). Вы можете перейти к следующим руководствам, чтобы узнать о других способах, с помощью которых Azure IoT Edge может помочь вам превратить данные в бизнес-аналитику на пограничном устройстве.

> [!div class="nextstepaction"]
> [Руководство по хранению данных в пограничной системе с помощью баз данных SQL Server](tutorial-store-data-sql-server.md)

