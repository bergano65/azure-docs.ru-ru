---
title: Создание пользовательского модуля Python — Azure IoT Edge | Документация Майкрософт
description: В этом руководстве показано, как создать модуль IoT Edge с кодом Python и его развертывание на пограничном устройстве.
services: iot-edge
author: shizn
manager: philmea
ms.reviewer: kgremban
ms.author: xshi
ms.date: 03/24/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: 55cdb70a02aae62a69f4fc930f4c6ee385a24702
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68839548"
---
# <a name="tutorial-develop-and-deploy-a-python-iot-edge-module-for-linux-devices"></a>Руководство по разработке модуля IoT Edge на языке Python и его развертыванию на устройствах Linux

использованию Visual Studio Code для разработки и развертывания кода C на устройствах Linux с Azure IoT Edge. 

Вы можете использовать модули Azure IoT Edge для развертывания кода, который реализует бизнес-логику непосредственно на устройствах IoT Edge. В этом руководстве рассматривается создание и развертывание модуля IoT Edge, который фильтрует данные датчика на устройстве IoT Edge, настроенном с помощью краткого руководства. Из этого руководства вы узнаете, как выполнять следующие задачи:    

> [!div class="checklist"]
> * создание модуля Python для IoT Edge с помощью Visual Studio Code;
> * использование Visual Studio Code и Docker для создания образа Docker и его публикация в реестре;
> * Развертывать модуль на устройстве IoT Edge.
> * Просматривать сформированные данные.


Модуль IoT Edge, создаваемый в этом руководстве, фильтрует данные температуры, которые создаются устройством. Оно отправляет сообщения, только если температура превышает заданное пороговое значение. Такой тип пограничного анализа удобен для сокращения объема данных, передаваемых в облако и сохраняемых в нем. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="solution-scope"></a>Область действия решения

Это руководстве показано разработку модуля на языке **Python** с помощью **Visual Studio Code** и его развертывание на **устройстве Linux**. IoT Edge не поддерживает модули Python для устройств Windows. 

В следующей таблице перечислены возможные варианты для разработки и развертывания модулей Python для Linux. 

| Python | Visual Studio Code | Visual Studio 2017 или 2019 | 
| - | ------------------ | ------------------ |
| **Linux AMD64** | ![Использование VS Code для модулей Python в Linux AMD64](./media/tutorial-c-module/green-check.png) |  |
| **Linux ARM32** | ![Использование VS Code для модулей Python в Linux ARM32](./media/tutorial-c-module/green-check.png) |  |

## <a name="prerequisites"></a>Предварительные требования

Предполагается, что перед началом работы с этим руководством вы прошли предыдущее, в рамках которого настроили окружение для разработки контейнеров Linux: [Develop IoT Edge modules for Linux devices](tutorial-develop-for-linux.md) (Разработка модулей IoT Edge для устройств Linux). После работы с любым из учебников у вас должны быть готовы все необходимые компоненты: 

* [Центр Интернета вещей](../iot-hub/iot-hub-create-through-portal.md) ценовой категории "Бесплатный" или "Стандартный" в Azure.
* [устройство Linux, на котором выполняется Azure IoT Edge](quickstart-linux.md);
* реестр контейнеров, например [Реестр контейнеров Azure](https://docs.microsoft.com/azure/container-registry/);
* средство [Visual Studio Code](https://code.visualstudio.com/), настроенное с помощью [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools);
* выпуск [Docker CE](https://docs.docker.com/install/), настроенный для выполнения контейнеров Linux.

Для разработки модуля IoT Edge на языке Python установите на компьютере разработки следующие дополнительные компоненты: 

* [Расширение Visual Studio Code для Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python). 
* [Python](https://www.python.org/downloads/).
* [PIP](https://pip.pypa.io/en/stable/installing/#installation) для установки пакетов Python (обычно входит в состав установки Python).

>[!Note]
>Убедитесь, что папка `bin` расположена в соответствии с требованиями вашей платформы. Обычно это `~/.local/` для UNIX и macOS или `%APPDATA%\Python` для Windows.

## <a name="create-a-module-project"></a>Создание проекта модуля
Описанный ниже процесс позволяет создать модуль Python для IoT Edge с использованием Visual Studio Code и средств Azure IoT.

### <a name="create-a-new-project"></a>Создание нового проекта

С помощью VS Code создайте шаблон решений Python, на основе которого можно выполнить сборку. 

1. В Visual Studio Code выберите **Вид** > **Терминал**, чтобы открыть интегрированный терминал VS Code.

1. Выберите **Представление** > **Палитра команд** для открытия палитры команд VS Code. 

1. В палитре команд введите и выполните команду **Azure: Sign in** (Azure: войти). Следуйте инструкциям, чтобы войти в свою учетную запись Azure. Если вход был выполнен, то этот шаг можно пропустить.

1. В палитре команд введите и выполните команду **Azure IoT Edge: New IoT Edge Solution** (Azure IoT Edge: создать решение IoT Edge). Выполняя инструкции, укажите следующие сведения для создания решения:

   | Поле | Значение |
   | ----- | ----- |
   | Выбор папки | Выберите расположение на компьютере разработчика для VS Code, чтобы создать файлы решения. |
   | Введите название решения. | Введите описательное имя решения или примите имя по умолчанию **EdgeSolution**. |
   | Выбор шаблона модуля | Выберите **Модуль Python**. |
   | Указание имени модуля | Назовите модуль **PythonModule**. |
   | Указание репозитория изображений Docker для модуля | Репозиторий изображений включает в себя имя реестра контейнеров и имя образа контейнера. Образ контейнера предварительно заполняется именем, которое вы указали на последнем шаге. Замените **localhost:5000** на значение сервера входа из реестра контейнеров Azure. Вы можете извлечь сервер входа на странице "Обзор" реестра контейнеров на портале Azure. <br><br>Окончательный репозиторий образов выглядит так: \<имя_реестра\>.azurecr.io/pythonmodule. |
 
   ![Выбор репозитория образа Docker](./media/tutorial-python-module/repository.png)


### <a name="add-your-registry-credentials"></a>Добавление учетных данных реестра

Файл среды хранит учетные данные для репозитория контейнеров и совместно использует их со средой выполнения IoT Edge. Среде выполнения нужны эти учетные данные, чтобы извлечь частный образ на устройство IoT Edge. 

1. Откройте в обозревателе VS Code файл с расширением **ENV**. 
2. Обновите поля с **именем пользователя** и **паролем**, скопированные из своего реестра контейнера Azure. 
3. Сохраните ENV-файл. 

### <a name="select-your-target-architecture"></a>Выбор целевой архитектуры

Сейчас Visual Studio Code позволяет разрабатывать модули C для устройств Linux AMD64 и Linux ARM32v7. Для каждого решения вам нужно выбрать одну целевую платформу, так как сборка и запуск контейнера для разных архитектур различается. По умолчанию используется Linux AMD64. 

1. Откройте палитру команд и выполните поиск **Azure IoT Edge: Set Default Target Platform for Edge Solution** (Azure IoT Edge: установить целевую платформу по умолчанию для решения Edge) или выберите значок ярлыка на боковой панели в нижней части окна. 

2. В палитре команд выберите целевую архитектуру из списка параметров. В рамках этого руководства мы используем в качестве устройства IoT Edge виртуальную машину Ubuntu. Поэтому сохраним значение по умолчанию **amd64**. 

### <a name="update-the-module-with-custom-code"></a>Обновление модуля с помощью пользовательского кода

Каждый шаблон содержит пример кода, который принимает имитируемые данные с датчиков модуля **SimulatedTemperatureSensor** и направляет их в Центр Интернета вещей. В этом разделе добавьте код, который расширяет **PythonModule**, для анализа сообщений перед их отправкой. 

1. В обозревателе VS Code откройте **modules** > **PythonModule** > **main.py**.

2. В верхней части файла **main.py** импортируйте библиотеку **json**:

    ```python
    import json
    ```

3. Добавьте переменные **TEMPERATURE_THRESHOLD** и **TWIN_CALLBACKS** в разделе глобальных счетчиков. Порог температуры задает значение измеренной температуры компьютера, при превышении которого данные отправляются в Центр Интернета вещей.

    ```python
    TEMPERATURE_THRESHOLD = 25
    TWIN_CALLBACKS = 0
    ```

4. Замените функцию **receive_message_callback** следующим кодом:

    ```python
    # receive_message_callback is invoked when an incoming message arrives on the specified 
    # input queue (in the case of this sample, "input1").  Because this is a filter module, 
    # we forward this message to the "output1" queue.
    def receive_message_callback(message, hubManager):
        global RECEIVE_CALLBACKS
        global TEMPERATURE_THRESHOLD
        message_buffer = message.get_bytearray()
        size = len(message_buffer)
        message_text = message_buffer[:size].decode('utf-8')
        print ( "    Data: <<<%s>>> & Size=%d" % (message_text, size) )
        map_properties = message.properties()
        key_value_pair = map_properties.get_internals()
        print ( "    Properties: %s" % key_value_pair )
        RECEIVE_CALLBACKS += 1
        print ( "    Total calls received: %d" % RECEIVE_CALLBACKS )
        data = json.loads(message_text)
        if "machine" in data and "temperature" in data["machine"] and data["machine"]["temperature"] > TEMPERATURE_THRESHOLD:
            map_properties.add("MessageType", "Alert")
            print("Machine temperature %s exceeds threshold %s" % (data["machine"]["temperature"], TEMPERATURE_THRESHOLD))
        hubManager.forward_event_to_output("output1", message, 0)
        return IoTHubMessageDispositionResult.ACCEPTED
    ```

5. Добавьте новую функцию **module_twin_callback**. Эта функция вызывается при обновлении нужного свойства.

    ```python
    # module_twin_callback is invoked when the module twin's desired properties are updated.
    def module_twin_callback(update_state, payload, user_context):
        global TWIN_CALLBACKS
        global TEMPERATURE_THRESHOLD
        print ( "\nTwin callback called with:\nupdateStatus = %s\npayload = %s\ncontext = %s" % (update_state, payload, user_context) )
        data = json.loads(payload)
        if "desired" in data and "TemperatureThreshold" in data["desired"]:
            TEMPERATURE_THRESHOLD = data["desired"]["TemperatureThreshold"]
        if "TemperatureThreshold" in data:
            TEMPERATURE_THRESHOLD = data["TemperatureThreshold"]
        TWIN_CALLBACKS += 1
        print ( "Total calls confirmed: %d\n" % TWIN_CALLBACKS )
    ```

6. В класс **HubManager** добавьте новую строку в метод **__init__** для инициализации функции **module_twin_callback**, которую вы только что добавили:

    ```python
    # Sets the callback when a module twin's desired properties are updated.
    self.client.set_module_twin_callback(module_twin_callback, self)
    ```

7. Сохраните файл main.py.

8. В обозревателе VS Code откройте файл **deployment.template.json** в рабочей области решения IoT Edge. 

9. Добавьте двойник модуля **PythonModule** в манифест развертывания. Вставьте следующее содержимое JSON в нижней части раздела **moduleContent** после двойника модуля **$edgeHub**: 

   ```json
       "PythonModule": {
           "properties.desired":{
               "TemperatureThreshold":25
           }
       }
   ```

   ![Добавление двойника модуля в шаблон развертывания](./media/tutorial-python-module/module-twin.png)

10. Сохраните файл deployment.template.json.

## <a name="build-and-push-your-module"></a>Сборка и отправка модуля

В предыдущем разделе вы создали решение IoT Edge и добавили код в PythonModule, который будет отфильтровывать сообщения, где указанная температура компьютера находится в рамках допустимых ограничений. Теперь необходимо создать решение в качестве образа контейнера и передать его в реестр контейнеров.

1. Откройте интегрированный терминал VS Code, выбрав **Вид** > **Терминал**.

1. Войдите в Docker, введя следующую команду в окне терминала. Выполните вход в систему, используя имя пользователя, пароль и сервер входа из реестра контейнеров Azure. Вы можете получить эти значения в разделе **Ключи доступа** в разделе реестра на портале Azure.
     
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

4. Нажмите кнопку "Обновить". Появится новый запущенный модуль **PythonModule**, модуль **SimulatedTemperatureSensor**, а также **$edgeAgent** и **$edgeHub**. 

## <a name="view-generated-data"></a>Просмотр сформированных данных

Когда вы примените манифест развертывания к устройству IoT Edge, в среде выполнения IoT Edge на устройстве начнется сбор сведений о новом развертывании и выполнение операций. Работа всех выполняющихся на устройстве модулей, которые не включены в манифест развертывания, будет остановлена. А модули, которых нет на устройстве, будут запущены.

Сведения о состоянии устройства IoT Edge можно просмотреть в разделе **Azure IoT Hub Devices** (Устройства Центра Интернета вещей Azure) в обозревателе Visual Studio Code. Разверните раздел со сведениями об устройстве, чтобы просмотреть список развернутых и запущенных модулей.

1. В обозревателе Visual Studio Code щелкните имя устройства IoT Edge правой кнопкой мыши и выберите **Start Monitoring Built-in Event Endpoint** (Начать мониторинг строенной конечной точки событий).

2. Просмотрите сообщения, поступающие в Центр Интернета вещей. Для поступления сообщений может потребоваться некоторое время, так как устройство IoT Edge должно сначала получить новое развертывание и запустить все модули. После этого измененный нами код PythonModule ожидает, пока температура компьютера не достигнет 25 градусов, и лишь затем отправляет сообщения. Он также присваивает тип **Предупреждение** всем сообщениям, которые сообщают о достижении порогового значения температуры. 

## <a name="edit-the-module-twin"></a>Изменение двойника модуля

Мы использовали двойник модуля PythonModule в манифесте развертывания, чтобы задать порог температуры на уровне 25 градусов. С помощью двойника модуля вы можете изменять функциональные возможности без необходимости обновлять код модуля.

1. В Visual Studio Code разверните подробные сведения об устройстве IoT Edge, чтобы просмотреть список выполняющихся модулей. 

2. Щелкните **PythonModule** правой кнопкой мыши и выберите **Edit module twin** (Изменение двойника модуля). 

3. В списке требуемых свойств Найдите **TemperatureThreshold**. Измените его значение, увеличив температуру на 5 или 10 градусов относительно последней сообщаемой температуры. 

4. Сохраните файл двойника модуля.

5. Щелкните правой кнопкой мыши в любой точки на панели редактирования двойника модуля и выберите **Update module twin** (Обновление двойника модуля). 

6. Выполните мониторинг сообщений, поступающих с устройства в облако. Вы увидите, что поток сообщений прекратится, пока не будет достигнут новый порог температуры. 

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