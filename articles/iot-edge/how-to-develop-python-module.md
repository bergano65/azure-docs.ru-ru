---
title: Отладка модулей Python для Azure IoT Edge | Документы Майкрософт
description: Использование Visual Studio Code для разработки, сборки и отладки модуля Python для Azure IoT Edge
services: iot-edge
keywords: ''
author: shizn
manager: timlt
ms.author: xshi
ms.date: 09/13/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 4393d3231ca4f0a813d4586161383bd6d61f6a49
ms.sourcegitcommit: f983187566d165bc8540fdec5650edcc51a6350a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/13/2018
ms.locfileid: "45552338"
---
# <a name="use-visual-studio-code-to-develop-and-debug-python-modules-for-azure-iot-edge"></a>Использование Visual Studio Code для разработки и отладки модулей Python для Azure IoT Edge

Вы можете превратить бизнес-логику в модули для Azure IoT Edge. В этой статье описывается, как использовать Visual Studio Code (VS Code) в качестве основного средства разработки и отладки модулей Python.

## <a name="prerequisites"></a>Предварительные требования
В этой статье предполагается, что для разработки вы используете компьютер или виртуальную машину под управлением Windows или Linux. Либо вы можете имитировать устройство IoT Edge на компьютере разработки с помощью управляющей программы безопасности IoT Edge.

> [!NOTE]
> В этой статье об отладке показано, как присоединить процесс в контейнер модуля и выполнить отладку модуля с помощью VS Code. Модули Python можно отлаживать только в контейнерах amd64 Linux. С возможностями отладки Visual Studio Code можно ознакомиться в [этой статье](https://code.visualstudio.com/Docs/editor/debugging). 

Так как в этой статье в качестве основного средства разработки используется Visual Studio Code, установите VS Code. Затем добавьте все необходимые расширения:
* [Visual Studio Code](https://code.visualstudio.com/) 
* [расширение Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge); 
* [расширение Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker).
* [Расширение Visual Studio Code для Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python). 
* [Python](https://www.python.org/downloads/).
* [PIP](https://pip.pypa.io/en/stable/installing/#installation) для установки пакетов Python (обычно входит в состав установки Python).
* Установите **Cookiecutter** с помощью приведенной ниже команды.
   
    ```cmd/sh
    pip install --upgrade --user cookiecutter
    ```

Для создания модуля необходимо иметь Docker для создания образа модуля и реестр контейнеров для хранения образа модуля.
* [Docker Community Edition](https://docs.docker.com/install/) на компьютере, на котором ведется разработка. 
* [Реестр контейнеров Azure](https://docs.microsoft.com/azure/container-registry/) или [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags).
   * Вместо облачного реестра можно использовать локальный реестр Docker для создания прототипов и тестирования. 

Для тестирования модуля на устройстве требуется действующий Центр Интернета вещей по крайней мере с одним устройством IoT Edge. Чтобы использовать компьютер в качестве устройства IoT Edge, выполните инструкции в кратком руководстве для [Linux](quickstart-linux.md). 

## <a name="create-a-new-solution-template"></a>Создайте новый шаблон решения

Выполните описанные здесь действия, чтобы создать модуль IoT Edge на основе пакета SDK Azure IoT для Python с использованием Visual Studio Code и расширения Azure IoT Edge. Сначала нужно создать решение, а затем сформировать в нем первый модуль. Каждое решение может содержать несколько модулей. 

1. В Visual Studio Code выберите **Вид** > **Интегрированный терминал**.

2. Выберите **Представление** > **Палитра команд**. 

3. В палитре команд введите и выполните команду **Azure IoT Edge: New IoT Edge Solution**.

   ![Запуск команды создания решения IoT Edge](./media/how-to-develop-csharp-module/new-solution.png)

4. Перейдите к папке, где требуется создать решение. Щелкните **Выбрать папку**. 

5. Введите имя для решения. 

6. Выберите **Модуль Python** в качестве шаблона для первого модуля в решении.

7. Введите имя модуля. Оно должно быть уникальным в пределах реестра контейнеров. 

8. Укажите имя репозитория образов для модуля. VS Code автоматически заполняет имя модуля значением **localhost:5000**. Замените его собственными данными реестра. Если для тестирования вы используете локальный реестр Docker, вполне подойдет значение **localhost**. Если используется Реестр контейнеров Azure, укажите сервер входа, заданный в параметрах реестра. Значение для сервера входа выглядит так: **\<имя реестра\>.azurecr.io**. Замените только часть строки localhost, не удаляйте имя модуля. 

   ![Выбор репозитория образа Docker](./media/how-to-develop-c-module/repository.png)

VS Code принимает предоставленные сведения, создает решение IoT Edge, а затем загружает его в новом окне.

В решении существует четыре элемента: 
* Папка **.vscode** содержит конфигурации отладки.
* Папка **modules** содержит вложенные папки для каждого модуля. На этом этапе у вас всего один модуль. Но вы можете добавить модули в палитре команд с помощью команды **Azure IoT Edge: Add IoT Edge Module**. 
* В файле **ENV** перечислены переменные среды. Если реестр контейнеров Azure является вашим реестром, вы получите в нем имя пользователя и пароль для реестра контейнеров Azure. 

   > [!NOTE]
   > Файл среды создается только в том случае, если вы указали репозиторий образов для модуля. Если вы не изменяли значения по умолчанию localhost для тестирования и отладки локально, объявлять переменные среды не требуется. 

* В файле **deployment.template.json** указан новый модуль, а также пример модуля **tempSensor**, который имитирует данные для тестирования. Дополнительные сведения о том, как работают манифесты развертывания, см. в разделе [Сведения об использовании манифестов развертывания для развертывания модулей и установки маршрутов](module-composition.md). 

## <a name="develop-your-module"></a>Разработка модуля

Код модуля Python, который поставляется с решением, по умолчанию находится в файле **modules** > [имя модуля] > **main.py**. Модуль и файл deployment.template.json настраиваются так, чтобы можно было собрать решение, передать его в реестр контейнеров и развернуть на устройстве, чтобы начать тестирование, не меняя код. Модуль просто принимает входные данные из источника (в данном случае модуль tempSensor имитирует данные) и передает их в Центр Интернета вещей. 

Когда вы будете готовы настроить шаблон Python с добавлением собственного кода, используйте [пакеты SDK для центра Интернета вещей Azure](../iot-hub/iot-hub-devguide-sdks.md), чтобы создать модули, которые удовлетворяют ключевые потребности решений Интернета вещей, такие как безопасность, управление устройствами и надежность. 

## <a name="build-and-deploy-your-module-for-debugging"></a>Создание и развертывание модуля для отладки

В папке каждого модуля находится несколько файлов Docker для разных типов контейнеров. Вы можете использовать любой **DEBUG**-файл, чтобы создать модуль для тестирования. В настоящее время модули Python поддерживают отладку только в контейнерах amd64 Linux. 

1. В VS Code перейдите к файлу `deployment.template.json`. Обновите URL-адрес образа модуля, добавив в конце **.debug**.

2. Замените модуль Python createOptions в файле **deployment.template.json** приведенным ниже содержимым и сохраните этот файл: 
    
    ```json
    "createOptions": "{\"ExposedPorts\":{\"5678/tcp\":{}},\"HostConfig\":{\"PortBindings\":{\"5678/tcp\":[{\"HostPort\":\"5678\"}]}}}"
    ```

3. Перейдите в файл `main.py` и добавьте следующий код после раздела import:
    
    ```python
    import ptvsd
    ptvsd.enable_attach(('0.0.0.0',  5678))
    ```

4. Добавьте следующую строку кода в обратный вызов, который необходимо отладить:

    ```python
    ptvsd.break_into_debugger()
    ```

   Например, вы хотите отладить метод `receive_message_callback`. Вы можете вставить всего одну строку кода, показанную ниже.

    ```python
    def receive_message_callback(message, hubManager):
        ptvsd.break_into_debugger()
        global RECEIVE_CALLBACKS
        message_buffer = message.get_bytearray()
        size = len(message_buffer)
        print ( "    Data: <<<%s>>> & Size=%d" % (message_buffer[:size].decode('utf-8'), size) )
        map_properties = message.properties()
        key_value_pair = map_properties.get_internals()
        print ( "    Properties: %s" % key_value_pair )
        RECEIVE_CALLBACKS += 1
        print ( "    Total calls received: %d" % RECEIVE_CALLBACKS )
        hubManager.forward_event_to_output("output1", message, 0)
        return IoTHubMessageDispositionResult.ACCEPTED
    ```

2. В палитре команд VS Code введите и выполните команду **Azure IoT Edge: Build and Push IoT Edge solution**.
3. Выберите файл `deployment.template.json` для решения из палитры команд. 
4. В Device Explorer Центра Интернета вещей Azure щелкните правой кнопкой мыши идентификатор устройства IoT Edge. Затем выберите **Create Deployment for Single Device** (Создание развертывания для одного устройства). 
5. Откройте папку **config** решения. Выберите файл `deployment.json`. Щелкните **Select Edge Deployment Manifest** (Выбрать манифест развертывания Edge). 

Ход развертывания можно отслеживать по идентификатору развертывания во встроенном терминале VS Code.

Чтобы проверить состояние контейнера, откройте обозреватель Docker VS Code или выполните команду `docker ps` в окне терминала.

## <a name="start-debugging-python-module-in-vs-code"></a>Запуск отладки модуля Python в VS Code
VS Code хранит информацию о конфигурации отладки в файле `launch.json`, расположенном в папке `.vscode` рабочей области. Этот файл `launch.json` создан при создании решения IoT Edge. Он автоматически обновляется при каждом добавлении нового модуля, который поддерживает отладку. 

1. Перейдите в представление отладки VS Code. Выберите файл конфигурации отладки для модуля. У действия отладки должно быть такое имя: **ModuleName Remote Debug (Python)**.

2. Перейдите на страницу `main.c`. Добавьте точку останова в метод обратного вызова, в который вы добавили `ptvsd.break_into_debugger()`.

3. Выберите **Начать отладку** или нажмите клавишу **F5**. Выберите процесс для присоединения.

4. В представлении отладки VS Code можно просмотреть переменные на панели слева. 

В предыдущем примере показан способ отладки модулей IoT Edge для Python в контейнерах. В нем добавлены открытые порты в контейнере модуля createOptions. После завершения отладки модулей Python рекомендуется удалить эти открытые порты для модулей IoT Edge, готовых для рабочей среды.

## <a name="next-steps"></a>Дополнительная информация

После создания модуля узнайте, как [развернуть модули Azure IoT Edge в Visual Studio Code](how-to-deploy-modules-vscode.md).

Чтобы разрабатывать модули для устройств IoT Edge, см. раздел [Понимание и использование пакетов SDK для Центра Интернета вещей Azure](../iot-hub/iot-hub-devguide-sdks.md).
