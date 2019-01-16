---
title: Разработка и отладка модулей C# в Visual Studio 2017 — Azure IoT Edge | Документация Майкрософт
description: Сведения об использовании Visual Studio 2017 для разработки и отладки модулей C# для Azure IoT Edge
services: iot-edge
author: shizn
manager: philmea
ms.author: xshi
ms.date: 01/04/2019
ms.topic: article
ms.service: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 9a6c892a71c452a0c6c0dcd43509e345280a810e
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54054929"
---
# <a name="use-visual-studio-2017-to-develop-and-debug-c-modules-for-azure-iot-edge-preview"></a>Сведения об использовании Visual Studio 2017 для разработки и отладки модулей C# для Azure IoT Edge (предварительная версия)

Вы можете превратить бизнес-логику в модули для Azure IoT Edge. В этой статье описывается, как использовать Visual Studio 2017 в качестве основного средства разработки и отладки модулей C#.

Средства Azure IoT Edge для Visual Studio обеспечивают следующие преимущества:

- создание, изменение, сборку, запуск и отладку решений и модулей Azure IoT Edge на локальном компьютере для разработки;
- развертывание решения Azure IoT Edge на устройстве Azure IoT Edge через Центр Интернета вещей;
- программирование модулей Интернета вещей Azure на C#, сохраняя при этом все преимущества разработки в Visual Studio;
- управление устройствами и модулями Azure IoT Edge с помощью пользовательского интерфейса.

В этой статье объясняется, как использовать средства Azure IoT Edge для Visual Studio 2017 для разработки модулей IoT Edge на C#. Вы также узнаете, как развернуть свой проект на устройство Azure IoT Edge.

## <a name="prerequisites"></a>Предварительные требования

В этой статье предполагается, что для разработки вы используете компьютер или виртуальную машину под управлением Windows. Устройство IoT Edge может быть другим физическим устройством.

Так как в этой статье в качестве основного средства разработки используется Visual Studio 2017, установите Visual Studio. Включите **рабочую нагрузку разработки Azure** в своей установке Visual Studio 2017. Вы можете [изменить Visual Studio 2017](https://docs.microsoft.com/visualstudio/install/modify-visual-studio?view=vs-2017) и добавить рабочую нагрузку разработки Azure.

После того как Visual Studio 2017 будет настроена, вам также понадобятся следующие средства и компоненты.

- Скачайте и установите [расширение Azure IoT Edge (предварительная версия)](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) из Visual Studio Marketplace для создания проекта IoT Edge в Visual Studio 2017.

- Скачайте и установите [Docker Community Edition](https://docs.docker.com/install/) на компьютере разработки для сборки и запуска образов модуля. Вам необходимо настроить Docker CE для работы в режиме контейнера в Linux или Windows.

- Настройте локальную среду разработки для отладки, запуска и тестирования решения IoT Edge, установив [средство разработки Azure IoT EdgeHub](https://pypi.org/project/iotedgehubdev/). Установите [Python (2.7 или 3.6) и Pip](https://www.python.org/), а затем установите пакет **iotedgehubdev**, выполнив следующую команду в своем терминале. Используйте версию средства разработки Azure IoT EdgeHub выше версии 0.3.0.

   ```cmd
   pip install --upgrade iotedgehubdev
   ```

- [Реестр контейнеров Azure](https://docs.microsoft.com/azure/container-registry/) или [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags).

  > [!TIP]
  > Вместо облачного реестра можно использовать локальный реестр Docker для создания прототипов и тестирования.

- Для тестирования модуля на устройстве потребуется действующий центр Интернета вещей по крайней мере с одним устройством IoT Edge. Чтобы использовать компьютер в качестве устройства IoT Edge, выполните инструкции в кратком руководстве для [Linux](quickstart-linux.md) или [Windows](quickstart.md). Если вы запустили управляющую программу IoT Edge на компьютере разработки, может потребоваться остановить EdgeHub и EdgeAgent, прежде чем запустить разработку в Visual Studio.

### <a name="check-your-tools-version"></a>Проверка версии инструментов

1. Откройте меню **Средства** и выберите пункт **Расширения и обновления**. Разверните **Установленные > Средства** и найдите **средства Azure IoT Edge** и **Cloud Explorer для Visual Studio**.

1. Запишите номер установленной версии. Вы можете сравнить эту версию с последней версией в Visual Studio Marketplace ([Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS), [Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge)).

1. Если вы используете более раннюю версию, чем та, которая доступна в Visual Studio Marketplace, обновите инструменты в Visual Studio, как показано в следующем разделе.

### <a name="update-your-tools"></a>Обновление инструментов

1. В диалоговом окне **Расширения и обновления** выберите **Обновления > Visual Studio Marketplace**, **Средства Azure IoT Edge** или **Cloud Explorer для Visual Studio**, а затем щелкните **Обновить**.

1. Скачав обновления для инструментов, закройте Visual Studio, чтобы активировать обновления с помощью установщика VSIX.

1. В установщике нажмите кнопку **ОК**, чтобы начать запуск, и выберите **Изменить** для обновления инструментов.

1. Когда обновление завершится, нажмите кнопку **Закрыть** и перезапустите Visual Studio.

### <a name="create-an-azure-iot-edge-project"></a>Создание проекта Azure IoT Edge

Шаблон проекта Azure IoT Edge в Visual Studio создает проект, который можно развернуть на устройства Azure IoT Edge в Центре Интернета вещей. Сначала нужно создать решение Azure IoT Edge, а затем сформировать в нем первый модуль C#. Каждое решение IoT Edge может содержать несколько модулей.

1. В Visual Studio в меню **Файл** выберите **Создать** > **Проект**.

1. В диалоговом окне **Новый проект** выберите **Установленные**, разверните узел **Visual C# > Облако**, выберите **Azure IoT Edge**, укажите имя проекта и расположение, а затем нажмите кнопку **ОК**. Имя проекта по умолчанию — **AzureIoTEdgeApp1**.

   ![Новый проект](./media/how-to-visual-studio-develop-csharp-module/create-new.jpg)

1. В окне **конфигурации модуля IoT Edge** выберите тип **модуля C#** и укажите имя модуля и репозиторий образа модуля. Visual Studio автоматически заполняет имя модуля значением **localhost:5000/<имя вашего модуля\>**. Замените его собственными данными реестра. Если для тестирования вы используете локальный реестр Docker, вполне подойдет значение **localhost**. Если используется Реестр контейнеров Azure, укажите сервер входа, заданный в параметрах реестра. Значение для сервера входа выглядит так: ***\<имя реестра\>*.azurecr.io**. Замените только часть **localhost:5000** строки, чтобы конечный результат выглядел следующим образом: **\<* имя реестра*\>.azurecr.io/*\<имя вашего модуля\>***. Имя модуля по умолчанию — **IoTEdgeModule1**.

1. Нажмите кнопку **ОК**, чтобы создать проект Azure IoT Edge с использованием модуля C#.

Теперь в решении есть проекты **AzureIoTEdgeApp1** и **IoTEdgeModule1**. Проект **AzureIoTEdgeApp1** включает файл **deployment.template.json**. Этот файл определяет модули, которые вы хотите создать и развернуть для решения IoT Edge, а также маршруты между ними. Решение по умолчанию включает модули **tempSensor** и **IoTEdgeModule1**. Модуль **tempSensor** генерирует смоделированные данные в модуль **IoTEdgeModule1**, а код по умолчанию в модуле **IoTEdgeModule1** напрямую передает полученные сообщения в Центр Интернета вещей Azure.

Проект **IoTEdgeModule1** представляет собой консольное приложение .NET Core 2.1. Он содержит обязательные файлы Docker, необходимые для вашего устройства IoT Edge, работающего с контейнером Windows или Linux. В файле **module.json** описываются метаданные модуля. Файл **program.cs** — это фактический код модуля, который использует пакет SDK для устройств Azure IoT в качестве зависимости.

## <a name="develop-your-module"></a>Разработка модуля

Код модуля C# по умолчанию, который поставляется вместе с решением, находится здесь: **IoTEdgeModule1** > **Program.cs**. Модуль и файл deployment.template.json настраиваются так, чтобы можно было собрать решение, передать его в реестр контейнеров и развернуть на устройстве, чтобы начать тестирование, не меняя код. Модуль просто принимает входные данные из источника (в данном случае из модуля **tempSensor**, который имитирует данные) и передает их в Центр Интернета вещей Azure.

Когда вы будете готовы настроить шаблон C# с добавлением собственного кода, используйте [пакеты SDK для Центра Интернета вещей Azure](../iot-hub/iot-hub-devguide-sdks.md), чтобы создать модули, которые удовлетворяют ключевые потребности решений Интернета вещей, такие как безопасность, управление устройствами и надежность.

## <a name="initialize-iotedgehubdev-with-iot-edge-device-connection-string"></a>Инициализация iotegehubdev с помощью строки подключения устройства IoT Edge

1. Скопируйте строку подключения любого устройства IoT Edge из **основной строки подключения** в Visual Studio Cloud Explorer. Не копируйте строку подключения другого устройства (не Edge), так как значок устройства IoT Edge отличается от значка другого устройства.

   ![Копирование строки подключения устройства IoT Edge](./media/how-to-visual-studio-develop-csharp-module/copy-edge-conn-string.png)

1. Щелкните правой кнопкой мыши проект **AzureIoTEdgeApp1**, а затем выберите **Set Edge Device Connection String** (Установить строку подключения устройства Edge), чтобы открыть окно настройки Azure IoT Edge.

   ![Открытое окно установки строки подключения к Edge](./media/how-to-visual-studio-develop-csharp-module/set-edge-conn-string.png)

1. Введите строку подключения из первого шага, а затем нажмите кнопку **ОК**.

> [!NOTE]
> Выполните следующие действия на компьютере разработки только раз, так как результаты автоматически применяются ко всем последующим решениям Azure IoT Edge. Если вам понадобится изменить строку подключения, тогда эту процедуру нужно выполнить еще раз.

## <a name="build-and-debug-single-c-module"></a>Создание и отладка единого модуля C#

Как правило, нужно выполнить тестирование и отладку каждого модуля, прежде чем запустить его в рамках целого решения с несколькими модулями.

1. Щелкните правой кнопкой мыши **IoTEdgeModule1** и в контекстном меню выберите **Назначить запускаемым проектом**.

   ![Задание запускаемого проекта](./media/how-to-visual-studio-develop-csharp-module/module-start-up-project.png)

1. Нажмите клавишу **F5** или кнопку ниже, чтобы запустить модуль. Это может занять 10&ndash;20 секунд в первый раз.

   ![Запуск модуля](./media/how-to-visual-studio-develop-csharp-module/run-module.png)

1. В случае успешной инициализации модуля вы должны увидеть запущенное консольное приложение .NET Core.

   ![Запущенный модуль](./media/how-to-visual-studio-develop-csharp-module/single-module-run.png)

1. Задайте точку останова в функции `PipeMessage()` в файле **Program.cs**, а затем протестируйте ее, отправив сообщение путем выполнения следующей команды в оболочке **Git Bash** или **WSL Bash**. (Команду `curl` нельзя запустить в PowerShell или командной строке.)

    ```bash
    curl --header "Content-Type: application/json" --request POST --data '{"inputName": "input1","data":"hello world"}' http://localhost:53000/api/v1/messages
    ```

   ![Отладка одного модуля](./media/how-to-visual-studio-develop-csharp-module/debug-single-module.png)

    Точка останова должна быть активирована. Вы можете просмотреть переменные в окне **Локальные элементы** Visual Studio.

   > [!TIP]
   > Вместо `curl` для отправки сообщений можно использовать [PostMan](https://www.getpostman.com/) или другие средства API.

1. Нажмите клавиши **CTRL + F5** или кнопку "Остановить", чтобы остановить отладку.

## <a name="build-and-debug-iot-edge-solution-with-multiple-modules"></a>Создание и отладка решения IoT Edge с несколькими модулями

После разработки единого модуля далее вы можете запустить и отладить все решение с несколькими модулями.

1. Добавьте второй модуль C# в решение, щелкнув правой кнопкой мыши **AzureIoTEdgeApp1** и выбрав **Добавить** > **New IoT Edge Module** (Новый модуль IoT Edge). Стандартное имя второго модуля — **IoTEdgeModule2**, и он будет выступать в качестве другого модуля канала.

1. Откройте файл`deployment.template.json`, и вы увидите модуль **IoTEdgeModule2**, добавленный в раздел **модулей**. Замените раздел **routes** следующим кодом: Если вы настроили имена модулей, обновите их таким образом, чтобы они совпадали.

    ```json
        "routes": {
          "IoTEdgeModule1ToIoTHub": "FROM /messages/modules/IoTEdgeModule1/outputs/* INTO $upstream",
          "sensorToIoTEdgeModule1": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/IoTEdgeModule1/inputs/input1\")",
          "IoTEdgeModule2ToIoTHub": "FROM /messages/modules/IoTEdgeModule2/outputs/* INTO $upstream",
          "sensorToIoTEdgeModule2": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/IoTEdgeModule2/inputs/input1\")"
        },
    ```

1. Щелкните правой кнопкой мыши **AzureIoTEdgeApp1** и в контекстном меню выберите **Назначить запускаемым проектом**.

1. Создайте точки останова и нажмите клавишу **F5**, чтобы одновременно запускать и отлаживать несколько модулей. Вы должны увидеть несколько окон консольного приложения .NET Core, где каждое окно являет собой разные модули C#.

   ![Отладка нескольких модулей](./media/how-to-visual-studio-develop-csharp-module/debug-multiple-modules.png)

1. Нажмите клавиши **CTRL+F5** или выберите кнопку "Остановить", чтобы остановить отладку.

## <a name="build-and-push-images"></a>Создание и отправка образов

1. Задайте **AzureIoTEdgeApp1** как запускаемый проект. Выберите конфигурацию **Отладка** или **Выпуск**, которая будет применяться для образов модуля.

    > [!NOTE]
    > В случае выбора конфигурации **Отладка** Visual Studio будет использовать `Dockerfile.debug` для сборки образов Docker. Это включает отладчик командной строки .Net Core VSDBG в образе контейнера при его создании. Для модулей IoT Edge, готовых для рабочей среды, мы советуем выбрать конфигурацию **Выпуск**, которая использует `Dockerfile` без VSDBG.

1. Если вы используете частный реестр, например Реестр контейнеров Azure, используйте следующую команду Docker, чтобы войти в него. Если вы используете локальный реестр, вы можете выполнить команду [запуска локального реестра](https://docs.docker.com/registry/deploying/#run-a-local-registry).

    ```cmd
    docker login -u <ACR username> -p <ACR password> <ACR login server>
    ```

1. Если вы используете частный реестр, например Реестр контейнеров Azure, необходимо добавить данные для входа в реестр к параметрам среды выполнения, которые находятся в файле `deployment.template.json`. Замените значения заполнителей фактическим именем администратора ACR, паролем и именем реестра.

    ```json
          "settings": {
            "minDockerVersion": "v1.25",
            "loggingOptions": "",
            "registryCredentials": {
              "registry1": {
                "username": "<username>",
                "password": "<password>",
                "address": "<registry name>.azurecr.io"
              }
            }
          }
    ```

1. Щелкните правой кнопкой мыши **AzureIoTEdgeApp1** и выберите пункт **Build and Push Edge Solution** (Создать и отправить решение Edge), чтобы создать и отправить образ Docker для каждого модуля.

   ![Создание и отправка образов](./media/how-to-visual-studio-develop-csharp-module/build-and-push.png)

## <a name="deploy-the-solution"></a>Развертывание решения

При использовании руководства по настройке устройства IoT Edge модуль был развернут с помощью портала Azure. Вы также можете развернуть модули с помощью Cloud Explorer для Visual Studio. У вас уже есть манифест развертывания, подготовленный для вашего сценария, —это файл `deployment.json`. Вам остается только выбрать устройство для получения развертывания.

1. Откройте **Cloud Explorer**, щелкнув **Просмотр** > **Cloud Explorer**. Войдите в Visual Studio 2017.

1. В **Cloud Explorer** разверните подписку, найдите Центр Интернета вещей Azure и устройство Azure IoT Edge, которое нужно развернуть.

1. Щелкните правой кнопкой мыши устройство IoT Edge, чтобы создать для него развертывание. Выберите файл манифеста развертывания: `$AzureIoTEdgeAppSolutionDir\config\deployment.(amd64|amd64.debug|windows-amd64).json`.

   > [!NOTE]
   > Не выбирайте `$AzureIoTEdgeAppSolutionDir\config\deployment_for_local_debug.json`.

1. Нажмите кнопку "Обновить", чтобы увидеть новые модули, работающие вместе с модулем **TempSensor**, а также **$edgeAgent** и **$edgeHub**.

## <a name="view-generated-data"></a>Просмотр сформированных данных

1. Для отслеживания сообщений D2C для конкретного устройства щелкните его кнопкой мыши в списке и выберите **Start Monitoring D2C Messages** (Начать мониторинг сообщений D2C) в окне **Действие**.

1. Чтобы завершить отслеживание данных, щелкните устройство в списке и выберите **Stop Monitoring D2C Messages** (Завершить мониторинг сообщений D2C) в окне **Действие**.

## <a name="next-steps"></a>Дополнительная информация

Сведения о разработке пользовательских модулей для устройств IoT Edge см. в статье [Понимание и использование пакетов SDK для Центра Интернета вещей Azure](../iot-hub/iot-hub-devguide-sdks.md).
