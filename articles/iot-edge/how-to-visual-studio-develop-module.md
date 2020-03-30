---
title: Разработка и отладка модулей в Visual Studio - Azure IoT Edge
description: Используйте Visual Studio с помощью инструментов Azure IoT Для разработки модуля C или C IoT Edge и отодвинуте его из концентратора IoT на устройство IoT, настроенное манифестом развертывания.
services: iot-edge
author: shizn
manager: philmea
ms.author: xshi
ms.date: 3/27/2020
ms.topic: conceptual
ms.service: iot-edge
ms.openlocfilehash: 9722c7dec3a066d8f776424cb599be0d463416d9
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/29/2020
ms.locfileid: "80384863"
---
# <a name="use-visual-studio-2019-to-develop-and-debug-modules-for-azure-iot-edge"></a>Используйте Visual Studio 2019 для разработки и отладки модулей для Azure IoT Edge

Вы можете превратить бизнес-логику в модули для Azure IoT Edge. В этой статье показано, как использовать Visual Studio 2019 в качестве основного инструмента для разработки и отладки модулей.

Средства Azure IoT Edge для Visual Studio обеспечивают следующие преимущества:

- создание, изменение, сборку, запуск и отладку решений и модулей Azure IoT Edge на локальном компьютере для разработки;
- развертывание решения Azure IoT Edge на устройстве Azure IoT Edge через Центр Интернета вещей;
- Кодируйте свои модули Azure IoT в C или C, имея при этом все преимущества разработки Visual Studio.
- управление устройствами и модулями Azure IoT Edge с помощью пользовательского интерфейса.

В этой статье показано, как использовать инструменты Azure IoT Edge для Visual Studio 2019 для разработки модулей IoT Edge. Вы также узнаете, как развернуть свой проект на устройство Azure IoT Edge. В настоящее время Visual Studio 2019 обеспечивает поддержку модулей, написанных на C и C. Поддерживаемые архитектуры устройств : Windows X64 и Linux X64 или ARM32. Для получения дополнительной информации об поддерживаемых операционных системах, языках и архитектурах можно [ознакомиться на поддержке Language and architecture.](module-development.md#language-and-architecture-support)
  
## <a name="prerequisites"></a>Предварительные требования

В этой статье предполагается, что для разработки вы используете компьютер или виртуальную машину под управлением Windows. На компьютерах Windows можно разрабатывать модули Windows или Linux. Для разработки модулей Windows используйте Windows-компьютер под управлением версии 1809/build 17763 или новее. Для разработки модулей Linux используйте компьютер Windows, отвечающий [требованиям Docker Desktop.](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install)

Поскольку эта статья использует Visual Studio 2019 в качестве основного инструмента разработки, установите Visual Studio. Убедитесь, что вы включаете **разработку Azure** и разработку рабочего стола с рабочими нагрузками **на C-образную** студию в установке Visual Studio 2019. Вы можете [изменить Visual Studio 2019,](https://docs.microsoft.com/visualstudio/install/modify-visual-studio?view=vs-2019) чтобы добавить необходимые рабочие нагрузки.

После того, как Visual Studio 2019 будет готова, вам также нужны следующие инструменты и компоненты:

- Скачать и установить [Azure IoT Edge Tools](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) с рынка Visual Studio для создания проекта IoT Edge в Visual Studio 2019.

> [!TIP]
> Если вы используете Visual Studio 2017, пожалуйста, скачайте и установите [инструменты Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) для VS 2017 с рынка Visual Studio

- Скачайте и установите [Docker Community Edition](https://docs.docker.com/install/) на компьютере разработки для сборки и запуска образов модуля. Вам необходимо настроить Docker CE для работы в режиме контейнера в Linux или Windows.

- Настройте локальную среду разработки для отладки, запуска и тестирования решения IoT Edge, установив [средство разработки Azure IoT EdgeHub](https://pypi.org/project/iotedgehubdev/). Установите [Python (2.7/3.6") и Pip,](https://www.python.org/) а затем установите пакет **iotedgehubdev,** запустив следующую команду в терминале. Используйте версию средства разработки Azure IoT EdgeHub выше версии 0.3.0.

   ```cmd
   pip install --upgrade iotedgehubdev
   ```

- Клонировать репозиторий и установить менеджер библиотеки Vcpkg, а затем установить **пакет azure-iot-sdk-c** для Windows.

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

1. Из меню **расширения** выберите **расширения управления.** Расширить **установленные > инструменты,** и вы можете найти **Azure IoT Edge Инструменты для визуальной студии** и **Cloud Explorer для визуальной студии**.

1. Запишите номер установленной версии. Вы можете сравнить эту версию с последней версией в Visual Studio Marketplace ([Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS2019), [Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools)).

1. Если вы используете более раннюю версию, чем та, которая доступна в Visual Studio Marketplace, обновите инструменты в Visual Studio, как показано в следующем разделе.

### <a name="update-your-tools"></a>Обновление инструментов

1. В окне **расширения управления** расширьте обновления > Visual **Studio Marketplace,** выберите **инструменты Azure IoT Edge** или Cloud Explorer для **визуальной студии** и выберите **обновление.**

1. Скачав обновления для инструментов, закройте Visual Studio, чтобы активировать обновления с помощью установщика VSIX.

1. В установщике нажмите кнопку **ОК**, чтобы начать запуск, и выберите **Изменить** для обновления инструментов.

1. Когда обновление завершится, нажмите кнопку **Закрыть** и перезапустите Visual Studio.

### <a name="create-an-azure-iot-edge-project"></a>Создание проекта Azure IoT Edge

Шаблон проекта Azure IoT Edge в Visual Studio создает проект, который можно развернуть на устройства Azure IoT Edge в Центре Интернета вещей. Сначала вы создаете решение Azure IoT Edge, а затем создаете первый модуль в этом решении. Каждое решение IoT Edge может содержать несколько модулей.

> [!TIP]
> Структура проекта IoT Edge, созданного в Visual Studio, отличается от структуры проекта Visual Studio Code.

1. В visual Studio новый диалог проекта, поиск и выберите **Проект Azure IoT Edge** и нажмите **Кнопка Next**. В окне конфигурации проекта введите имя для проекта и укажите местоположение, а затем выберите **Создать.** Имя проекта по умолчанию — **AzureIoTEdgeApp1**.

   ![Создание проекта](./media/how-to-visual-studio-develop-csharp-module/create-new.png)

1. В окне **приложения и модуля Add IoT Edge** выберите модуль **C'** или **C Module,** а затем укажите свое имя модуля и репозиторий изображений модуля. Visual Studio автоматически заполняет имя модуля значением **localhost:5000/<имя вашего модуля\>**. Замените его собственными данными реестра. Если вы используете местный реестр Docker для тестирования, то **localhost** в порядке. Если используется Реестр контейнеров Azure, укажите сервер входа, заданный в параметрах реестра. Сервер входа выглядит как ** _ \<\>имя реестра_.azurecr.io**. Только заменить **localhost:5000** часть строки так, что конечный результат выглядит как ** \< *имя*\>реестра .azurecr.io/_\<имя\>модуля_**. Имя модуля по умолчанию **— IotEdgeModule1**

   ![Добавление приложения и модуля](./media/how-to-visual-studio-develop-csharp-module/add-application-and-module.png)

1. Выберите **OK** для создания решения Azure IoT Edge с модулем, который использует либо C, либо C.

Теперь у вас есть проект **AzureIoTEdgeApp1.Linux.Amd64** или проект **AzureIoTEdgeApp1.Windows.Amd64,** а также проект **IotEdgeModule1** в вашем решении. Каждый проект **AzureIoTEdgeApp1** имеет `deployment.template.json` файл, который определяет модули, которые вы хотите построить и развернуть для решения IoT Edge, а также определяет маршруты между модулями. Решение по умолчанию имеет модуль **SimulatedTemperatureSensor** и модуль **IotEdgeModule1.** **Модуль SimulatedTemperatureSensor** генерирует смоделированные данные в модуль **IotEdgeModule1,** в то время как код по умолчанию в модуле **IotEdgeModule1** непосредственно получает сообщения в Azure IoT Hub.

Чтобы увидеть, как работает смоделированный датчик температуры, просмотрите [исходный код SimulatedTemperatureSensor.csproj.](https://github.com/Azure/iotedge/tree/master/edge-modules/SimulatedTemperatureSensor)

Проект **IotEdgeModule1** — это консольное приложение .NET Core 2.1, если это модуль C.'. Он содержит обязательные файлы Docker, необходимые для вашего устройства IoT Edge, работающего с контейнером Windows или Linux. Файл `module.json` описывает метаданные модуля. Фактический код модуля, который использует SDK IoT-устройства Azure `main.c` в качестве зависимости, находится в файле или файле. `Program.cs`

## <a name="develop-your-module"></a>Разработка модуля

Код модуля по умолчанию, который поставляется с решением, находится на **iotEdgeModule1** > **Program.cs** (для C) или **main.c** (C). Модуль и `deployment.template.json` файл настроены таким образом, что вы можете создать решение, нажать его на контейнер реестра, и развернуть его на устройство, чтобы начать тестирование, не касаясь какого-либо кода. Модуль построен для ввода из источника (в данном случае модуля **SimulatedTemperatureSensor,** который имитирует данные) и сравнять его с Azure IoT Hub.

Когда вы будете готовы настроить шаблон модуля с помощью собственного кода, используйте [sDK Azure IoT Hub](../iot-hub/iot-hub-devguide-sdks.md) для создания модулей, которые удовлетворяют ключевые потребности в решениях IoT, таких как безопасность, управление устройствами и надежность.

## <a name="initialize-iotedgehubdev-with-iot-edge-device-connection-string"></a>Инициализация iotegehubdev с помощью строки подключения устройства IoT Edge

1. Скопируйте строку подключения любого устройства IoT Edge из **основной строки подключения** в Visual Studio Cloud Explorer. Не копируйте строку подключения другого устройства (не Edge), так как значок устройства IoT Edge отличается от значка другого устройства.

   ![Копирование строки подключения устройства IoT Edge](./media/how-to-visual-studio-develop-csharp-module/copy-edge-conn-string.png)

1. Из меню **Инструменты** выберите **Azure IoT Edge Tools** > **Настройка IoT Edge Simulator,** вставьте строку соединения и нажмите **OK**.

   ![Открытое окно установки строки подключения к Edge](./media/how-to-visual-studio-develop-csharp-module/set-edge-conn-string.png)

1. Введите строку подключения из первого шага, а затем нажмите кнопку **ОК**.

> [!NOTE]
> Выполните следующие действия на компьютере разработки только раз, так как результаты автоматически применяются ко всем последующим решениям Azure IoT Edge. Если вам понадобится изменить строку подключения, тогда эту процедуру нужно выполнить еще раз.

## <a name="build-and-debug-single-module"></a>Сборка и отладка одного модуля

Как правило, нужно выполнить тестирование и отладку каждого модуля, прежде чем запустить его в рамках целого решения с несколькими модулями.

1. В **Solution Explorer**, правой кнопкой мыши **IotEdgeModule1** и выберите **Set как StartUp Project** из контекстного меню.

   ![Задание запускаемого проекта](./media/how-to-visual-studio-develop-csharp-module/module-start-up-project.png)

1. Нажмите клавишу **F5** или кнопку ниже, чтобы запустить модуль. Это может занять 10&ndash;20 секунд в первый раз.

   ![Запуск модуля](./media/how-to-visual-studio-develop-csharp-module/run-module.png)

1. В случае успешной инициализации модуля вы должны увидеть запущенное консольное приложение .NET Core.

   ![Запущенный модуль](./media/how-to-visual-studio-develop-csharp-module/single-module-run.png)

1. При разработке в СЗ установите `PipeMessage()` точку разрыва в функции в **Program.cs;** при использовании C установите `InputQueue1Callback()` точку разрыва в функции **main.c.** Затем вы можете проверить его, отправив сообщение, запустив следующую команду в оболочке **Git Bash** или **WSL Bash.** (Команду `curl` нельзя запустить в PowerShell или командной строке.)

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

1. В **Solution Explorer**добавьте второй модуль к решению, нажав правой кнопкой **AzureIoTEdgeApp1** и выбрав **добавить** > **новый модуль IoT Edge.** По умолчанию имя второго модуля **IotEdgeModule2** и будет выступать в качестве другого модуля трубы.

1. Откройте `deployment.template.json` файл, и вы **увидите, что IotEdgeModule2** был добавлен в раздел **модулей.** Замените раздел **маршрутов** на следующее. Если вы настроили имена модулей, обновите их таким образом, чтобы они совпадали.

    ```json
        "routes": {
          "IotEdgeModule1ToIoTHub": "FROM /messages/modules/IotEdgeModule1/outputs/* INTO $upstream",
          "sensorToIotEdgeModule1": "FROM /messages/modules/SimulatedTemperatureSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/IotEdgeModule1/inputs/input1\")",
          "IotEdgeModule2ToIoTHub": "FROM /messages/modules/IotEdgeModule2/outputs/* INTO $upstream",
          "sensorToIotEdgeModule2": "FROM /messages/modules/SimulatedTemperatureSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/IotEdgeModule2/inputs/input1\")"
        },
    ```

1. Щелкните правой кнопкой мыши **AzureIoTEdgeApp1** и в контекстном меню выберите **Назначить запускаемым проектом**.

1. Создайте точки останова и нажмите клавишу **F5**, чтобы одновременно запускать и отлаживать несколько модулей. Вы должны увидеть несколько окон консоли .NET Core, каждое окно которого представляет собой другой модуль.

   ![Отладка нескольких модулей](./media/how-to-visual-studio-develop-csharp-module/debug-multiple-modules.png)

1. Нажмите клавиши **CTRL+F5** или выберите кнопку "Остановить", чтобы остановить отладку.

## <a name="build-and-push-images"></a>Создание и отправка образов

1. Задайте **AzureIoTEdgeApp1** как запускаемый проект. Выберите конфигурацию **Отладка** или **Выпуск**, которая будет применяться для образов модуля.

    > [!NOTE]
    > В случае выбора конфигурации **Отладка** Visual Studio будет использовать `Dockerfile.(amd64|windows-amd64).debug` для сборки образов Docker. Это включает отладчик командной строки .Net Core VSDBG в образе контейнера при его создании. Для модулей IoT Edge, готовых для рабочей среды, мы советуем выбрать конфигурацию **Выпуск**, которая использует `Dockerfile.(amd64|windows-amd64)` без VSDBG.

1. Если вы используете частный реестр, такой как Реестр контейнеров Azure (ACR), используйте следующую команду Docker, чтобы войти в него.  Имя пользователя и пароль можно получить со **страницы ключей доступа** вашего реестра на портале Azure. Если вы используете локальный реестр, вы можете выполнить команду [запуска локального реестра](https://docs.docker.com/registry/deploying/#run-a-local-registry).

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

1. В **Solution Explorer**, правой кнопкой мыши **AzureIoTEdgeApp1** и выберите **сборку и нажмите IoT Edge Модули** для создания и нажмите докер изображения для каждого модуля.

## <a name="deploy-the-solution"></a>Развертывание решения

При использовании руководства по настройке устройства IoT Edge модуль был развернут с помощью портала Azure. Вы также можете развернуть модули с помощью Cloud Explorer для Visual Studio. У вас уже есть манифест развертывания, подготовленный для вашего сценария, —это файл `deployment.json`. Вам остается только выбрать устройство для получения развертывания.

1. Откройте **Cloud Explorer**, щелкнув **Просмотр** > **Cloud Explorer**. Убедитесь, что вы вошли в Visual Studio 2019.

1. В **Cloud Explorer** разверните подписку, найдите Центр Интернета вещей Azure и устройство Azure IoT Edge, которое нужно развернуть.

1. Нажмите правой кнопкой мыши на устройстве IoT Edge, чтобы создать развертывание для него. Перейдите к развертываемому манифесту, настроенного для платформы, расположенной `deployment.arm32v7.json`в папке **конфигурации** в решении Visual Studio, например.

1. Нажмите кнопку обновления, чтобы увидеть новые модули работает вместе с **модулем SimulatedTemperatureSensor** и **$edgeAgent** и **$edgeHub**.

## <a name="view-generated-data"></a>Просмотр сформированных данных

1. Чтобы отслеживать сообщение D2C для определенного устройства IoT-Edge, выберите его в концентраторе IoT в **Cloud Explorer,** а затем нажмите **Start Monitoring Built-in Event Endpoint** в окне **действия.**

1. Чтобы остановить мониторинг данных, выберите **встроенную конечную точку событий Stop Monitoring** в окне **действия.**

## <a name="next-steps"></a>Дальнейшие действия

Сведения о разработке пользовательских модулей для устройств IoT Edge см. в статье [Понимание и использование пакетов SDK для Центра Интернета вещей Azure](../iot-hub/iot-hub-devguide-sdks.md).
