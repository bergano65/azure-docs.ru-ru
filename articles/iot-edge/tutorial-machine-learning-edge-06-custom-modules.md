---
title: Машинное обучение в Azure IoT Edge. Создание и развертывание пользовательских модулей | Документация Майкрософт
description: Узнайте, как создавать и развертывать модули IoT Edge, которые обрабатывают данные от конечных устройств с использованием модели машинного обучения, а затем отправляют аналитические сведения в Центр Интернета вещей.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/13/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 4d03e5ee5faf39425e1bf927a3c0557b0ad01b82
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68840104"
---
# <a name="tutorial-create-and-deploy-custom-iot-edge-modules"></a>Руководство по Создание и развертывание пользовательских модулей IoT Edge

> [!NOTE]
> Эта статья входит в серию учебников по использованию Машинного обучения Azure в IoT Edge. Если вы перешли к этой статье по прямой ссылке, для оптимальных результатов рекомендуем начать с изучения [первой статьи](tutorial-machine-learning-edge-01-intro.md) этой серии.

В этой статье описано, как создать три модуля IoT Edge, которые получают сообщения от конечных устройств, обрабатывают данные через модель машинного обучения и передают аналитические сведения в Центр Интернета вещей.

Центр IoT Edge облегчает обмен данными между модулями. Использование Центра IoT Edge в качестве брокера сообщений позволяет сохранять независимость модулей друг от друга. Модулям необходимо указывать только те входы, на которые они принимают сообщения, и выходы, на которые они записывают сообщения.

Нам нужно, чтобы устройство IoT Edge выполняло четыре задачи:

* получало данные от конечных устройств;
* прогнозировало оставшийся срок полезного использования (RUL) для устройства, которое отправило эти данные;
* отправляло сообщение с указанием RUL для устройства в Центр Интернета вещей (эту функцию можно изменить так, чтобы данные отправлялись, только когда текущее значение RUL окажется ниже указанного);
* сохраняло данные, полученные от конечных устройств, в локальный файл на устройстве IoT Edge. Этот файл данных периодически загружается в Центр Интернета вещей через функцию отправки файла, что позволяет оптимизировать модель машинного обучения. Отправка файла — более экономный способ по сравнению с постоянной потоковой передачей сообщений.

Для выполнения этих задач мы применим следующие три пользовательских модуля:

* **Классификатор RUL**. Модуль turboFanRulClassifier, описанный в статье [Обучение и развертывание модели Машинного обучения Azure](tutorial-machine-learning-edge-04-train-model.md), — это стандартный модуль машинного обучения с входом amlInput и выходом amlOutput. Вход amlInput ожидает данные точно в том же формате, который мы используем для отправки данных в веб-службу на основе ACI. Аналогичным образом, выход amlOutput возвращает такие же данные, как и веб-служба.

* **Модуль записи Avro**. Этот модуль получает сообщения на вход avroModuleInput и сохраняет их на диск в формате Avro для последующей отправки в Центр Интернета вещей.

* **Модуль маршрутизатора**. Модуль маршрутизатора получает сообщения от подчиненных конечных устройств, форматирует эти сообщения и передает далее в классификатор. После этого он получает ответные сообщения от классификатора и перенаправляет их в модуль записи Avro. И наконец, этот модуль отправляет в Центр Интернета вещей прогнозное значение RUL.

  * Входные данные:
    * **deviceInput** получает сообщения от конечных устройств;
    * **rulInput** получает сообщения от amlOutput.

  * Выходные данные:
    * **classify** отправляет сообщения в amlInput;
    * **writeAvro** отправляет сообщения в avroModuleInput;
    * **toIotHub** отправляет сообщения в $upstream, откуда они передаются в подключенный Центр Интернета вещей.

На рисунке ниже показаны модули, входы, выходы и маршруты Центра Интернета вещей в пределах всего решения:

![Архитектура трех модулей IoT Edge](media/tutorial-machine-learning-edge-06-custom-modules/modules-diagram.png)

Описанные в этой статье действия обычно выполняются разработчиком облачной системы.

## <a name="create-a-new-iot-edge-solution"></a>Создание решения IoT Edge

С использованием второй из двух используемых Записных книжек Azure, мы создали и опубликовали образ контейнера с моделью RUL. Машинное обучение Azure в процессе создания образа встроило в него элементы поддержки развертывания в качестве модуля Azure IoT Edge. На этом шаге мы создадим решение Azure IoT Edge на основе модуля Машинного обучения Azure и подключим к этому модулю образ, который мы опубликовали с помощью Записных книжек Azure.

1. Откройте сеанс удаленного рабочего стола для подключения к компьютеру разработки.

2. Откройте папку **C:\\source\\IoTEdgeAndMlSample** в Visual Studio Code.

3. Щелкните правой кнопкой панель обозревателя (в свободном месте) и выберите **Создать решение IoT Edge**.

    ![Создание решения IoT Edge](media/tutorial-machine-learning-edge-06-custom-modules/new-edge-solution-command.png)

4. Подтвердите для решения имя по умолчанию **EdgeSolution**.

5. Выберите шаблон модуля **Машинное обучение Azure**.

6. Присвойте модулю имя **turbofanRulClassifier**.

7. Выберите рабочую область машинного обучения.

8. Выберите образ, который вы создали с использованием Записной книжки Azure.

9. Изучите решение и созданные файлы:

   * **deployment.template.json**. Этот файл содержит определения всех модулей в решении. В этом файле стоит обратить внимание на три раздела.

     * **Учетные данные реестров**. Здесь определен набор пользовательских реестров, которые вы используете в решении. Сейчас здесь должен быть реестр из рабочей области машинного обучения, в которой хранится образ Машинного обучения Azure. Число используемых реестров контейнеров не ограничено, но в этом примере для простоты мы будем использовать один реестр для всех модулей.

       ```json
       "registryCredentials": {
         "<your registry>": {
           "username": "$CONTAINER_REGISTRY_USERNAME_<your registry>",
           "password": "$CONTAINER_REGISTRY_PASSWORD_<your registry>",
           "address": "<your registry>.azurecr.io”
         }
       }
       ```

     * **Модули**. Этот раздел содержит набор определяемых пользователем модулей, которые входят в это решение. Сейчас в нем определены два модуля: SimulatedTemperatureSensor и turbofanRulClassifier. SimulatedTemperatureSensor устанавливается из шаблона Visual Studio Code, но для этого решения он не нужен. Определение модуля SimulatedTemperatureSensor можно удалить из раздела модулей. Обратите внимание, что определение модуля turbofanRulClassifier указывает на образ, размещенный в реестре контейнеров. Здесь же будут появляться новые модули, которые мы постепенно добавим в решение.

       ```json
       "modules": {
         "SimulatedTemperatureSensor": {
           "version": "1.0",
           "type": "docker",
           "status": "running",
           "restartPolicy": "always",
           "settings": {
             "image": "mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0",
             "createOptions": {}
           }
         },
         "turbofanRulClassifier": {
           "version": "1.0",
           "type": "docker",
           "status": "running",
           "restartPolicy": "always",
           "settings": {
             "image": "<your registry>.azurecr.io/edgemlsample:1",
             "createOptions": {}
           }
         }
       }
       ```

     * **Маршруты**. В этом руководстве мы будем много работать с маршрутами. Они определяют, как взаимодействуют между собой модули. Два маршрута, определенные шаблоном, не подходят для наших задач. Первый маршрут отправляет все выходные данные из классификатора в Центр Интернета вещей ($upstream). Второй предназначен для SimulatedTemperatureSensor, который мы только что удалили. Удалите оба эти маршрута.

       ```json
       "$edgeHub": {
         "properties.desired": {
           "schemaVersion": "1.0",
           "routes": {
             "turbofanRulClassifierToIoTHub": "FROM /messages/modules/turbofanRulClassifier/outputs/\* INTO $upstream",
             "sensorToturbofanRulClassifier": "FROM /messages/modules/SimulatedTemperatureSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\\"/modules/turbofanRulClassifier/inputs/input1\\")"
           },
           "storeAndForwardConfiguration": {
             "timeToLiveSecs": 7200
           }
         }
       }
       ```

   * Файл **deployment.debug.template.json** содержит отладочную версию deployment.template.json. Нам нужно отражать в этом файле все изменения, вносимые в файл deployment.template.json.

   * Файл **.env** используется для предоставления имени пользователя и пароля для доступа к реестру.

      ```env
      CONTAINER_REGISTRY_USERNAME_<your registry name>=<ACR username>
      CONTAINER_REGISTRY_PASSWORD_<your registry name>=<ACR password>
      ```

10. Щелкните правой кнопкой мыши файл deployment.template.json в Visual Studio Code и выберите **Компилировать решение IoT Edge**.

11. Обратите внимание на то, что эта команда создает папку конфигурации с файлом deployment.amd64.json. Этот файл содержит шаблон развертывания для решения.

## <a name="add-router-module"></a>Добавление модуля маршрутизатора

Теперь мы добавим в решение модуль маршрутизатора. Модуль маршрутизатора отвечает в этом решении за следующие функции:

* **Получение сообщений от конечных устройств**. При поступлении сообщений от подчиненных устройств на устройство IoT Edge это сообщение передается в модуль маршрутизатора, который оркестрирует его маршрутизацию.
* **Отправка сообщений в модуль классификатора RUL**. При получении нового сообщения от подчиненного устройства модуль маршрутизатора преобразует его в соответствующий формат для классификатора RUL. Затем маршрутизатор отправляет сообщение в классификатор RUL, чтобы получить прогнозное значение. Создав прогноз, классификатор отправляет ответное сообщение в модуль маршрутизатора.
* **Отправка сообщений со значением RUL в Центр Интернета вещей**. Когда маршрутизатор получает сообщения от классификатора, он извлекает из него требуемую информацию (идентификатор устройства и значение RUL) и отправляет сокращенное сообщение в Центр Интернета вещей. Благодаря дополнительному изменению, которое мы еще не внесли, сообщения будут отправляться в Центр Интернета вещей, только если прогнозное значение RUL окажется ниже заданного порога (например, менее 100 циклов). Такая фильтрация позволит уменьшить объем сообщений и снизить затраты на Центр Интернета вещей.
* **Отправка сообщений в модуль записи Avro.** Чтобы сохранить все данные, отправленные с подчиненного устройства, модуль маршрутизатора отправляет все сообщения, полученные от модуля классификатора, в модуль записи Avro. Он отвечает за хранение и загрузку данных через механизм отправки файлов в Центр Интернета вещей.

> [!NOTE]
> Из описания поведения модулей может создать впечатление, что обработка выполняется линейно. На самом деле весь этот поток управляется сообщениями и событиями. Для этого используется модуль оркестрации. В нашем примере — это модуль маршрутизатора.

### <a name="create-module-and-copy-files"></a>Создание модуля и копирование файлов

1. Щелкните правой кнопкой папку модулей в Visual Studio Code и выберите **Добавить модуль IoT Edge**.

2. Выберите **Модуль C#** .

3. Присвойте модулю имя **turbofanRouter**.

4. Когда появится запрос на выбор репозитория образов Docker, укажите реестр из рабочей области машинного обучения (этот реестр можно найти в узле registryCredentials файла *deployment.template.json*). Это значение содержит полный адреса реестра в формате **\<имя_реестра\>.azurecr.io/turbofanrouter**.

    > [!NOTE]
    > В этой статье мы используем Реестр контейнеров Azure, созданный в рабочей области Службы машинного обучения Azure, в который мы обучали и развертывали классификатор. Мы выбрали такой вариант исключительно для удобства. Мы можем создать новый реестр контейнеров и опубликовать в нем наши модули.

5. Откройте новое окно терминала в Visual Studio Code (**Вид** > **Терминал**) и скопируйте файлы из каталога модулей.

    ```cmd
    copy c:\source\IoTEdgeAndMlSample\EdgeModules\modules\turbofanRouter\*.cs c:\source\IoTEdgeAndMlSample\EdgeSolution\modules\turbofanRouter\
    ```

6. Когда появится предложение перезаписать файл program.cs, нажмите `y`, а затем `Enter`.

### <a name="build-router-module"></a>Компиляция модуля маршрутизатора

1. В Visual Studio Code выберите действие **Терминал** > **Настроить задачу сборки по умолчанию**.

2. Щелкните **Создать файл tasks.json из шаблона**.

3. Щелкните **.NET Core**.

4. Когда откроется файл tasks.json, замените его содержимое приведенным ниже кодом.

    ```json
    {
      // See https://go.microsoft.com/fwlink/?LinkId=733558
      // for the documentation about the tasks.json format
      "version": "2.0.0",
      "tasks": [
        {
          "label": "build",
          "command": "dotnet",
          "type": "shell",
          "group": {
            "kind": "build",
            "isDefault": true
          },
          "args": [
            "build",
            "${workspaceFolder}/modules/turbofanRouter"
          ],
          "presentation": {
            "reveal": "always"
          },
          "problemMatcher": "$msCompile"
        }
      ]
    }
    ```

5. Сохраните и закройте файл tasks.json.

6. Запустите сборку, нажав клавишу `Ctrl + Shift + B` или выбрав **Терминал** > **Запустить задачу сборки**.

### <a name="set-up-module-routes"></a>Настройка маршрутов модуля

Как упоминалось выше, среда выполнения IoT Edge использует маршруты, заданные в файле *deployment.template.json*, для управления взаимодействием слабо связанных модулей. В этом разделе мы подробнее разберемся с настройкой маршрутов для модуля turbofanRouter. Сначала мы опишем входные маршруты, а затем перейдем к выходам.

#### <a name="inputs"></a>Входные данные

1. В методе Init() файла Program.cs мы зарегистрируем для модуля два обратных вызова:

   ```csharp
   await ioTHubModuleClient.SetInputMessageHandlerAsync(EndpointNames.FromLeafDevice, LeafDeviceInputMessageHandler, ioTHubModuleClient);
   await ioTHubModuleClient.SetInputMessageHandlerAsync(EndpointNames.FromClassifier, ClassifierCallbackMessageHandler, ioTHubModuleClient);
   ```

2. Первый обратный вызов прослушивает сообщения, отправляемые в приемник **deviceInput**. Из приведенной выше схемы видно, что на этот вход нужно передавать все сообщения из от конечных устройств. В файле *deployment.template.json* добавьте маршрут, чтобы Центр IoT Edge направлял все полученные устройством IoT Edge сообщения, кроме отправленных модулями самого устройства IoT Edge, на вход deviceInput в модуле turbofanRouter:

   ```json
   "leafMessagesToRouter": "FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO BrokeredEndpoint(\"/modules/turbofanRouter/inputs/deviceInput\")"
   ```

3. Теперь добавьте маршрут для сообщений, направленных из модуля rulClassifier в модуль turbofanRouter:

   ```json
   "classifierToRouter": "FROM /messages/modules/turbofanRulClassifier/outputs/amloutput INTO BrokeredEndpoint(\"/modules/turbofanRouter/inputs/rulInput\")"
   ```

#### <a name="outputs"></a>outputs

Добавьте в параметр маршрутизации $edgeHub четыре дополнительные маршрута, которые будут обрабатывать выходы модуля маршрутизатора.

1. В файле program.cs определен метод SendMessageToClassifier(), который клиент модуля использует для отправки сообщений в классификатору RUL по этому маршруту:

   ```json
   "routerToClassifier": "FROM /messages/modules/turbofanRouter/outputs/classOutput INTO BrokeredEndpoint(\"/modules/turbofanRulClassifier/inputs/amlInput\")"
   ```

2. SendRulMessageToIotHub() использует клиент модуля для отправки данных RUL для устройства в Центр Интернета вещей по этому маршруту:

   ```json
   "routerToIoTHub": "FROM /messages/modules/turboFanRouter/outputs/hubOutput INTO $upstream"
   ```

3. SendMessageToAvroWriter() использует клиент модуля для отправки сообщения с новыми данными RUL в модуль avroFileWriter.

   ```json
   "routerToAvro": "FROM /messages/modules/turbofanRouter/outputs/avroOutput INTO BrokeredEndpoint(\"/modules/avroFileWriter/inputs/avroModuleInput\")"
   ```

4. HandleBadMessage() передает сообщения, которые не удалось отправить, в вышестоящий Центр Интернета вещей, где они могут быть обработаны позже.

   ```json
   "deadLetter": "FROM /messages/modules/turboFanRouter/outputs/deadMessages INTO $upstream"
   ```

Когда вы соберете все нужные маршруты, узел $edgeHub будет содержать примерно такой код JSON:

```json
"$edgeHub": {
  "properties.desired": {
    "schemaVersion": "1.0",
    "routes": {
      "leafMessagesToRouter": "FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO BrokeredEndpoint(\"/modules/turbofanRouter/inputs/deviceInput\")",
      "classifierToRouter": "FROM /messages/modules/turbofanRulClassifier/outputs/amlOutput INTO BrokeredEndpoint(\"/modules/turbofanRouter/inputs/rulInput\")",
      "routerToClassifier": "FROM /messages/modules/turbofanRouter/outputs/classOutput INTO BrokeredEndpoint(\"/modules/turbofanRulClassifier/inputs/amlInput\")",
      "routerToIoTHub": "FROM /messages/modules/turboFanRouter/outputs/hubOutput INTO $upstream",
      "routerToAvro": "FROM /messages/modules/turbofanRouter/outputs/avroOutput INTO BrokeredEndpoint(\"/modules/avroFileWriter/inputs/avroModuleInput\")",
      "deadLetter": "FROM /messages/modules/turboFanRouter/outputs/deadMessages INTO $upstream"
    },
    "storeAndForwardConfiguration": {
      "timeToLiveSecs": 7200
    }
  }
}
```

> [!NOTE]
> При добавлении модуля turbofanRouter создается еще один дополнительный маршрут: `turbofanRouterToIoTHub": "FROM /messages/modules/turbofanRouter/outputs/* INTO $upstream`. Удалите его, чтобы в файле deployment.template.json остались только перечисленные выше маршруты.

#### <a name="copy-routes-to-deploymentdebugtemplatejson"></a>Скопируйте маршруты в файл deployment.debug.template.json.

Наконец, чтобы синхронизировать файлы, скопируйте изменения, внесенные в файл deployment.template.json, в файл deployment.debug.template.json.

## <a name="add-avro-writer-module"></a>Добавление модуля записи Avro

Модуль записи Avro выполняет в нашем решении две функции: сохранение сообщений и передача файлов.

* **Сохранение сообщений**. Когда модуль записи Avro получает сообщение, он записывает его в локальную файловую систему в формате Avro. Мы используем подключение с привязкой, которое подключает каталог (/data/avrofiles в нашем примере) в указанный путь в контейнере модуля. Такое подключение позволяет модулю сохранять данные по локальному пути (/avrofiles), чтобы сделать их доступными для устройства IoT Edge.

* **Отправка файлов**. Модуль записи Avro использует функцию передачи файлов Центра Интернета вещей, чтобы передать файлы в учетную запись хранилища Azure. Успешно переданный файл удаляется модулем с диска.

### <a name="create-module-and-copy-files"></a>Создание модуля и копирование файлов

1. В палитре команд найдите и щелкните **Python. Select Interpreter** (Python: выбор интерпретатора).

1. Выберите интерпретатор, расположенный в каталоге C:\\Python37.

1. Снова откройте палитру команд, найдите и щелкните **Терминал. Выбрать оболочку по умолчанию**.

1. В ответ на запрос выберите вариант **Командная строка**.

1. Откройте новую оболочку терминала: **Терминал** > **Новый терминал**.

1. Щелкните правой кнопкой папку модулей в Visual Studio Code и выберите **Добавить модуль IoT Edge**.

1. Выберите **Модуль Python**.

1. Присвойте модулю имя avroFileWriter.

1. Когда появится запрос на выбор репозитория образов Docker, укажите тот же реестр, что и при добавлении модуля маршрутизатора.

1. Скопируйте файлы модуля из примера в решение.

   ```cmd
   copy C:\source\IoTEdgeAndMlSample\EdgeModules\modules\avroFileWriter\*.py C:\source\IoTEdgeAndMlSample\EdgeSolution\modules\avroFileWriter\
   ```

1. Если будет предложено перезаписать файл main.py, введите `y` и нажмите клавишу `Enter`.

1. Обратите внимание на то, что в решении появились файлы filemanager.py и schema.py, а также обновлен файл main.py.

> [!NOTE]
> Когда вы откроете файл Python, может появиться запрос на установку pylint. Но для работы с этим руководством анализатор кода вам не нужен.

### <a name="bind-mount-for-data-files"></a>Подключение с привязкой для файлов данных

Как уже упоминалось во введении, для модуля записи требуется подключение с привязкой, чтобы сохранять файлы Avro в локальной файловой системе устройства.

#### <a name="add-directory-to-device"></a>Добавление каталога на устройство

1. Подключитесь к виртуальной машине устройства IoT Edge по протоколу SSH.

   ```bash
   ssh -l <user>@IoTEdge-<extension>.<region>.cloudapp.azure.com
   ```

2. Создайте каталог, в котором будут храниться сообщения от конечного устройства.

   ```bash
   sudo mkdir -p /data/avrofiles
   ```

3. Обновите разрешения для каталога, чтобы контейнер мог записывать в него данные.

   ```bash
   sudo chmod ugo+rw /data/avrofiles
   ```

4. Убедитесь, что каталог теперь доступен для записи (имеет разрешение w) пользователю, группе и владельцу.

   ```bash
   ls -la /data
   ```

   ![Разрешения для каталога с файлами Avro](media/tutorial-machine-learning-edge-06-custom-modules/avrofiles-directory-permissions.png)

#### <a name="add-directory-to-the-module"></a>Добавление каталога в модуль

Чтобы добавить каталог в контейнер модуля, мы изменим файлы Dockerfile для модуля avroFileWriter. С модулем связаны три файла Dockerfile: Dockerfile.amd64, Dockerfile.amd64.debug и Dockerfile.arm32v7. Эти файлы нужно синхронизировать, если нас интересует возможность отладки или развертывания на устройстве arm32. В этой статье мы рассматриваем только Dockerfile.amd64.

1. На компьютере разработки откройте файл **Dockerfile.amd64**.

2. Измените этот файл так, чтобы он выглядел следующим образом:

   ```dockerfile
   FROM ubuntu:xenial

   WORKDIR /app

   RUN apt-get update && apt-get install -y --no-install-recommends libcurl4-openssl-dev
   python3-pip libboost-python1.58-dev libpython3-dev && rm -rf /var/lib/apt/lists/*

   RUN pip3 install --upgrade pip
   COPY requirements.txt ./
   RUN pip install -r requirements.txt

   COPY . .

   RUN useradd -ms /bin/bash moduleuser
   RUN mkdir /avrofiles && chown moduleuser /avrofiles
   USER moduleuser

   CMD [ "python3", "-u", "./main.py" ]
   ```

   Команды `mkdir` и `chown`, выполняемые во время сборки Docker, отвечают за создание на верхнем уровне образа каталога /avrofiles и назначение пользователя moduleuser владельцем этого каталога. Важно выполнять эти команды только после добавления пользователя в образ (команда `useradd`), но до переключения контекста на moduleuser (команда USER moduleuser).

3. Внесите аналогичные изменения в файлы Dockerfile.amd64.debug и Dockerfile.arm32v7.

#### <a name="update-the-module-configuration"></a>Обновление конфигурации модуля

Последний этап создания привязки — внесение сведений о привязке в файлы deployment.template.json и deployment.debug.template.json.

1. Откройте файл deployment.template.json.

2. Измените определение модуля avroFileWriter, добавив в него параметр `Binds`, который сопоставит каталог контейнера /avrofiles с локальным каталогом на граничном устройстве. Теперь определение модуля должно соответствовать следующему:

   ```json
   "avroFileWriter": {
     "version": "1.0",
     "type": "docker",
     "status": "running",
     "restartPolicy": "always",
     "settings": {
       "image": "${MODULES.avroFileWriter}",
       "createOptions": {
         "HostConfig": {
           "Binds": [
             "/data/avrofiles:/avrofiles"
           ]
         }
       }
     }
   }
   ```

3. Внесите аналогичные изменения в файл deployment.debug.template.json.

### <a name="bind-mount-for-access-to-configyaml"></a>Подключение с привязкой для доступа к файлу config.yaml

Нам потребуется еще одна привязка для модуля записи. Эта привязка предоставит нашему модулю доступ на чтение строки подключения из файла /etc/iotedge/config.yaml на устройстве IoT Edge. Эта строка подключения нужна для создания IoTHubClient, чтобы мы могли вызывать метод upload\_blob\_async для передачи файлов в Центр Интернета вещей. Процесс добавления этой привязки аналогичен тому, что мы делали в предыдущем разделе.

#### <a name="update-directory-permission"></a>Обновление разрешений для каталога

1. Подключитесь к устройству IoT Edge по протоколу SSH.

   ```bash
   ssh -l <user>@IoTEdge-<extension>.<region>.cloudapp.azure.com
   ```

2. Добавьте разрешение на чтение файла config.yaml.

   ```bash
   sudo chmod +r /etc/iotedge/config.yaml
   ```

3. Убедитесь, что разрешения указаны верно.

   ```bash
   ls -la /etc/iotedge/
   ```

4. Разрешения для файла config.yaml должны иметь значение **-r--r--r--** .

#### <a name="add-directory-to-module"></a>Добавление каталога в модуль

1. На компьютере разработки откройте файл **Dockerfile.amd64**.

2. Добавьте в файл еще один набор команд `mkdir` и `chown`, чтобы текст файла выглядел так:

   ```dockerfile
   FROM ubuntu:xenial

   WORKDIR /app

   RUN apt-get update && apt-get install -y --no-install-recommends libcurl4-openssl-dev
   python3-pip libboost-python1.58-dev libpython3-dev && rm -rf /var/lib/apt/lists/\*

   RUN pip3 install --upgrade pip
   COPY requirements.txt ./
   RUN pip install -r requirements.txt

   COPY . .

   RUN useradd -ms /bin/bash moduleuser
   RUN mkdir /avrofiles && chown moduleuser /avrofiles
   RUN mkdir -p /app/iotconfig && chown moduleuser /app/iotconfig

   USER moduleuser

   CMD "python3", "-u", "./main.py"]
   ```

3. Внесите аналогичные изменения в файлы Dockerfile.amd64.debug и Dockerfile.arm32v7.

#### <a name="update-the-module-configuration"></a>Обновление конфигурации модуля

1. Откройте файл **deployment.template.json**.

2. Измените определение модуля avroFileWriter, добавив вторую строку в параметр `Binds`, который сопоставляет каталог контейнера (/app/iotconfig) с локальным каталогом на граничном устройстве (/etc/iotedge).

   ```json
   "avroFileWriter": {
     "version": "1.0",
     "type": "docker",
     "status": "running",
     "restartPolicy": "always",
     "settings": {
       "image": "${MODULES.avroFileWriter}",
       "createOptions": {
         "HostConfig": {
           "Binds": [
             "/data/avrofiles:/avrofiles",
             "/etc/iotedge:/app/iotconfig"
           ]
         }
       }
     }
   }
   ```

3. Внесите аналогичные изменения в файл deployment.debug.template.json.

## <a name="install-dependencies"></a>Установка зависимостей

Модуль записи зависит от двух библиотек Python: fastavro и PyYAML. Нам нужно установить эти зависимости на компьютере разработки и настроить процесс сборки Docker, чтобы он устанавливал их в образ модуля.

### <a name="pyyaml"></a>PyYAML

1. На компьютере разработки откройте файл **requirements.txt** и добавьте pyyaml.

   ```txt
   azure-iothub-device-client~=1.4.3
   pyyaml
   ```

2. Откройте файл **Dockerfile.amd64** и добавьте команду `pip install`, чтобы обновить setuptools.

   ```dockerfile
   FROM ubuntu:xenial

   WORKDIR /app

   RUN apt-get update && \
       apt-get install -y --no-install-recommends libcurl4-openssl-dev python3-pip libboost-python1.58-dev libpython3-dev && \
       rm -rf /var/lib/apt/lists/\*

   RUN pip3 install --upgrade pip
   RUN pip install -U pip setuptools
   COPY requirements.txt ./
   RUN pip install -r requirements.txt

   COPY . .

   RUN useradd -ms /bin/bash moduleuser
   RUN mkdir /avrofiles && chown moduleuser /avrofiles
   RUN mkdir -p /app/iotconfig && chown moduleuser /app/iotconfig
   USER moduleuser

   CMD [ "python3", "-u", "./main.py" ]
   ```

3. Внесите аналогичные изменения в файл Dockerfile.amd64.debug. <!--may not be necessary. Add 'if needed'?-->

4. Установите pyyaml локально, открыв окно терминала в Visual Studio Code и введя следующую команду:

   ```cmd
   pip install pyyaml
   ```

### <a name="fastavro"></a>Fastavro

1. В файл requirements.txt добавьте fastavro после pyyaml.

   ```txt
   azure-iothub-device-client~=1.4.3
   pyyaml
   fastavro
   ```

2. Установите fastavro на компьютере для разработки через окно терминала Visual Studio Code.

   ```cmd
   pip install fastavro
   ```

## <a name="reconfigure-iot-hub"></a>Изменение настройки Центра Интернета вещей

Добавив в нашу систему устройство IoT Edge и модули, мы изменили концепцию и цель отправки данных в Центр Интернета вещей. Теперь нам нужно перенастроить маршрутизацию в концентраторе с учетом этих изменений.

> [!NOTE]
> Настройки нужно изменить до того, как мы развернем модули, так как некоторые из параметров концентратора, в частности параметры отправки файлов, должны быть правильно настроены для работы модуля avroFileWriter.

### <a name="set-up-route-for-rul-messages-in-iot-hub"></a>Настройка маршрута в Центре Интернета вещей для сообщений о значениях RUL

Теперь маршрутизатор и классификатор полностью готовы, и мы будем получать регулярные сообщения, содержащие идентификатор устройства и прогнозное значение RUL для этого устройства. Эти данные будут передаваться в специальное расположение в хранилище, где мы будем отслеживать состояние устройств, создавать отчеты и создавать предупреждения, если потребуется. В то же время нам нужно, чтобы сведения, поступающие от конечных устройств, которые еще не подключены к устройству IoT Edge, как и раньше направлялись в текущее место хранения.

#### <a name="create-a-rul-message-route"></a>Создание маршрута для сообщений со значениями RUL

1. Найдите нужный Центр Интернета вещей на портале Azure.

2. Выберите **Маршрутизация сообщений** в меню навигации слева.

3. Выберите **Добавить**.

4. Присвойте этому маршруту имя **RulMessageRoute**.

5. Щелкните **Добавить** рядом с селектором **Конечная точка** и выберите вариант **Хранилище BLOB-объектов**.

6. В форме **Добавление конечной точки хранилища** введите имя **ruldata** для конечной точки.

7. Щелкните **Выберите контейнер**.

8. Выберите учетную запись хранилища, которую мы использовали при работе с этим руководством, — **iotedgeandml\<уникальный_суффикс\>** .

9. Выберите контейнер **ruldata** и щелкните **Выбрать**.

10. Щелкните **Создать**, чтобы создать конечную точку хранилища.

11. В поле **Запрос маршрутизации** введите следующий запрос:

    ```sql
    IS_DEFINED($body.PredictedRul) AND NOT IS_DEFINED($body.OperationalSetting1)
    ```

12. Разверните раздел **Тест**, а в нем — раздел **Текст сообщения**. Замените сообщение текстом из примера ожидаемых сообщений:

    ```json
    {
      "ConnectionDeviceId": "aaLeafDevice_1",
      "CorrelationId": "b27e97bb-06c5-4553-a064-e9ad59c0fdd3",
      "PredictedRul": 132.62721409309165,
      "CycleTime": 64.0
    }
    ```

13. Выберите **Проверить маршрут**. Если проверка пройдет успешно, вы увидите сообщение "Сообщение соответствовало запросу".

14. Выберите команду **Сохранить**.

#### <a name="update-turbofandevicetostorage-route"></a>Обновление маршрута turbofanDeviceToStorage

Нам не нужно хранить новые прогнозные данные в старом месте хранения, поэтому мы обновим маршрут.

1. На странице **Маршрутизация сообщений** для Центра Интернета вещей откройте вкладку **Маршруты**.

2. Выберите **turbofanDeviceDataToStorage** или другое имя, которое вы ранее присвоили маршруту на устройстве.

3. Заменит запрос маршрутизации следующим текстом:

   ```sql
   IS_DEFINED($body.OperationalSetting1)
   ```

4. Разверните раздел **Тест**, а в нем — раздел **Текст сообщения**. Замените сообщение текстом из примера ожидаемых сообщений:

   ```json
   {
     "Sensor13": 2387.96,
     "OperationalSetting1": -0.0008,
     "Sensor6": 21.61,
     "Sensor11": 47.2,
     "Sensor9": 9061.45,
     "Sensor4": 1397.86,
     "Sensor14": 8140.39,
     "Sensor18": 2388.0,
     "Sensor12": 522.87,
     "Sensor2": 642.42,
     "Sensor17": 391.0,
     "OperationalSetting3": 100.0,
     "Sensor1": 518.67,
     "OperationalSetting2": 0.0002,
     "Sensor20": 39.03,
     "DeviceId": 19.0,
     "Sensor5": 14.62,
     "PredictedRul": 212.00132402791962,
     "Sensor8": 2388.01,
     "Sensor16": 0.03,
     "CycleTime": 42.0,
     "Sensor21": 23.3188,
     "Sensor15": 8.3773,
     "Sensor3": 1580.09,
     "Sensor10": 1.3,
     "Sensor7": 554.57,
     "Sensor19": 100.0
   }
   ```

5. Выберите **Проверить маршрут**. Если проверка пройдет успешно, вы увидите сообщение "Сообщение соответствовало запросу".

6. Щелкните **Сохранить**.

### <a name="configure-file-upload"></a>Настройка передачи файлов

Настройте функцию передачи файлов для Центра Интернета вещей, чтобы модуль записи файла мог передавать файлы в хранилище.

1. На странице Центра Интернета вещей выберите **Отправка файла** в меню навигации слева.

2. Выберите **Контейнер службы хранилища Azure**.

3. Выберите свою учетную запись хранения в списке.

4. Выберите контейнер **uploadturbofanfiles** и щелкните **Выбрать**.

5. Щелкните **Сохранить**. Портал сообщит о завершении процесса сохранения.

> [!Note]
> При работе с этим руководством мы не включаем оповещения о загрузке, но вы можете найти сведения на эту тему в статье [Получение уведомления о передачи файла](../iot-hub/iot-hub-java-java-file-upload.md#receive-a-file-upload-notification).

## <a name="build-publish-and-deploy-modules"></a>Сборка, публикация и развертывание модулей

Теперь, когда мы внесли изменения в конфигурацию, можно переходить к сборке образов и их публикации в реестре контейнеров Azure. Процесс сборки использует файл deployment.template.json, чтобы определить требуемые модули. Параметры каждого модуля, в том числе используемая версия, указаны в файле module.json в папке модулей. Процесс сборки запускает сначала сборку Docker для всех файлов Dockerfile, которые соответствуют текущим настройкам конфигурации в файле module.json, чтобы получить готовый образ. Затем он публикует этот образ в реестре, который указан в файле module.json, присваивая ему метку с указанным там же номером версии. Наконец, он создает связанный с конфигурацией манифест развертывания (например, deployment.amd64.json), который мы затем развернем на устройстве IoT Edge. Устройство IoT Edge считывает информацию из манифеста развертывания и на основе этих инструкций скачивает модули, настраивает маршруты и задает требуемые свойства. Этот метод развертывания имеет два побочных эффекта, которые вам нужно учитывать.

* **Задержка развертывания**. Среда выполнения IoT Edge должна распознать изменения требуемых свойств до того, как начнет изменение параметров, поэтому после развертывания модулей может пройти некоторое время до того, как среда выполнения обработает их и приступит к обновлению устройства IoT Edge.

* **Важность версии модуля**. Если вы опубликуете в реестре контейнеров новую версию контейнера модуля, указав метку с прежней версией, среда выполнения не будет скачивать такую версию модуля. Она сравнивает значение версии в метках для локальной версии образа на требуемого образа в манифесте развертывания. Если эти версии совпадают, среда выполнения не выполняет никаких действий. Это означает, что версию модуля необходимо увеличивать каждый раз, когда вам нужно развернуть изменения. Чтобы увеличить номер версии, измените свойство **version** в свойстве **tag** в файле module.json для обновленного модуля. После этого скомпилируйте и опубликуйте модуль.

    ```json
    {
      "$schema-version": "0.0.1",
      "description": "",
      "image": {
        "repository": "<your registry>.azurecr.io/avrofilewriter",
        "tag": {
          "version": "0.0.1",
          "platforms": {
            "amd64": "./Dockerfile.amd64",
            "amd64.debug": "./Dockerfile.amd64.debug",
            "arm32v7": "./Dockerfile.arm32v7"
          }
        },
        "buildOptions": []
      },
      "language": "python"
    }
    ```

### <a name="build-and-publish"></a>Сборка и публикация

1. В Visual Studio Code откройте окно терминала Visual Studio Code на виртуальной машине разработки и войдите в реестр контейнеров.

   ```cmd
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

1. В Visual Studio Code щелкните правой кнопкой мыши файл deployment.template.json и выберите **Создать и отправить решение IoT Edge**.

### <a name="view-modules-in-the-registry"></a>Просмотр модулей в реестре

Когда сборка успешно завершится, мы сможем просмотреть опубликованные модули с помощью портала Azure.

1. На портале Azure и перейдите к рабочему пространству Службы машинного обучения и щелкните ссылку **Реестр**.

    ![Переход к реестру из рабочего пространства Службы машинного обучения](media/tutorial-machine-learning-edge-06-custom-modules/follow-registry-link.png)

2. В навигаторе на стороне реестра выберите узел **Репозитории**.

3. Обратите внимание, что оба созданных модуля (**avrofilewriter** и **turbofanrouter**) отображаются здесь в виде репозиториев.

4. Выберите **turbofanrouter** и вы увидите, что опубликован один образ с меткой 0.0.1-amd64.

   ![Просмотр первой версии turbofanrouter и ее метки](media/tutorial-machine-learning-edge-06-custom-modules/tagged-image-turbofanrouter-repo.png)

### <a name="deploy-modules-to-iot-edge-device"></a>Развертывание модулей на устройстве IoT Edge

Итак, мы скомпилировали и настроили модули в решении. Теперь нам нужно развернуть их на устройстве IoT Edge.

1. В Visual Studio Code откройте файл **deployment.amd64.json** из папки конфигурации.

2. Щелкните **Создать развертывание для одного устройства**.

3. Выберите нужное устройство IoT Edge **aaTurboFanEdgeDevice**.

4. В обозревателе Visual Studio Code обновите панель устройств Центра Интернета вещей. Вы увидите, что все три новых модуля уже развернуты, но пока не работают.

5. Обновите представление еще раз через несколько минут, и вы увидите, что модули выполняются.

   ![Просмотр запущенных модулей в Visual Studio Code](media/tutorial-machine-learning-edge-06-custom-modules/view-running-modules-list.png)

> [!NOTE]
> Для запуска модулей и перехода в состояние стабильной работы может потребоваться несколько минут. В этот период вы можете заметить, что модули запускаются и останавливаются, пока они пытаются подключиться к модулю Центра IoT Edge.

## <a name="diagnosing-failures"></a>Диагностика сбоев

В этом разделе мы расскажем вам про некоторые методы определения проблем с одним или несколькими модулями. Обычно сбой определяется по состоянию в Visual Studio Code.

### <a name="identify-failed-modules"></a>Выявление проблемных модулей

* **Visual Studio Code**. Изучите панель устройств в Центре Интернета вещей. Если большинство модулей выполняются нормально, но один из них остановлен, следует подробно исследовать именно этот модуль. Если все модули длительное время остаются в остановленном состоянии, это также может означать сбой.

* **Портал Azure** Перейдите на портале в Центр Интернета вещей и найдите страницу сведений об устройстве (см. сведения в разделе IoT Edge). Здесь вы можете обнаружить ошибку, полученную от модуля, или выяснить, что это устройство не передавало никаких данных в центр Интернета вещей.

### <a name="diagnosing-from-the-device"></a>Диагностические сведения от устройства.

Войдя на устройство IoT Edge, вы получите доступ к большому объему информации о состоянии модулей. В качестве основного механизма мы используем команды Docker, которые позволяют изучать контейнеры и образы на устройстве.

1. Получите список всех работающих контейнеров. Для каждого модуля должен отобразиться контейнер, имя которого соответствует этому модулю. Кроме того, эта команда возвращает точные характеристики образа контейнера, в том числе его версию, и вы можете сопоставить с их с ожидаемым состоянием. Эта же команда поможет вам получить список образов, если вы измените в ней слово image на слово container.

   ```bash
   sudo docker container ls
   ```

2. Получение журналов для контейнера. Эта команда выводит все данные, которые были записаны в StdErr и StdOut в указанном контейнере. Они используется для контейнеров, которые были запущены и затем остановились по неизвестной причине. Также она помогает понять, что происходит с контейнерами edgeAgent и edgeHub.

   ```bash
   sudo docker container logs <container name>
   ```

3. Изучите контейнер. Эта команда возвращает огромный объем сведений об образе. Эти данные можно фильтровать с учетом ваших потребностей. Например, если вам нужно проверить привязку для avroFileWriter, можно выполнить такую команду:

   ```bash
   sudo docker container inspect -f "{{ json .Mounts }}" avroFileWriter | python -m json.tool
   ```

4. Подключитесь к запущенному контейнеру. Эта команда может быть полезной, если вы хотите изучить контейнер во время его выполнения:

   ```bash
   sudo docker exec -it avroFileWriter bash
   ```

## <a name="next-steps"></a>Дополнительная информация

В этой статье описано, как создать в Visual Studio Code решение IoT Edge с тремя модулями: классификатор, маршрутизатор, а также средство записи и отправки файлов. Мы настроили маршруты, которые позволяют модулям обмениваться данными друг с другом на граничном устройстве, изменили конфигурацию этого устройства и обновили файлы Dockerfile для установки зависимостей и добавления подключений с привязкой в контейнеры модулей. Затем мы обновили конфигурацию Центра Интернета вещей, чтобы передавать сообщения с учетом их типа и обрабатывать отправленные файлы. После этого мы развернули модули на устройстве IoT Edge и убедились, что эти модули работают правильно.

См. подробнее в следующих статьях:

* [Сведения о развертывании модулей и настройке маршрутов в IoT Edge](module-composition.md)
* [Синтаксис запросов маршрутизации сообщений Центра Интернета вещей](../iot-hub/iot-hub-devguide-routing-query-syntax.md)
* [Маршрутизация сообщений с помощью Центра Интернета вещей (маршрутизация по тексту сообщений)](https://azure.microsoft.com/blog/iot-hub-message-routing-now-with-routing-on-message-body/)
* [Отправка файлов с помощью Центра Интернета вещей](../iot-hub/iot-hub-devguide-file-upload.md)
* [Передача файлов с устройства в облако с помощью Центра Интернета вещей](../iot-hub/iot-hub-python-python-file-upload.md)

Теперь вы можете перейти к следующей статье, в которой описано, как отправлять данные и проверять работу решения.

> [!div class="nextstepaction"]
> [Отправка данных через прозрачный шлюз](tutorial-machine-learning-edge-07-send-data-to-hub.md)
