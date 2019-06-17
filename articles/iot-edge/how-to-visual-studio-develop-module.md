---
title: Разработка и отладка модулей в Visual Studio — Edge Интернета вещей Azure | Документация Майкрософт
description: Использовать 2019 г. Visual Studio для разработки и отладки модулей для Azure IoT Edge
services: iot-edge
author: shizn
manager: philmea
ms.author: xshi
ms.date: 05/27/2019
ms.topic: article
ms.service: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 4014827366afc492d73757a0ac5e1acb64262c51
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66474779"
---
# <a name="use-visual-studio-2019-to-develop-and-debug-modules-for-azure-iot-edge-preview"></a>Использовать 2019 г. Visual Studio для разработки и отладки модулей для Azure IoT Edge (Предварительная версия)

Вы можете превратить бизнес-логику в модули для Azure IoT Edge. В этой статье показано, как использовать Visual Studio 2019 как основной инструмент для разработки и отладки модулей.

Средства Azure IoT Edge для Visual Studio обеспечивают следующие преимущества:

- создание, изменение, сборку, запуск и отладку решений и модулей Azure IoT Edge на локальном компьютере для разработки;
- развертывание решения Azure IoT Edge на устройстве Azure IoT Edge через Центр Интернета вещей;
- Код модулей Azure IoT на языке C или C# сохраняя при этом все преимущества разработки в Visual Studio.
- управление устройствами и модулями Azure IoT Edge с помощью пользовательского интерфейса.

В этой статье показано, как использовать инструменты Azure IoT Edge для Visual Studio 2019 для разработки модулей IoT Edge. Вы также узнаете, как развернуть свой проект на устройство Azure IoT Edge.

> [!TIP]
> Структура проекта IoT Edge, созданного в Visual Studio, отличается от структуры проекта Visual Studio Code.
  
## <a name="prerequisites"></a>Технические условия

В этой статье предполагается, что для разработки вы используете компьютер или виртуальную машину под управлением Windows. Устройство IoT Edge может быть другим физическим устройством.

Так как в этой статье используется Visual Studio 2019 как основное средство разработки, установите Visual Studio. Не забудьте включить **разработки Azure** и **разработка классических приложений на C++**  рабочих нагрузок в вашей установке Visual Studio 2019. Вы можете [изменение Visual Studio 2019](https://docs.microsoft.com/visualstudio/install/modify-visual-studio?view=vs-2019) для добавления рабочих нагрузок.

Когда в Visual Studio 2019 будет готов, необходимо также следующие средства и компоненты:

- Скачайте и установите [средства Edge Интернета вещей Azure (Предварительная версия)](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) из Visual Studio marketplace для создания проекта IoT Edge в Visual Studio 2019.

> [!TIP]
> Если вы используете Visual Studio 2017, plrease, загрузите и установите [средства Edge Интернета вещей Azure (Предварительная версия)](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) для Visual STUDIO 2017 в Visual Studio Marketplace

- Скачайте и установите [Docker Community Edition](https://docs.docker.com/install/) на компьютере разработки для сборки и запуска образов модуля. Вам необходимо настроить Docker CE для работы в режиме контейнера в Linux или Windows.

- Настройте локальную среду разработки для отладки, запуска и тестирования решения IoT Edge, установив [средство разработки Azure IoT EdgeHub](https://pypi.org/project/iotedgehubdev/). Установите [Python (2.7 или 3.6) и Pip](https://www.python.org/), а затем установите пакет **iotedgehubdev**, выполнив следующую команду в своем терминале. Используйте версию средства разработки Azure IoT EdgeHub выше версии 0.3.0.

   ```cmd
   pip install --upgrade iotedgehubdev
   ```

- Клонируйте репозиторий и установить диспетчер Vcpkg библиотеки, а затем установите **пакета azure-iot-sdk-c** для Windows.

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

Шаблон проекта Azure IoT Edge в Visual Studio создает проект, который можно развернуть на устройства Azure IoT Edge в Центре Интернета вещей. Сначала вы создаете решение Azure IoT Edge, и затем создать в этом решении первый модуль. Каждое решение IoT Edge может содержать несколько модулей.

1. В Visual Studio диалоговое окно нового проекта, найдите и выберите **Azure IoT Edge** проекта и нажмите кнопку **Далее**. В окне конфигурации проекта, введите имя проекта и укажите расположение и выберите **создать**. Имя проекта по умолчанию — **AzureIoTEdgeApp1**.

   ![Создание проекта](./media/how-to-visual-studio-develop-csharp-module/create-new.png)

1. В **Добавление приложения Edge Интернета вещей и модуль** окно, выберите пункт  **C# модуль** или **модуль C** и укажите имя модуля и модуль репозитория образов. Visual Studio автоматически заполняет имя модуля значением **localhost:5000/<имя вашего модуля\>** . Замените его собственными данными реестра. Если для тестирования вы используете локальный реестр Docker, вполне подойдет значение **localhost**. Если используется Реестр контейнеров Azure, укажите сервер входа, заданный в параметрах реестра. Значение для сервера входа выглядит так: * **\<имя реестра\>*.azurecr.io**. Замените только часть **localhost:5000** строки, чтобы конечный результат выглядел следующим образом: * *\<* имя реестра *\>.azurecr.io/* \<имя вашего модуля\>***. Имя модуля по умолчанию — **IoTEdgeModule1**.

   ![Добавить приложение и модуля](./media/how-to-visual-studio-develop-csharp-module/add-application-and-module.png)

1. Выберите **ОК** для создания решения Azure IoT Edge с модулем, который использует либо C# или C.

Теперь у вас есть **AzureIoTEdgeApp1.Linux.Amd64** проекта или **AzureIoTEdgeApp1.Windows.Amd64** проекта, а также **IoTEdgeModule1** проекта в решении. Каждый **AzureIoTEdgeApp1** проект имеет `deployment.template.json` файл, который определяет модули, требуется для создания и развертывания для вашего решения IoT Edge, а также определяет маршруты между модулями. Решение по умолчанию включает модули **tempSensor** и **IoTEdgeModule1**. Модуль **tempSensor** генерирует смоделированные данные в модуль **IoTEdgeModule1**, а код по умолчанию в модуле **IoTEdgeModule1** напрямую передает полученные сообщения в Центр Интернета вещей Azure.

**IoTEdgeModule1** проект представляет собой консольное приложение .NET Core 2.1, если это C# модуля. Он содержит обязательные файлы Docker, необходимые для вашего устройства IoT Edge, работающего с контейнером Windows или Linux. `module.json` Файл описывает метаданные модуля. Фактический модуля кода, который принимает в качестве зависимости пакет SDK устройства Интернета вещей Azure находится в `Program.cs` или `main.c` файл.

## <a name="develop-your-module"></a>Разработка модуля

Код модуля по умолчанию, входящий в состав решения расположенный **IoTEdgeModule1** > **Program.cs** (для C#) или **main.c** (C). Модуль и `deployment.template.json` файл настройки, чтобы построить решение, передать их в реестр контейнеров и развернуть его на устройство, чтобы начать тестирование без модификации кода. Модуль просто принимает входные данные из источника (в данном случае из модуля **tempSensor**, который имитирует данные) и передает их в Центр Интернета вещей Azure.

Когда вы будете готовы настроить шаблон модуля с вашим собственным кодом, используйте [пакеты SDK для центра Интернета вещей Azure](../iot-hub/iot-hub-devguide-sdks.md) для создания модулей этот адрес, ключ должен для решений Интернета вещей, такие как безопасность, управление устройствами и надежность.

## <a name="initialize-iotedgehubdev-with-iot-edge-device-connection-string"></a>Инициализация iotegehubdev с помощью строки подключения устройства IoT Edge

1. Скопируйте строку подключения любого устройства IoT Edge из **основной строки подключения** в Visual Studio Cloud Explorer. Не копируйте строку подключения другого устройства (не Edge), так как значок устройства IoT Edge отличается от значка другого устройства.

   ![Копирование строки подключения устройства IoT Edge](./media/how-to-visual-studio-develop-csharp-module/copy-edge-conn-string.png)

1. Перейдите к **средства** > **средства Edge Интернета вещей Azure** > **симулятор Edge Интернета вещей установки**, вставьте строку подключения и нажмите кнопку **ОК**.

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

1. Если разработка в среде C#, установите точку останова в `PipeMessage()` работать в **Program.cs**; Если с помощью c#, установите точку останова в `InputQueue1Callback()` работать в **main.c**. Затем можно проверить путем отправки сообщения, выполнив следующую команду в **Git Bash** или **WSL Bash** оболочки. (Команду `curl` нельзя запустить в PowerShell или командной строке.)

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

1. Добавьте второй модуль в решение, щелкните правой кнопкой мыши **AzureIoTEdgeApp1** и выбрав **добавить** > **новый модуль Edge Интернета вещей**. Стандартное имя второго модуля — **IoTEdgeModule2**, и он будет выступать в качестве другого модуля канала.

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

1. Создайте точки останова и нажмите клавишу **F5**, чтобы одновременно запускать и отлаживать несколько модулей. Вы должны увидеть несколько окон приложения консоли .NET Core, какие каждого окна, представляющий другой модуль.

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

1. Нажмите кнопку "Обновить", чтобы увидеть новые модули, работающие вместе с модулем **TempSensor**, а также **$edgeAgent** и **$edgeHub**.

## <a name="view-generated-data"></a>Просмотр сформированных данных

1. Чтобы отслеживать сообщения D2C для конкретного устройства, выберите устройство в списке и нажмите кнопку **начать мониторинг встроенную событий конечную точку** в **действие** окна.

1. Чтобы перестать отслеживать данные, выберите устройство в списке, а затем выберите **Остановить мониторинг встроенную событий конечную точку** в **действие** окна.

## <a name="next-steps"></a>Дальнейшие действия

Сведения о разработке пользовательских модулей для устройств IoT Edge см. в статье [Понимание и использование пакетов SDK для Центра Интернета вещей Azure](../iot-hub/iot-hub-devguide-sdks.md).
