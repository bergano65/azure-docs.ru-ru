---
title: Руководство по созданию пользовательского модуля Java — Azure IoT Edge | Документация Майкрософт
description: В этом руководстве показано, как создать модуль IoT Edge c кодом Java и развернуть его на пограничном устройстве.
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/25/2018
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: 53be0f36e79d5691d8531c46bf7f554c53f641ee
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/13/2018
ms.locfileid: "53342839"
---
# <a name="tutorial-develop-a-java-iot-edge-module-and-deploy-to-your-simulated-device"></a>Руководство. Разработка модуля IoT Edge с кодом Java и его развертывание на имитированном устройстве

Вы можете использовать модули Azure IoT Edge для развертывания кода, который реализует бизнес-логику непосредственно на устройствах IoT Edge. В этом руководстве рассматриваются создание и развертывание модуля IoT Edge, который фильтрует данные датчика. Вы будете использовать имитированное устройство IoT Edge, созданное с помощью процедуры развертывания Azure IoT Edge на имитированном устройстве, описанной в кратких руководствах для [Windows](quickstart.md) или [Linux](quickstart-linux.md). Из этого руководства вы узнаете, как выполнять следующие задачи:    

> [!div class="checklist"]
> * Использовать Visual Studio Code для создания модуля IoT Edge Java на основе пакета шаблонов Maven для Azure IoT Edge и пакета SDK для устройств Интернета вещей Azure для Java.
> * Использовать Visual Studio Code и Docker для создания образа Docker и его публикации в реестре.
> * Развертывать модуль на устройстве IoT Edge.
> * Просматривать сформированные данные.


Модуль IoT Edge, создаваемый в этом руководстве, фильтрует данные температуры, которые создаются устройством. Оно отправляет сообщения, только если температура превышает заданное пороговое значение. Такой тип пограничного анализа удобен для сокращения объема данных, передаваемых в облако и сохраняемых в нем. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Предварительные требования

Устройство Azure IoT Edge.

* В качестве устройства Azure IoT Edge можно использовать компьютер, на котором ведется разработка, или виртуальную машину. Для этого выполните действия, описанные в кратком руководстве для устройства [Linux](quickstart-linux.md).
* Модули Java для IoT Edge не поддерживают устройства Windows.

Облачные ресурсы.

* [Центр Интернета вещей](../iot-hub/iot-hub-create-through-portal.md) ценовой категории "Бесплатный" или "Стандартный" в Azure. 

Ресурсы разработки.

* [Visual Studio Code](https://code.visualstudio.com/). 
* [Пакет расширения Java](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-pack) для Visual Studio Code.
* [Расширение Azure IoT Edge для Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge). 
* [Пакет SDK для Java SE 10](https://aka.ms/azure-jdks) и [переменная среды `JAVA_HOME`,](https://docs.oracle.com/cd/E19182-01/820-7851/inst_cli_jdk_javahome_t/) которая указывает на эту установку JDK.
* [Maven](https://maven.apache.org/)
* [Docker CE](https://docs.docker.com/install/)
   * Если разработка выполняется на устройстве Windows, убедитесь, что для Docker [настроено использование контейнеров Linux](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers). 


## <a name="create-a-container-registry"></a>Создание реестра контейнеров

В этом руководстве описано, как создать модуль с помощью расширения Azure IoT Edge для Visual Studio Code и **образ контейнера** из файлов. Затем вы отправите этот образ в **реестр**, содержащий ваши образы и управляющий ими. Наконец, вы развернете свой образ из реестра для выполнения на устройстве IoT Edge.  

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
Ниже описано, как создать проект модуля IoT Edge на основе пакета шаблонов Maven для Azure IoT Edge и пакета SDK для Интернета вещей Azure для Java с использованием Visual Studio Code и расширения Azure IoT Edge.

### <a name="create-a-new-solution"></a>Создание решения

Создайте шаблон решения Java, в который вы сможете поместить собственный код. 

1. В Visual Studio Code выберите **Представление** > **Палитра команд** для открытия палитры команд VS Code. 

2. В палитре команд введите и выполните команду **Azure IoT Edge: New IoT Edge Solution** (Azure IoT Edge: создать решение IoT Edge). Следуйте инструкциям на экране в палитре команд для создания решения.

   | Поле | Значение |
   | ----- | ----- |
   | Выбор папки | Выберите расположение на компьютере разработчика для VS Code, чтобы создать файлы решения. |
   | Введите название решения. | Введите описательное имя решения или примите имя по умолчанию **EdgeSolution**. |
   | Выбор шаблона модуля | Выберите **Java Module** (Модуль Java). |
   | Указание значения идентификатора группы | Введите значение идентификатора группы или примите значение по умолчанию **com.edgemodule**. |
   | Указание имени модуля | Присвойте модулю имя **JavaModule**. |
   | Указание репозитория изображений Docker для модуля | Репозиторий изображений включает в себя имя реестра контейнеров и имя образа контейнера. Образ контейнера предварительно заполняется на последнем шаге. Замените **localhost:5000** на значение сервера входа из реестра контейнеров Azure. Вы можете извлечь сервер входа на странице "Обзор" реестра контейнеров на портале Azure. Окончательная строка выглядит так: \<имя_реестра\>.azurecr.io/javamodule. |
 
   ![Выбор репозитория образа Docker](./media/tutorial-java-module/repository.png)
   
Если вы впервые создаете модуль Java, может потребоваться несколько минут на скачивание пакетов Maven. После этого окно VS Code открывает рабочую область решения IoT Edge. Рабочая область решения содержит пять компонентов верхнего уровня. Папка **modules** содержит код Java для модуля и файлы Dockerfile для создания модуля в качестве образа контейнера. Файл **\.env** хранит учетные данные реестра контейнеров. Файл **deployment.template.json** содержит сведения, которые среда выполнения IoT Edge использует для развертывания модулей на устройстве. Файл **deployment.debug.template.json** содержит отладочные версии модулей. Работая с этим руководством, вы не будете изменять папку **\.vscode** или файл **\.gitignore**. 

Если вы не указывали реестр контейнеров при создании решения, но приняли значение по умолчанию localhost:5000, у вас не будет файла \.env. 

### <a name="add-your-registry-credentials"></a>Добавление учетных данных реестра

Файл среды хранит учетные данные для реестра контейнеров и совместно использует их со средой выполнения IoT Edge. Среде выполнения нужны эти учетные данные, чтобы извлечь частный образ на устройство IoT Edge. 

1. Откройте в обозревателе VS Code файл с расширением ENV. 
2. Обновите поля с **именем пользователя** и **паролем**, скопированные из своего реестра контейнера Azure. 
3. Сохраните этот файл. 

### <a name="update-the-module-with-custom-code"></a>Обновление модуля с помощью пользовательского кода

1. В обозревателе VS Code откройте **модули** > **JavaModule** > **src** > **main**  >  **java** > **com** > **edgemodule** > **App.java**.

5. Добавьте в начало файла следующий код, который импортирует новые ссылочные классы.

    ```java
    import java.io.StringReader;
    import java.util.concurrent.atomic.AtomicLong;
    import java.util.HashMap;
    import java.util.Map;
    
    import javax.json.Json;
    import javax.json.JsonObject;
    import javax.json.JsonReader;
    
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.Pair;
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.Property;
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.TwinPropertyCallBack;
    ```

5. Добавьте следующее определение в класс **App**. Эта переменная устанавливает значение, которое должно быть измеренной температурой, чтобы данные были отправлены в Центр Интернета вещей. 

    ```java
    private static final String TEMP_THRESHOLD = "TemperatureThreshold";
    private static AtomicLong tempThreshold = new AtomicLong(25);
    ```

7. В объекте **MessageCallbackMqtt** замените метод execute следующим кодом. Этот метод вызывается каждый раз, когда модуль получает сообщение MQTT из центра IoT Edge. Он отфильтровывает сообщения о температуре ниже порогового значения, настроенного с помощью двойника модуля.

    ```java
        private int counter = 0;
       @Override
        public IotHubMessageResult execute(Message msg, Object context) {
            this.counter += 1;
 
            String msgString = new String(msg.getBytes(), Message.DEFAULT_IOTHUB_MESSAGE_CHARSET);
            System.out.println(
                   String.format("Received message %d: %s",
                            this.counter, msgString));
            if (context instanceof ModuleClient) {
                try (JsonReader jsonReader = Json.createReader(new StringReader(msgString))) {
                    final JsonObject msgObject = jsonReader.readObject();
                    double temperature = msgObject.getJsonObject("machine").getJsonNumber("temperature").doubleValue();
                    long threshold = App.tempThreshold.get();
                    if (temperature >= threshold) {
                        ModuleClient client = (ModuleClient) context;
                        System.out.println(
                            String.format("Temperature above threshold %d. Sending message: %s",
                            threshold, msgString));
                        client.sendEventAsync(msg, eventCallback, msg, App.OUTPUT_NAME);
                    }
                } catch (Exception e) {
                    e.printStackTrace();
                }
            }
            return IotHubMessageResult.COMPLETE;
        }
    ```

8. Добавьте следующие два внутренних статических класса в класс **App**. Эти классы принимают изменения свойств из двойника модуля и соответствующим образом изменяют переменную **tempThreshold**. У каждого модуля есть собственный модуль-двойник, что позволяет настроить код, выполняемый в модуле, непосредственно из облака.

    ```java
    protected static class DeviceTwinStatusCallBack implements IotHubEventCallback {
        @Override
        public void execute(IotHubStatusCode status, Object context) {
            System.out.println("IoT Hub responded to device twin operation with status " + status.name());
        }
    }
 
    protected static class OnProperty implements TwinPropertyCallBack {
        @Override
        public void TwinPropertyCallBack(Property property, Object context) {
            if (!property.getIsReported()) {
                if (property.getKey().equals(App.TEMP_THRESHOLD)) {
                    try {
                        long threshold = Math.round((double) property.getValue());
                        App.tempThreshold.set(threshold);
                    } catch (Exception e) {
                        System.out.println("Faile to set TemperatureThread with exception");
                        e.printStackTrace();
                    }
                }
            }
        }
    }
    ```

9. Добавьте следующие строки в метод **main** после строки **client.open()**, чтобы подписаться на обновления двойника модуля.

    ```java
    client.startTwin(new DeviceTwinStatusCallBack(), null, new OnProperty(), null);
    Map<Property, Pair<TwinPropertyCallBack, Object>> onDesiredPropertyChange = new HashMap<Property, Pair<TwinPropertyCallBack, Object>>() {
        {
            put(new Property(App.TEMP_THRESHOLD, null), new Pair<TwinPropertyCallBack, Object>(new OnProperty(), null));
        }
    };
    client.subscribeToTwinDesiredProperties(onDesiredPropertyChange);
    client.getTwin();
    ```

11. Сохраните этот файл.

12. В обозревателе VS Code откройте файл deployment.template.json в рабочей области IoT Edge. Этот файл указывает, что **$edgeAgent** должен развернуть два модуля: **tempSensor** и **JavaModule**. На панели состояния VS Code для устройства IoT Edge указана платформа по умолчанию **amd64**, т. е. модуль **JavaModule** использует версию образа для Linux amd64. При необходимости измените платформу по умолчанию на панели состояния с **amd64** на **arm32v7** или **windows-amd64** в соответствии с архитектурой вашего устройства IoT Edge. 

   Общие сведения о манифестах развертывания см. в статье [Сведения об использовании, настройке и повторном использовании модулей Azure IoT Edge (предварительная версия)](module-composition.md).

   В файле deployment.template.json есть раздел **registryCredentials**, который хранит учетные данные реестра Docker. Фактическое имя пользователя хранится в файле ENV, который Git игнорирует.  

13. Добавьте двойник модуля **JavaModule** в манифест развертывания. Вставьте следующее содержимое JSON в нижней части раздела **moduleContent** после двойника модуля **$edgeHub**: 

   ```json
       "JavaModule": {
           "properties.desired":{
               "TemperatureThreshold":25
           }
       }
   ```

   ![Добавление двойника модуля в шаблон развертывания](./media/tutorial-java-module/module-twin.png)

14. Сохраните этот файл.

## <a name="build-your-iot-edge-solution"></a>Сборка решения IoT Edge

В предыдущем разделе вы создали решение IoT Edge и добавили в **PythonModule** код для фильтрации сообщений, в которых указана температура компьютера ниже допустимого порогового значения. Теперь необходимо создать решение в качестве образа контейнера и передать его в реестр контейнеров. 

1. Войдите в Docker, введя следующую команду в окне интегрированного терминала Visual Studio Code. Затем можно отправить образ модуля в Реестр контейнеров Azure.
     
   ```csh/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```
   Укажите имя пользователя, пароль и сервер входа, скопированные из Реестра контейнеров Azure при его создании. Вы также можете получить эти значения из раздела **Ключи доступа** в разделе реестра на портале Azure.

2. В обозревателе VS Code щелкните правой кнопкой мыши файл deployment.template.json и выберите **Build and Push IoT Edge solution** (Создать и отправить решение IoT Edge). 

После указания службе Visual Studio Code создать решение, сначала она запишет данные в шаблон развертывания, а затем в новой папке **config** создаст файл deployment.json. Затем выполняются две команды в интегрированном терминале: `docker build` и `docker push`. Они создают код, упаковывают приложение Java в контейнер и отправляют его в реестр контейнеров, который был указан при инициализации решения. 

Полный адрес образа контейнера с тегом можно увидеть в окне интегрированного терминала VS Code. Адрес образа создается на основе информации из файла module.json в формате \<repository\>:\<version\>-\<platform\>. В этом руководстве он должен выглядеть так: registryname.azurecr.io/javamodule:0.0.1-amd64.

## <a name="deploy-and-run-the-solution"></a>Развертывание и запуск решения

При использовании руководства по настройке устройства IoT Edge модуль был развернут с помощью портала Azure. Модули также можно развертывать с помощью расширения Azure IoT Hub Toolkit (прежнее название — Azure IoT Toolkit) для Visual Studio Code. У вас уже есть манифест развертывания, подготовленный для вашего сценария (см. файл **deployment.json**). Теперь вам осталось выбрать устройство для получения развертывания.

1. В палитре команд VS Code выполните команду **Azure: Sign in** (Azure: войти). Следуйте инструкциям, чтобы войти в свою учетную запись Azure. Если вход был выполнен, то этот шаг можно пропустить.

2. В палитре команд VS Code выполните команду **Azure IoT Hub: Select IoT Hub** (Azure IoT Hub: выбрать Центр Интернета вещей). 

3. Выберите подписку и Центр Интернета вещей, содержащий устройство IoT Edge, которое нужно настроить. 

4. В обозревателе VS Code разверните раздел **Azure IoT Hub Devices** (Устройства Центра Интернета вещей Azure). 

5. Щелкните имя устройства IoT Edge правой кнопкой мыши, а затем выберите **Create Deployment for Single Device** (Создание развертывания для одного устройства). 

   ![Создание развертывания для одного устройства](./media/tutorial-java-module/create-deployment.png)

6. Выберите файл **deployment.json** в папке **config** и щелкните **Select Edge Deployment Manifest** (Выбрать манифест развертывания Edge). Не используйте файл deployment.template.json. 

7. Нажмите кнопку "Обновить". Появится новый работающий модуль **JavaModule**, а также модули **TempSensor**, **$edgeAgent** и **$edgeHub**.  

## <a name="view-generated-data"></a>Просмотр сформированных данных

Когда вы примените манифест развертывания к устройству IoT Edge, в среде выполнения IoT Edge на устройстве начнется сбор сведений о новом развертывании и выполнение операций. Работа всех выполняющихся на устройстве модулей, которые не включены в манифест развертывания, будет остановлена. А модули, которых нет на устройстве, будут запущены. 

Сведения о состоянии устройства IoT Edge можно просмотреть в разделе **Azure IoT Hub Devices** (Устройства Центра Интернета вещей Azure) в обозревателе Visual Studio Code. Разверните раздел со сведениями об устройстве, чтобы просмотреть список развернутых и запущенных модулей. 

На самом устройстве IoT Edge можно просмотреть состояние развертывания модулей с помощью команды `iotedge list`. Вы увидите четыре модуля: два модуля среды выполнения IoT Edge, tempSensor и пользовательский модуль, который вы создали в рамках этого руководства. Запуск всех модулей может занять несколько минут, поэтому выполните команду повторно, если сразу отображаются не все модули. 

Чтобы просмотреть сообщения, создаваемые любым модулем, используйте команду `iotedge logs <module name>`. 

Сообщения можно просматривать после их поступления в центр Интернета вещей с помощью Visual Studio Code. 

1. Для отслеживания данных, поступающих в Центр Интернета вещей, нажмите кнопку с многоточием (**...**), а затем выберите **Start Monitoring D2C Messages** (Начать мониторинг сообщений D2C).
2. Для отслеживания сообщений D2C для конкретного устройства щелкните его правой кнопкой мыши в списке и выберите **Start Monitoring D2C Messages** (Начать мониторинг сообщений D2C).
3. Чтобы перестать отслеживать данные, в палитре команд выполните команду **Azure IoT Hub: Stop monitoring D2C message** (Центр Интернета вещей: остановить мониторинг сообщений D2C). 
4. Для просмотра или обновления двойника модуля щелкните модуль правой кнопкой мыши в списке и выберите **Edit module twin** (Редактирование двойника модуля). Чтобы обновить двойник модуля, сохраните двойник файла JSON, затем щелкните правой кнопкой мыши область редактора и выберите **Update Module Twin** (Обновить двойник модуля).
5. Чтобы просмотреть журналы Docker, установите [Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) для VS Code. Локально запущенные модули можно найти в обозревателе Docker. В контекстном меню выберите пункт **Показать журналы** для просмотра в интегрированном терминале.
 
## <a name="clean-up-resources"></a>Очистка ресурсов 

Если вы планируете перейти к следующей рекомендуемой статье, можно сохранить созданные и повторно используемые ресурсы и конфигурации. Это же устройство IoT Edge также можно использовать в качестве тестового устройства. 

В противном случае чтобы избежать расходов, можно удалить локальные конфигурации и ресурсы Azure, созданные в рамках этой статьи. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

[!INCLUDE [iot-edge-clean-up-local-resources](../../includes/iot-edge-clean-up-local-resources.md)]


## <a name="next-steps"></a>Дополнительная информация

В этом руководстве вы создали модуль IoT Edge с кодом для фильтрации необработанных данных, созданных устройством IoT Edge. Перейдите к следующим руководствам, чтобы узнать о других способах, с помощью которых Azure IoT Edge может помочь вам превратить данные в бизнес-аналитику на пограничном устройстве.

> [!div class="nextstepaction"]
> [Руководство по хранению данных в пограничной системе с помощью баз данных SQL Server](tutorial-store-data-sql-server.md)

