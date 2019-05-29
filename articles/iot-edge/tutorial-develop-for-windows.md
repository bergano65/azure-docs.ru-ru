---
title: Разработка модулей для устройств Windows (Azure IoT Edge) | Документация Майкрософт
description: В этом учебнике описано, как настроить компьютер и облачные ресурсы для разработки модулей IoT Edge с использованием контейнеров Windows для устройств с Windows.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/20/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 631338c0217eb61f4f98cd06ffa16cb2500f246b
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/23/2019
ms.locfileid: "66146730"
---
# <a name="tutorial-develop-iot-edge-modules-for-windows-devices"></a>Руководство по разработке модулей IoT Edge для устройств с Windows

Для разработки и развертывания кода на устройствах с Windows с IoT Edge используйте Visual Studio 2017.

В кратком руководстве вы создали устройство IoT Edge, использующее виртуальную машину Windows, и развернули предварительно собранный модуль из Azure Marketplace. В этом учебнике описано, что нужно для разработки и развертывания собственного кода на устройстве IoT Edge. Полезно ознакомиться с этим учебником, прежде чем изучать другие учебники, в которых подробнее рассматриваются конкретные языки программирования или службы Azure. 

В этом учебнике используется пример развертывания модуля **C на устройстве Windows**. Этот пример был выбран из-за его простоты, так что вы можете узнать о средствах разработки, не беспокоясь о том, установлены ли у вас нужные библиотеки. Как только вы поймете принципы разработки, вы сможете выбрать предпочитаемый язык или службу Azure, чтобы ознакомиться с подробностями. 

Из этого руководства вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * Настройка компьютера для разработки.
> * Создание проекта с помощью средств IoT Edge для Visual Studio 2017.
> * Создание проекта как контейнера и его сохранение в реестре контейнеров Azure.
> * Развертывание кода на устройстве IoT Edge. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="key-concepts"></a>Основные понятия

В этом учебнике рассказывается о разработке модуля IoT Edge. *Модуль IoT Edge*, а иногда просто *модуль*, является контейнером, который содержит исполняемый код. На устройстве IoT Edge можно развернуть один или несколько модулей. Модули выполняют определенные задачи, такие как прием данных с датчиков, выполнение анализа данных или операции очистки данных или отправки сообщений в центр Интернета вещей. Дополнительные сведения см. в статье [Understand Azure IoT Edge modules](iot-edge-modules.md) (Общие сведения о модулях Azure IoT Edge).

При разработке модулей IoT Edge важно понимать разницу между компьютером для разработки и целевым устройством IoT Edge, где в конечном итоге будет развернут модуль. Контейнер, созданный для хранения кода модуля, должен соответствовать операционной системе (ОС) *целевого устройства*. Для разработки контейнера Windows такой подход проще, так как контейнеры Windows запускаются только на операционных системах Windows. Но вы можете, например, использовать компьютер для разработки Windows, чтобы создать модули для устройств IoT Edge с Linux. В таком случае вам нужно было бы убедиться, что на компьютере для разработки запущены контейнеры Linux. При прохождении данного учебника помните о разнице между *ОС компьютера разработки* и *ОС контейнера*.

Этот учебник предназначен для устройств с Windows с IoT Edge. Устройства Windows IoT Edge используют контейнеры Windows. Для разработки решений для устройств с Windows мы советуем использовать Visual Studio 2017, поэтому в этом учебнике будет использоваться именно эта среда. Вы также можете использовать Visual Studio Code, хотя между этими двумя средствами есть различия в поддержке.

В следующей таблице перечислены поддерживаемые сценарии разработки **контейнеров Windows** в Visual Studio Code и Visual Studio 2017.

|   | Visual Studio Code | Visual Studio 2017 |
| - | ------------------ | ------------------ |
| **Службы Azure**; | Функции Azure <br> Azure Stream Analytics |   |
| **Языки** | C# (отладка не поддерживается) | C <br> C# |
| **Дополнительные сведения** | [Azure IoT Edge для Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) | [Средства Azure IoT Edge для Visual Studio 2017](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) |

В этом учебнике описываются этапы разработки в Visual Studio 2017. Дополнительные сведения см. в статье [Use Visual Studio Code to develop and debug modules for Azure IoT Edge (Preview)](how-to-vs-code-develop-module.md) (Разработка и отладка модулей для Azure IoT Edge (предварительная версия) с помощью Visual Studio Code).

## <a name="prerequisites"></a>Предварительные требования

Компьютер для разработки:

* Windows 10 с обновлением 1809 или новее.
* При разработке можно использовать собственный компьютер или виртуальную машину в зависимости от ваших предпочтений.
* Установите [Git](https://git-scm.com/). 
* Установите пакет SDK Azure IoT для C для Windows X64 с помощью vcpkg:

   ```powershell
   git clone https://github.com/Microsoft/vcpkg
   cd vcpkg
   .\bootstrap-vcpkg.bat
   .\vcpkg install azure-iot-sdk-c:x64-windows
   .\vcpkg --triplet x64-windows integrate install
   ```

<!--vcpkg only required for C development-->

Устройство Azure IoT Edge на платформе Windows.

* Мы не советуем запускать IoT Edge на компьютере разработки. Вместо этого следует использовать другое устройство. Это различие между компьютером для разработки и устройством IoT Edge точнее отражает реальный сценарий развертывания и поможет не путать различные понятия.
* Если у вас нет второго доступного устройства, воспользуйтесь краткой статьей, чтобы создать устройство IoT Edge в Azure с [виртуальной машиной Windows](quickstart.md).

Облачные ресурсы.

* [Центр Интернета вещей](../iot-hub/iot-hub-create-through-portal.md) ценовой категории "Бесплатный" или "Стандартный" в Azure. 

## <a name="install-container-engine"></a>Установка платформы контейнеров

Модули IoT Edge упакованы в виде контейнеров, поэтому для создания контейнеров и управления ими на компьютере разработки необходима платформа контейнеров. Для разработки приложений советуем использовать Docker Desktop из-за его обширного функционала и популярности в качестве платформы контейнеров. С помощью Docker Desktop на компьютере с Windows можно переключаться между контейнерами Linux и контейнерами Windows, чтобы с легкостью разрабатывать модули для различных типов устройств IoT Edge. 

Чтобы выполнить установку на компьютере разработки, используйте документацию по Docker: 

* [Установка Docker Desktop для Windows](https://docs.docker.com/docker-for-windows/install/)

  * При установке Docker Desktop для Windows вы увидите запрос на использование контейнеров Linux или Windows. Для этого учебника используйте **контейнеры Windows**. Дополнительные сведения см. в документации по Docker [о переключении между контейнерами Windows и Linux](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers).


## <a name="set-up-visual-studio-and-tools"></a>Настройка Visual Studio и инструментов

При разработке модулей IoT Edge используйте расширения Интернета вещей для Visual Studio 2017. Эти расширения предоставляют шаблоны проектов, автоматизируют создание манифеста развертывания и позволяют отслеживать устройства IoT Edge и управлять ими. В этом разделе вы установите Visual Studio и расширения IoT Edge, а затем настроите учетную запись Azure для управления ресурсами Центра Интернета вещей в Visual Studio. 

1. Если у вас еще нет Visual Studio на компьютере для разработки, [установите Visual Studio 2017](https://docs.microsoft.com/visualstudio/install/install-visual-studio?view=vs-2017) со следующими рабочими нагрузками: 

   * разработка Azure;
   * разработка классических приложений на C++;
   * Кроссплатформенная разработка .NET Core.

1. Если у вас уже есть Visual Studio 2017 на компьютере для разработки, убедитесь, что его версия 15.7 или выше. Выполните шаги, приведенные в статье [Изменение Visual Studio путем добавления или удаления рабочих нагрузок и компонентов](https://docs.microsoft.com/visualstudio/install/modify-visual-studio?view=vs-2017), чтобы добавить требуемые рабочие нагрузки, если их еще у вас нет.

2. Скачайте и установите расширение [Средства Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) для Visual Studio 2017. 

3. После установки откройте Visual Studio.

4. Выберите **Представление** > **Cloud Explorer**. 

5. В Cloud Explorer выберите значок профиля и войдите в учетную запись Azure, если вы еще не вошли. 

6. После входа будут перечислены ваши подписки Azure. Выберите подписки, с которыми вы собираетесь получить доступ через Cloud Explorer, а затем щелкните **Применить**. 

7. Разверните подписку, затем **Центры Интернета вещей** и свой центр Интернета вещей. Вы должны увидеть список устройств Интернета вещей и сможете использовать этот обозреватель, чтобы управлять ими. 

   ![Доступ к ресурсам Центра Интернета вещей в Cloud Explorer](./media/tutorial-develop-for-windows/cloud-explorer-view-hub.png)

[!INCLUDE [iot-edge-create-container-registry](../../includes/iot-edge-create-container-registry.md)]

## <a name="create-a-new-module-project"></a>Создание проекта модуля

Расширение "Средства Azure IoT Edge" предоставляет шаблоны проектов для всех поддерживаемых языков модулей IoT Edge в Visual Studio 2017. Эти шаблоны содержат все файлы и код, необходимые для развертывания рабочего модуля для тестирования IoT Edge, а также станут отправной точкой для настройки шаблона в соответствии с вашей бизнес-логикой. 

1. Запустите Visual Studio от имени администратора.

2. Выберите **Файл** > **Создать** > **Проект**. 

3. В окне нового проекта выберите тип проекта **Azure IoT** и проект **Azure IoT Edge**. Переименуйте проект и решение или примите значение по умолчанию **AzureIoTEdgeApp1**. Нажмите кнопку **ОК**, чтобы создать проект. 

   ![Создание проекта Azure IoT Edge](./media/tutorial-develop-for-windows/new-project.png)

4. В приложении и окне модуля IoT Edge настройте проект с помощью следующих значений: 

   | Поле | Значение |
   | ----- | ----- |
   | Платформа приложений | Снимите флажок **Linux Amd64** и установите флажок **WindowsAmd64**. |
   | Выберите шаблон | Выберите **Модуль C**. | 
   | Имя проекта модуля | Примите значение по умолчанию **IoTEdgeModule1**. | 
   | Репозиторий образа Docker | Репозиторий изображений включает в себя имя реестра контейнеров и имя образа контейнера. Образ контейнера предварительно заполняется значением имени проекта модуля. Замените **localhost:5000** на значение сервера входа из реестра контейнеров Azure. Вы можете извлечь сервер входа на странице "Обзор" реестра контейнеров на портале Azure. <br><br> Окончательный вариант репозитория образов выглядит так: \<имя_реестра\>.azurecr.io/iotedgemodule1. |

   ![Настройка целевого устройства, типа модуля и реестра контейнеров для проекта](./media/tutorial-develop-for-windows/add-application-and-module.png)

5. Щелкните **ОК**, чтобы применить изменения. 

Как только ваш новый проект загрузится в окне Visual Studio, ознакомьтесь с файлами, которые он создал: 

* Проект IoT Edge с именем **AzureIoTEdgeApp1.Windows.Amd64**.
    * Папка **Модули** содержит указатели к модулям, включенным в проект. В данном случае это должен быть просто IoTEdgeModule1. 
    * Файл **deployment.template.json** представляет собой шаблон для создания манифеста развертывания. *Манифест развертывания* — это файл, который точно определяет, какие модули вы хотите развернуть на устройстве, как они должны быть настроены и как они могут взаимодействовать друг с другом и облаком. 
* Проект модуля IoT Edge с именем **IoTEdgeModule1**.
    * Файл **main.c** содержит код модуля C по умолчанию, который поставляется с шаблоном проекта. Модуль по умолчанию принимает входные данные из источника и передает их в Центр Интернета вещей. 
    * Файл **module.json** содержит сведения о модуле, включая полный репозиторий образа, версию образа и Dockerfile, который следует использовать для каждой поддерживаемой платформы.

### <a name="provide-your-registry-credentials-to-the-iot-edge-agent"></a>Предоставление учетных данных реестра агенту IoT Edge

Среде выполнения IoT Edge нужны учетные данные реестра, чтобы извлечь образы контейнеров на устройство IoT Edge. Добавьте эти учетные данные к шаблону развертывания. 

1. Откройте файл **deployment.template.json**.

2. Найдите свойство **registryCredentials** в требуемых свойствах $edgeAgent. 

3. Обновите свойство своими учетными данными в следующем формате: 

   ```json
   "registryCredentials": {
     "<registry name>": {
       "username": "<username>",
       "password": "<password>",
       "address": "<registry name>.azurecr.io"
     }
   }

4. Save the deployment.template.json file. 

### Review the sample code

The solution template that you created includes sample code for an IoT Edge module. This sample module simply receives messages and then passes them on. The pipeline functionality demonstrates an important concept in IoT Edge, which is how modules communicate with each other.

Each module can have multiple *input* and *output* queues declared in their code. The IoT Edge hub running on the device routes messages from the output of one module into the input of one or more modules. The specific language for declaring inputs and outputs varies between languages, but the concept is the same across all modules. For more information about routing between modules, see [Declare routes](module-composition.md#declare-routes).

1. In the **main.c** file, find the **SetupCallbacksForModule** function.

2. This function sets up an input queue to receive incoming messages. It calls the C SDK module client function [SetInputMessageCallback](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-ll-h/iothubmoduleclient-ll-setinputmessagecallback). Review this function and see that it initializes an input queue called **input1**. 

   ![Find the input name in the SetInputMessageCallback constructor](./media/tutorial-develop-for-windows/declare-input-queue.png)

3. Next, find the **InputQueue1Callback** function.

4. This function processes received messages and sets up an output queue to pass them along. It calls the C SDK module client function [SendEventToOutputAsync](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-ll-h/iothubmoduleclient-ll-sendeventtooutputasync). Review this function and see that it initializes an output queue called **output1**. 

   ![Find the output name in the SendEventToOutputAsync constructor](./media/tutorial-develop-for-windows/declare-output-queue.png)

5. Open the **deployment.template.json** file.

6. Find the **modules** property of the $edgeAgent desired properties. 

   There should be two modules listed here. The first is **tempSensor**, which is included in all the templates by default to provide simulated temperature data that you can use to test your modules. The second is the **IotEdgeModule1** module that you created as part of this project.

   This modules property declares which modules should be included in the deployment to your device or devices. 

7. Find the **routes** property of the $edgeHub desired properties. 

   One of the functions if the IoT Edge hub module is to route messages between all the modules in a deployment. Review the values in the routes property. The first route, **IotEdgeModule1ToIoTHub**, uses a wildcard character (**\***) to include any message coming from any output queue in the IoTEdgeModule1 module. These messages go into *$upstream*, which is a reserved name that indicates IoT Hub. The second route, **sensorToIotEdgeModule1**, takes messages coming from the tempSensor module and routes them to the *input1* input queue of the IotEdgeModule1 module. 

   ![Review routes in deployment.template.json](./media/tutorial-develop-for-windows/deployment-routes.png)


## Build and push your solution

You've reviewed the module code and the deployment template to understand some key deployment concepts. Now, you're ready to build the IotEdgeModule1 container image and push it to your container registry. With the IoT tools extension for Visual Studio, this step also generates the deployment manifest based on the information in the template file and the module information from the solution files. 

### Sign in to Docker

Provide your container registry credentials to Docker on your development machine so that it can push your container image to be stored in the registry. 

1. Open PowerShell or a command prompt.

2. Sign in to Docker with the Azure container registry credentials that you saved after creating the registry. 

   ```cmd
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Может появиться предупреждение системы безопасности с рекомендацией использовать `--password-stdin`. Для рабочих сценариев это лучшая методика, но мы не будем рассматривать ее в этом учебнике. Дополнительные сведения см. в справочнике по [docker login](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin).

### <a name="build-and-push"></a>Создание и отправка

Компьютер для разработки и устройства IoT Edge теперь имеют доступ к реестру контейнеров. Пришло время превратить код проекта в образ контейнера. 

1. Щелкните правой кнопкой мыши папку проекта **AzureIotEdgeApp1.Windows.Amd64** и выберите **Build and Push IoT Edge Modules** (Создание и отправка модулей IoT Edge). 

   ![Создание и отправка модулей IoT Edge](./media/tutorial-develop-for-windows/build-and-push-modules.png)

   Команда сборки и отправки запускает три операции. Во-первых, она создает в решении папку с именем **config**, которая содержит полный манифест развертывания на основе сведений из шаблона развертывания и других файлов решения. Во-вторых, она выполняет команду `docker build` для сборки образа контейнера на основе подходящего файла dockerfile для целевой архитектуры. В-третьих, она выполняет команду `docker push` для отправки репозитория образов в реестр контейнеров. 

   В первый раз выполнение этого процесса может занять несколько минут, но при следующем запуске команд он будет быстрее. 

2. Откройте файл **deployment.windows-amd64.json** в созданной папке config. (Папка config может не отображаться в обозревателе решений в Visual Studio. Если это так, выберите значок **Показать все файлы** на панели задач обозревателя решений.)

3. Найдите параметр **image** в разделе IotEdgeModule1. Обратите внимание, что образ содержит полный репозиторий образов с именем, версией и тегом архитектуры из модуля файла module.json.

4. Откройте файл **module.json** в папке IotEdgeModule1. 

5. Измените номер версии для образа модуля. (Это не версия $schema). Например, увеличьте номер версии исправления до **0.0.2**, как будто мы сделали небольшое исправление в коде модуля. 

   >[!TIP]
   >Версии модулей обеспечивают управление версиями и позволяют тестировать изменения на небольшом наборе устройств, прежде чем развертывать обновления в рабочей среде. Если перед построением и принудительной отправкой вы не увеличите версию модуля, перезапишите репозиторий в реестре контейнеров. 

6. Сохраните изменения в файле module.json.

7. Снова щелкните правой кнопкой мыши папку проекта **AzureIotEdgeApp1.Windows.Amd64** и выберите **Build and Push IoT Edge modules** (Создание и отправка модулей IoT Edge). 

8. Снова откройте файл **deployment.windows-amd64.json**. Обратите внимание, что при повторном выполнении команды сборки и отправки новый файл не был создан. Вместо этого, чтобы отразить изменения, был обновлен тот же файл. Образ IotEdgeModule1 теперь указывает на контейнер версии 0.0.2. Это изменение в манифесте развертывания заключается в том, как вы сообщаете устройству IoT Edge, что есть новая версия модуля для извлечения. 

9. Чтобы дополнительно проверить, что сделала команда создания и отправки, откройте [портал Azure](https://portal.azure.com) и перейдите к своему реестру контейнеров. 

10. В реестре контейнеров щелкните **Репозитории**, а затем выберите **iotedgemodule1**. Убедитесь, что обе версии образа отправлены в реестр.

    ![Просмотр обеих версий образа в реестре контейнеров](./media/tutorial-develop-for-windows/view-repository-versions.png)

### <a name="troubleshoot"></a>Устранение неполадок

Если вы сталкиваетесь с ошибками при создании и отправке образа вашего модуля, это часто связано с настройкой Docker на вашем компьютере для разработки. Чтобы просмотреть конфигурацию, выполните следующие проверки: 

* Вы выполнили команду `docker login`, используя учетные данные, скопированные из реестра контейнеров? Эти учетные данные отличаются от тех, которые вы используете для входа на портал Azure. 
* Правильно ли указан репозиторий контейнеров? У вас есть правильное имя реестра контейнеров и правильное имя модуля? Чтобы проверить, откройте файл **module.json** в папке IotEdgeModule1. Значение репозитория должно выглядеть так: **\<имя_реестра\>.azurecr.io/iotedgemodule1**. 
* Если вы использовали другое имя, отличное от **IotEdgeModule1**, для вашего модуля, согласовано ли оно во всем решении?
* Ваш компьютер использует тот же тип контейнеров, который вы создаете? Этот учебник предназначен для устройств Windows IoT Edge, поэтому файлы Visual Studio должны иметь расширение **windows-amd64**, а Docker Desktop должен работать с контейнерами Windows. 

## <a name="deploy-modules-to-device"></a>Развертывание модулей на устройстве

Вы убедились, что образы встроенных контейнеров хранятся в реестре контейнеров, поэтому пришло время развернуть их на устройстве. Убедитесь, что ваше устройство IoT Edge запущено и работает. 

1. Откройте Cloud Explorer в Visual Studio и разверните сведения для центра Интернета вещей. 

2. Выберите имя устройства, которое необходимо развернуть. В списке **Действия** выберите **Создать развертывание**.

   ![Создание развертывания для одного устройства](./media/tutorial-develop-for-windows/create-deployment.png)


3. В проводнике перейдите в папку ​​config проекта и выберите файл **deployment.windows-amd64.json**. Этот файл всегда находится здесь: `C:\Users\<username>\source\repos\AzureIotEdgeApp1\AzureIotEdgeApp1.Windows.Amd64\config\deployment.windows-amd64.json`

   Не используйте файл deployment.template.json, в котором нет полных значений образа модуля. 

4. Разверните сведения для устройства IoT Edge в Cloud Explorer, чтобы просмотреть модули на устройстве.

5. С помощью кнопки **Обновить** обновите состояние устройства, чтобы увидеть, что модули tempSensor и IotEdgeModule1 развернуты на устройстве. 


   ![Просмотр данных о модулях, запущенных на устройстве IoT Edge](./media/tutorial-develop-for-windows/view-running-modules.png)

## <a name="view-messages-from-device"></a>Просмотр сообщений с устройства

Код IotEdgeModule1 получает сообщения через свою очередь входящих сообщений и передает их через свою очередь выходящих сообщений. В манифесте развертывания объявлены маршруты, которые передают сообщения из tempSensor в IotEdgeModule1, а затем пересылают сообщения из IotEdgeModule1 в Центр Интернета вещей. Средства Azure IoT Edge для Visual Studio позволяют просматривать сообщения по мере их поступления в Центр Интернета вещей с ваших отдельных устройств. 

1. В Visual Studio Cloud Explorer выберите имя устройства IoT Edge, в котором выполнено развертывание. 

2. В меню **Actions** (Действия) выберите **Start Monitoring D2C Message** (Начать мониторинг сообщения D2C).

3. Посмотрите в раздел **Вывод** в Visual Studio, чтобы увидеть сообщения, поступающие в центр Интернета вещей. 

   Модули могут запуститься через несколько минут. Среда выполнения IoT Edge должна получить новый манифест развертывания, извлечь образы модулей из среды выполнения контейнеров, а затем запустить каждый новый модуль. Если же: 

   ![Просмотр входящих сообщений с устройств в облако](./media/tutorial-develop-for-windows/view-d2c-messages.png)

## <a name="view-changes-on-device"></a>Просмотр изменений на устройстве

Чтобы проверить среду выполнения IoT Edge и модули, работающие на вашем устройстве, используйте команды, приведенные в этом разделе. 

Команды в этом разделе предназначены для устройства IoT Edge, а не для компьютера для разработки. Если вы используете виртуальную машину для устройства IoT Edge, подключитесь к ней сейчас. В Azure перейдите на страницу обзора виртуальной машины и щелкните **Подключить**, чтобы получить доступ к соединению удаленного рабочего стола. На устройстве откройте окно команды или PowerShell, чтобы запустить команды `iotedge`.

* Просмотрите все модули, развернутые на вашем устройстве, и проверьте их состояние:

   ```cmd
   iotedge list
   ```

   Вы увидите четыре модуля: два модуля среды выполнения IoT Edge, tempSensor и IotEdgeModule1. Все четыре должны быть указаны как работающие.

* Просмотрите журналы для определенного модуля:

   ```cmd
   iotedge logs <module name>
   ```

   Модули IoT Edge учитывают регистр. 

   Журналы tempSensor и IotEdgeModule1 должны отображать сообщения, которые они обрабатывают. Модуль edgeAgent отвечает за запуск других модулей, поэтому его журналы будут содержать информацию о реализации манифеста развертывания. Если какой-либо модуль не указан или не запущен, возможно, в журналах edgeAgent возникли ошибки. Модуль edgeHub отвечает за обмен данными между модулями и Центром Интернета вещей. Если модули запущены и работают, но сообщения не поступают в ваш центр Интернета вещей, вероятно журналы edgeHub будут содержать ошибки. 

## <a name="next-steps"></a>Дополнительная информация

В этом учебнике вы настроили Visual Studio 2017 на своем компьютере для разработки и развернули на нем свой первый модуль IoT Edge. Теперь, когда вы знаете основные понятия, попробуйте добавить функциональность в модуль, чтобы он мог анализировать данные, проходящие через него. Выберите предпочитаемый язык. 

> [!div class="nextstepaction"] 
> [C](tutorial-c-module-windows.md)
> [C#](tutorial-csharp-module-windows.md)