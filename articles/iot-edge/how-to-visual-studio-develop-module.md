---
title: Разработка и отладка модулей в Visual Studio — Azure IoT Edge | Документация Майкрософт
description: Использование Visual Studio 2019 для разработки и отладки модулей для Azure IoT Edge
services: iot-edge
author: shizn
manager: philmea
ms.author: xshi
ms.date: 07/22/2019
ms.topic: article
ms.service: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 0ed7d65601465a197cb4d7f92f500e1bf29ad8c2
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68839659"
---
# <a name="use-visual-studio-2019-to-develop-and-debug-modules-for-azure-iot-edge"></a>Использование Visual Studio 2019 для разработки и отладки модулей для Azure IoT Edge

Вы можете превратить бизнес-логику в модули для Azure IoT Edge. В этой статье показано, как использовать Visual Studio 2019 в качестве основного средства для разработки и отладки модулей.

Средства Azure IoT Edge для Visual Studio обеспечивают следующие преимущества:

- создание, изменение, сборку, запуск и отладку решений и модулей Azure IoT Edge на локальном компьютере для разработки;
- развертывание решения Azure IoT Edge на устройстве Azure IoT Edge через Центр Интернета вещей;
- Код для модулей Интернета вещей Azure на языке C# C или при наличии всех преимуществ разработки Visual Studio.
- управление устройствами и модулями Azure IoT Edge с помощью пользовательского интерфейса.

В этой статье показано, как использовать средства Azure IoT Edge для Visual Studio 2019 для разработки модулей IoT Edge. Вы также узнаете, как развернуть свой проект на устройство Azure IoT Edge. В настоящее время Visual Studio 2019 предоставляет поддержку модулей, написанных на C#языке C и. Поддерживаемыми архитектурами устройств являются Windows x64 и Linux x64 или ARM32. Дополнительные сведения о поддерживаемых операционных системах, языках и архитектурах см. в разделе [Поддержка языков и архитектур](module-development.md#language-and-architecture-support).
  
## <a name="prerequisites"></a>предварительные требования

В этой статье предполагается, что для разработки вы используете компьютер или виртуальную машину под управлением Windows. Устройство IoT Edge может быть другим физическим устройством.

Поскольку в этой статье в качестве основного средства разработки используется Visual Studio 2019, установите Visual Studio. Убедитесь, что вы включили разработку и настольные приложения **Azure** **с C++**  рабочими нагрузками в установке Visual Studio 2019. Вы можете [изменить Visual Studio 2019](https://docs.microsoft.com/visualstudio/install/modify-visual-studio?view=vs-2019) , добавив необходимые рабочие нагрузки.

После того как Visual Studio 2019 будет готова, вам понадобятся следующие средства и компоненты:

- Скачайте и установите [средства Azure IOT Edge](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) из Visual Studio Marketplace, чтобы создать IOT Edge проект в visual Studio 2019.

> [!TIP]
> Если вы используете Visual Studio 2017, плреасе Скачайте и установите [инструменты Azure IOT Edge](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) для VS 2017 из Visual Studio Marketplace.

- Скачайте и установите [Docker Community Edition](https://docs.docker.com/install/) на компьютере разработки для сборки и запуска образов модуля. Вам необходимо настроить Docker CE для работы в режиме контейнера в Linux или Windows.

- Настройте локальную среду разработки для отладки, запуска и тестирования решения IoT Edge, установив [средство разработки Azure IoT EdgeHub](https://pypi.org/project/iotedgehubdev/). Установите [Python (2.7 или 3.6) и Pip](https://www.python.org/), а затем установите пакет **iotedgehubdev**, выполнив следующую команду в своем терминале. Используйте версию средства разработки Azure IoT EdgeHub выше версии 0.3.0.

   ```cmd
   pip install --upgrade iotedgehubdev
   ```

- Клонировать репозиторий и установить Диспетчер библиотеки Vcpkg, а затем установить **пакет Azure-IOT-SDK-c** для Windows.

  ```cmd
  git clone https://github.com/Microsoft/vcpkg
  cd vcpkg
  bootstrap-vcpkg.bat
  ```

  ```cmd
  vcpkg.exe install azure-iot-sdk-c:x64-windows
  vcpkg.exe --triplet x64-windows integrate install
  ```

- [Реестр контейнеров Azure](https://docs.microsoft.com/azure/container-registry/) или [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags).

  > [!TIP]
  > Вместо облачного реестра можно использовать локальный реестр Docker для создания прототипов и тестирования.

- Для тестирования модуля на устройстве потребуется действующий центр Интернета вещей по крайней мере с одним устройством IoT Edge. Чтобы использовать компьютер в качестве устройства IoT Edge, выполните инструкции в кратком руководстве для [Linux](quickstart-linux.md) или [Windows](quickstart.md). Если вы запустили управляющую программу IoT Edge на компьютере разработки, может потребоваться остановить EdgeHub и EdgeAgent, прежде чем запустить разработку в Visual Studio.

### <a name="check-your-tools-version"></a>Проверка версии инструментов

1. Откройте меню **Средства** и выберите пункт **Расширения и обновления**. Разверните **Установленные > Средства** и найдите **средства Azure IoT Edge** и **Cloud Explorer для Visual Studio**.

1. Запишите номер установленной версии. Вы можете сравнить эту версию с последней версией в Visual Studio Marketplace ([Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS2019), [Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools)).

1. Если вы используете более раннюю версию, чем та, которая доступна в Visual Studio Marketplace, обновите инструменты в Visual Studio, как показано в следующем разделе.

### <a name="update-your-tools"></a>Обновление инструментов

1. В диалоговом окне **Расширения и обновления** выберите **Обновления > Visual Studio Marketplace**, **Средства Azure IoT Edge** или **Cloud Explorer для Visual Studio**, а затем щелкните **Обновить**.

1. Скачав обновления для инструментов, закройте Visual Studio, чтобы активировать обновления с помощью установщика VSIX.

1. В установщике нажмите кнопку **ОК**, чтобы начать запуск, и выберите **Изменить** для обновления инструментов.

1. Когда обновление завершится, нажмите кнопку **Закрыть** и перезапустите Visual Studio.

### <a name="create-an-azure-iot-edge-project"></a>Создание проекта Azure IoT Edge

Шаблон проекта Azure IoT Edge в Visual Studio создает проект, который можно развернуть на устройства Azure IoT Edge в Центре Интернета вещей. Сначала создается Azure IoT Edge решение, а затем создается первый модуль в этом решении. Каждое решение IoT Edge может содержать несколько модулей.

> [!TIP]
> Структура проекта IoT Edge, созданного в Visual Studio, отличается от структуры проекта Visual Studio Code.

1. В диалоговом окне Новый проект Visual Studio найдите и выберите **Azure IOT Edge** проект, а затем нажмите кнопку **Далее**. В окне конфигурации проекта введите имя проекта и укажите расположение, а затем выберите **создать**. Имя проекта по умолчанию — **AzureIoTEdgeApp1**.

   ![Создание проекта](./media/how-to-visual-studio-develop-csharp-module/create-new.png)

1. В **C#** окне **Добавление IOT Edge приложения и модуля** выберите модуль или **модуль C** , а затем укажите имя модуля и репозиторий образа модуля. Visual Studio автоматически заполняет имя модуля значением **localhost:5000/<имя вашего модуля\>** . Замените его собственными данными реестра. Если для тестирования вы используете локальный реестр Docker, вполне подойдет значение **localhost**. Если используется Реестр контейнеров Azure, укажите сервер входа, заданный в параметрах реестра. Сервер входа выглядит как   **_\<имя\>реестра_. azurecr.IO**. Замените только часть **localhost: 5000** строки, чтобы окончательный результат  **\<был похож на *имя*\>реестра. azurecr.IO/ _\<имя\>модуля_** . Имя модуля по умолчанию — **IoTEdgeModule1**.

   ![Добавление приложения и модуля](./media/how-to-visual-studio-develop-csharp-module/add-application-and-module.png)

1. Нажмите кнопку **ОК** , чтобы создать Azure IOT EDGE решение с модулем, использующим C# или C.

Теперь у вас есть проект **AzureIoTEdgeApp1. Linux. AMD64** или проект **AzureIoTEdgeApp1. Windows. AMD64** , а также проект **IoTEdgeModule1** в решении. Каждый проект **AzureIoTEdgeApp1** имеет `deployment.template.json` файл, который определяет модули, которые необходимо создать и развернуть для решения IOT EDGE, а также определяет маршруты между модулями. Решение по умолчанию содержит модуль **симулатедтемпературесенсор** и модуль **IoTEdgeModule1** . Модуль **симулатедтемпературесенсор** создает смоделированные данные в модуле **IoTEdgeModule1** , а код по умолчанию в модуле **IoTEdgeModule1** напрямую передает сообщения в центр Интернета вещей Azure.

Проект **IoTEdgeModule1** является консольным приложением .net Core 2,1, если это C# модуль. Он содержит обязательные файлы Docker, необходимые для вашего устройства IoT Edge, работающего с контейнером Windows или Linux. В `module.json` файле описаны метаданные модуля. Фактический код модуля, который использует пакет SDK для устройств Azure IOT в качестве зависимости, находится в `Program.cs` файле `main.c` или.

## <a name="develop-your-module"></a>Разработка модуля

Код модуля по умолчанию, поставляемый вместе с решением, находится по адресу C# **IoTEdgeModule1** > **Program.CS** (для) или **Main. c** (c). Модуль и `deployment.template.json` файл настроены таким образом, чтобы вы могли создать решение, отправить его в реестр контейнеров и развернуть на устройстве, чтобы начать тестирование, не затрагивая код. Модуль создается для получения входных данных из источника (в данном случае это модуль **симулатедтемпературесенсор** , моделирующий данные) и передает его в центр Интернета вещей Azure.

Когда вы будете готовы к настройке шаблона модуля с помощью собственного кода, используйте [пакеты SDK для центра Интернета вещей Azure](../iot-hub/iot-hub-devguide-sdks.md) , чтобы создать модули, которые удовлетворяют ключевым потребностям для решений Интернета вещей, таких как безопасность, Управление устройствами и надежность.

## <a name="initialize-iotedgehubdev-with-iot-edge-device-connection-string"></a>Инициализация iotegehubdev с помощью строки подключения устройства IoT Edge

1. Скопируйте строку подключения любого устройства IoT Edge из **основной строки подключения** в Visual Studio Cloud Explorer. Не копируйте строку подключения другого устройства (не Edge), так как значок устройства IoT Edge отличается от значка другого устройства.

   ![Копирование строки подключения устройства IoT Edge](./media/how-to-visual-studio-develop-csharp-module/copy-edge-conn-string.png)

1. Последовательно выберите **пункты Сервис** > **Azure IOT Edge инструменты** > **Настройка IOT Edge симулятор**, вставьте строку подключения и нажмите кнопку **ОК**.

   ![Открытое окно установки строки подключения к Edge](./media/how-to-visual-studio-develop-csharp-module/set-edge-conn-string.png)

1. Введите строку подключения из первого шага, а затем нажмите кнопку **ОК**.

> [!NOTE]
> Выполните следующие действия на компьютере разработки только раз, так как результаты автоматически применяются ко всем последующим решениям Azure IoT Edge. Если вам понадобится изменить строку подключения, тогда эту процедуру нужно выполнить еще раз.

## <a name="build-and-debug-single-module"></a>Создание и отладка одного модуля

Как правило, нужно выполнить тестирование и отладку каждого модуля, прежде чем запустить его в рамках целого решения с несколькими модулями.

1. Щелкните правой кнопкой мыши **IoTEdgeModule1** и в контекстном меню выберите **Назначить запускаемым проектом**.

   ![Задание запускаемого проекта](./media/how-to-visual-studio-develop-csharp-module/module-start-up-project.png)

1. Нажмите клавишу **F5** или кнопку ниже, чтобы запустить модуль. Это может занять 10&ndash;20 секунд в первый раз.

   ![Запуск модуля](./media/how-to-visual-studio-develop-csharp-module/run-module.png)

1. В случае успешной инициализации модуля вы должны увидеть запущенное консольное приложение .NET Core.

   ![Запущенный модуль](./media/how-to-visual-studio-develop-csharp-module/single-module-run.png)

1. При разработке в C#установите точку останова в `PipeMessage()` функции в **Program.CS**; при использовании C Установите точку останова в `InputQueue1Callback()` функции в **Main. C**. Затем можно протестировать его, отправив сообщение, выполнив следующую команду в оболочке **Git Bash** или **WSL Bash** . (Команду `curl` нельзя запустить в PowerShell или командной строке.)

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

1. Добавьте в решение второй модуль, щелкнув правой кнопкой мыши **AzureIoTEdgeApp1** и выбрав **Добавить** > **новый модуль IOT Edge**. Стандартное имя второго модуля — **IoTEdgeModule2**, и он будет выступать в качестве другого модуля канала.

1. Откройте файл`deployment.template.json`, и вы увидите модуль **IoTEdgeModule2**, добавленный в раздел **модулей**. Замените раздел **routes** следующим кодом: Если вы настроили имена модулей, обновите их таким образом, чтобы они совпадали.

    ```json
        "routes": {
          "IoTEdgeModule1ToIoTHub": "FROM /messages/modules/IoTEdgeModule1/outputs/* INTO $upstream",
          "sensorToIoTEdgeModule1": "FROM /messages/modules/SimulatedTemperatureSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/IoTEdgeModule1/inputs/input1\")",
          "IoTEdgeModule2ToIoTHub": "FROM /messages/modules/IoTEdgeModule2/outputs/* INTO $upstream",
          "sensorToIoTEdgeModule2": "FROM /messages/modules/SimulatedTemperatureSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/IoTEdgeModule2/inputs/input1\")"
        },
    ```

1. Щелкните правой кнопкой мыши **AzureIoTEdgeApp1** и в контекстном меню выберите **Назначить запускаемым проектом**.

1. Создайте точки останова и нажмите клавишу **F5**, чтобы одновременно запускать и отлаживать несколько модулей. Вы увидите несколько окон консольного приложения .NET Core, каждое из которых представляет отдельный модуль.

   ![Отладка нескольких модулей](./media/how-to-visual-studio-develop-csharp-module/debug-multiple-modules.png)

1. Нажмите клавиши **CTRL+F5** или выберите кнопку "Остановить", чтобы остановить отладку.

## <a name="build-and-push-images"></a>Создание и отправка образов

1. Задайте **AzureIoTEdgeApp1** как запускаемый проект. Выберите конфигурацию **Отладка** или **Выпуск**, которая будет применяться для образов модуля.

    > [!NOTE]
    > В случае выбора конфигурации **Отладка** Visual Studio будет использовать `Dockerfile.(amd64|windows-amd64).debug` для сборки образов Docker. Это включает отладчик командной строки .Net Core VSDBG в образе контейнера при его создании. Для модулей IoT Edge, готовых для рабочей среды, мы советуем выбрать конфигурацию **Выпуск**, которая использует `Dockerfile.(amd64|windows-amd64)` без VSDBG.

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

1. Откройте **Cloud Explorer**, щелкнув **Просмотр** > **Cloud Explorer**. Убедитесь, что вы вошли в Visual Studio 2019.

1. В **Cloud Explorer** разверните подписку, найдите Центр Интернета вещей Azure и устройство Azure IoT Edge, которое нужно развернуть.

1. Щелкните правой кнопкой мыши устройство IoT Edge, чтобы создать для него развертывание. Выберите файл манифеста развертывания: `$AzureIoTEdgeAppSolutionDir\config\deployment.(amd64|amd64.debug|windows-amd64).json`.

   > [!NOTE]
   > Не выбирайте `$AzureIoTEdgeAppSolutionDir\config\deployment_for_local_debug.json`.

1. Нажмите кнопку обновить, чтобы просмотреть новые модули, работающие с модулем **симулатедтемпературесенсор** , а также **$edgeAgent** и **$edgeHub**.

## <a name="view-generated-data"></a>Просмотр сформированных данных

1. Чтобы отслеживать сообщение D2C для конкретного устройства, выберите его в списке, а затем щелкните **начать мониторинг встроенной конечной точки события** в окне **действий** .

1. Чтобы отключить мониторинг данных, выберите устройство в списке, а затем в окне **действия** выберите пункт **закончить мониторинг встроенной конечной точки события** .

## <a name="next-steps"></a>Следующие шаги

Сведения о разработке пользовательских модулей для устройств IoT Edge см. в статье [Понимание и использование пакетов SDK для Центра Интернета вещей Azure](../iot-hub/iot-hub-devguide-sdks.md).
