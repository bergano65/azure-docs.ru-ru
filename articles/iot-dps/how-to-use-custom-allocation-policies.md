---
title: Custom allocation policies with Azure IoT Hub Device Provisioning Service
description: Как использовать пользовательские политики выделения со Службой подготовки устройств к добавлению в Центр Интернета вещей Azure.
author: wesmc7777
ms.author: wesmc
ms.date: 11/14/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 2a17cc6c9f2211de31d4551bd12e6c832d4eee38
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74228732"
---
# <a name="how-to-use-custom-allocation-policies"></a>Как использовать пользовательские политики выделения

Пользовательская политика выделения обеспечивает больший контроль над назначением устройств в Центре Интернета вещей. Это достигается с помощью пользовательского кода в [функции Azure](../azure-functions/functions-overview.md), используемого для назначения устройств Центру Интернета вещей. Служба подготовки устройств вызывает код функции Azure, предоставляя всю необходимую информацию об устройстве и регистрации. Код функции выполняется и возвращает данные Центра Интернета вещей, используемые при подготовке устройства.

By using custom allocation policies, you define your own allocation policies when the policies provided by the Device Provisioning Service don't meet the requirements of your scenario.

For example, maybe you want to examine the certificate a device is using during provisioning and assign the device to an IoT hub based on a certificate property. Or, maybe you have information stored in a database for your devices and need to query the database to determine which IoT hub a device should be assigned to.

В этой статье показана работа с пользовательскими политиками с помощью кода функции Azure на языке C#. Создаются два новых Центра Интернета вещей, представляющие *отдел тостеров Contoso* и *отдел тепловых насосов Contoso*. Устройства, запрашивающие подготовку, должны иметь идентификатор регистрации с одним из следующих суффиксов, чтобы они были приняты для подготовки:

* **-contoso-tstrsd-007**: отдел тостеров Contoso;
* **-contoso-hpsd-088**: отдел тепловых насосов Contoso.

Устройства будут подготовлены в соответствии с этими обязательными суффиксами в идентификаторе регистрации. Эти устройства будут имитироваться с помощью примера для подготовки из [пакета SDK Azure IoT для C](https://github.com/Azure/azure-iot-sdk-c).

You perform the following steps in this article:

* Использование Azure CLI для создания центров Интернета вещей для двух отделов Contoso (**отдела тостеров Contoso** и **отдела тепловых насосов Contoso**).
* Создание группы регистрации с помощью функции Azure для пользовательской политики выделения.
* Создание ключей устройства для имитации двух устройств.
* Подготовка среды разработки для пакета SDK Azure IoT для C.
* Simulate the devices and verify that they are provisioned according to the example code in the custom allocation policy

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Технические условия

* [Visual Studio 2015](https://visualstudio.microsoft.com/vs/) или более поздней версии с включенной рабочей нагрузкой [Разработка классических приложений на C++](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/).
* Установите последнюю версию [Git](https://git-scm.com/download/).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-the-provisioning-service-and-two-divisional-iot-hubs"></a>Create the provisioning service and two divisional IoT hubs

In this section, you use the Azure Cloud Shell to create a provisioning service and two IoT hubs representing the **Contoso Toasters Division** and the **Contoso Heat Pumps division**.

> [!TIP]
> The commands used in this article create the provisioning service and other resources in the West US location. We recommend that you create your resources in the region nearest you that supports Device Provisioning Service. Чтобы просмотреть список доступных расположений, выполните команду `az provider show --namespace Microsoft.Devices --query "resourceTypes[?resourceType=='ProvisioningServices'].locations | [0]" --out table`, или перейдите на страницу [Состояние Azure](https://azure.microsoft.com/status/) и введите в строке поиска "Служба подготовки устройств". In commands, locations can be specified either in one word or multi-word format; for example: westus, West US, WEST US, etc. The value is not case sensitive. Если для указания расположения вы используете несколько слов, укажите значение поиска в кавычках, например `-- location "West US"`.
>

1. В Azure Cloud Shell создайте группу ресурсов с помощью команды [az group create](/cli/azure/group#az-group-create). Группа ресурсов Azure является логическим контейнером, в котором происходит развертывание ресурсов Azure и управление ими.

    The following example creates a resource group named *contoso-us-resource-group* in the *westus* region. Рекомендуется использовать эту группу для всех ресурсов, созданных в рамках этой статьи. This approach will make clean up easier after you're finished.

    ```azurecli-interactive 
    az group create --name contoso-us-resource-group --location westus
    ```

2. Use the Azure Cloud Shell to create a device provisioning service with the [az iot dps create](/cli/azure/iot/dps#az-iot-dps-create) command. The provisioning service will be added to *contoso-us-resource-group*.

    The following example creates a provisioning service named *contoso-provisioning-service-1098* in the *westus* location. You must use a unique service name. Make up your own suffix in the service name in place of **1098**.

    ```azurecli-interactive 
    az iot dps create --name contoso-provisioning-service-1098 --resource-group contoso-us-resource-group --location westus
    ```

    Выполнение этой команды может занять несколько минут.

3. В Azure Cloud Shell создайте Центр Интернета вещей **отдела тостеров Contoso** с помощью команды [az iot hub create](/cli/azure/iot/hub#az-iot-hub-create). Этот Центр Интернета вещей будет добавлен в группу *contoso-us-resource-group*.

    The following example creates an IoT hub named *contoso-toasters-hub-1098* in the *westus* location. You must use a unique hub name. Придумайте собственный суффикс в имени центра вместо **1098**. В примере кода для пользовательской политики выделения требуется наличие слова `-toasters-` в имени центра.

    ```azurecli-interactive 
    az iot hub create --name contoso-toasters-hub-1098 --resource-group contoso-us-resource-group --location westus --sku S1
    ```

    Выполнение этой команды может занять несколько минут.

4. В Azure Cloud Shell создайте Центр Интернета вещей **отдела тепловых насосов Contoso** с помощью команды [az iot hub create](/cli/azure/iot/hub#az-iot-hub-create). Этот Центр Интернета вещей также будет добавлен в группу *contoso-us-resource-group*.

    The following example creates an IoT hub named *contoso-heatpumps-hub-1098* in the *westus* location. You must use a unique hub name. Придумайте собственный суффикс в имени центра вместо **1098**. В примере кода для пользовательской политики выделения требуется наличие слова `-heatpumps-` в имени центра.

    ```azurecli-interactive 
    az iot hub create --name contoso-heatpumps-hub-1098 --resource-group contoso-us-resource-group --location westus --sku S1
    ```

    Выполнение этой команды может занять несколько минут.

## <a name="create-the-custom-allocation-function"></a>Create the custom allocation function

In this section, you create an Azure function that implements your custom allocation policy. This function decides which divisional IoT hub a device should be registered to based on whether its registration ID contains the string **-contoso-tstrsd-007** or **-contoso-hpsd-088**. It also sets the initial state of the device twin based on whether the device is a toaster or a heat pump.

1. Войдите на [портале Azure](https://portal.azure.com). From your home page, select **+ Create a resource**.

2. In the *Search the Marketplace* search box, type "Function App". From the drop-down list select **Function App**, and then select **Create**.

3. On **Function App** create page, under the **Basics** tab, enter the following settings for your new function app and select **Review + create**:

    **Resource Group**: Select the **contoso-us-resource-group** to keep all resources created in this article together.

    **Function App name**: Enter a unique function app name. This example uses **contoso-function-app-1098**.

    **Publish**: Verify that **Code** is selected.

    **Runtime Stack**: Select **.NET Core** from the drop-down.

    **Region**: Select the same region as your resource group. This example uses **West US**.

    > [!NOTE]
    > By default, Application Insights is enabled. Application Insights is not necessary for this article, but it might help you understand and investigate any issues you encounter with the custom allocation. If you prefer, you can disable Application Insights by selecting the **Monitoring** tab and then selecting **No** for **Enable Application Insights**.

    ![Create an Azure Function App to host the custom allocation function](./media/how-to-use-custom-allocation-policies/create-function-app.png)

4. On the **Summary** page, select **Create** to create the function app. Deployment may take several minutes. When it completes, select **Go to resource**.

5. On the left pane of the function app **Overview** page, select **+** next to **Functions** to add a new function.

    ![Add a function to the Function App](./media/how-to-use-custom-allocation-policies/create-function.png)

6. On the **Azure Functions for .NET - getting started** page, for the **CHOOSE A DEPLOYMENT ENVIRONMENT** step, select the **In-portal** tile, then select **Continue**.

    ![Select the portal development environment](./media/how-to-use-custom-allocation-policies/function-choose-environment.png)

7. On the next page, for the **CREATE A FUNCTION** step, select the **Webhook + API** tile, then select **Create**. A function named **HttpTrigger1** is created, and the portal displays the contents of the **run.csx** code file.

8. Reference required Nuget packages. To create the initial device twin, the custom allocation function uses classes that are defined in two Nuget packages that must be loaded into the hosting environment. With Azure Functions, Nuget packages are referenced using a *function.host* file. In this step, you save and upload a *function.host* file.

    1. Copy the following lines into your favorite editor and save the file on your computer as *function.host*.

        ```xml
        <Project Sdk="Microsoft.NET.Sdk">  
            <PropertyGroup>  
                <TargetFramework>netstandard2.0</TargetFramework>  
            </PropertyGroup>  
            <ItemGroup>  
                <PackageReference Include="Microsoft.Azure.Devices.Provisioning.Service" Version="1.5.0" />  
                <PackageReference Include="Microsoft.Azure.Devices.Shared" Version="1.16.0" />  
            </ItemGroup>  
        </Project>
        ```

    2. On the **HttpTrigger1** function, expand the **View Files** tab on the right side of the window.

        ![Open view files](./media/how-to-use-custom-allocation-policies/function-open-view-files.png)

    3. Select **Upload**, browse to the **function.proj** file, and select **Open** to upload the file.

        ![Select upload file](./media/how-to-use-custom-allocation-policies/function-choose-upload-file.png)

9. Replace the code for the **HttpTrigger1** function with the following code and select **Save**:

    ```csharp
    #r "Newtonsoft.Json"

    using System.Net;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Primitives;
    using Newtonsoft.Json;

    using Microsoft.Azure.Devices.Shared;               // For TwinCollection
    using Microsoft.Azure.Devices.Provisioning.Service; // For TwinState

    public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
    {
        log.LogInformation("C# HTTP trigger function processed a request.");

        // Get request body
        string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
        dynamic data = JsonConvert.DeserializeObject(requestBody);

        log.LogInformation("Request.Body:...");
        log.LogInformation(requestBody);

        // Get registration ID of the device
        string regId = data?.deviceRuntimeContext?.registrationId;

        string message = "Uncaught error";
        bool fail = false;
        ResponseObj obj = new ResponseObj();

        if (regId == null)
        {
            message = "Registration ID not provided for the device.";
            log.LogInformation("Registration ID : NULL");
            fail = true;
        }
        else
        {
            string[] hubs = data?.linkedHubs.ToObject<string[]>();

            // Must have hubs selected on the enrollment
            if (hubs == null)
            {
                message = "No hub group defined for the enrollment.";
                log.LogInformation("linkedHubs : NULL");
                fail = true;
            }
            else
            {
                // This is a Contoso Toaster Model 007
                if (regId.Contains("-contoso-tstrsd-007"))
                {
                    //Find the "-toasters-" IoT hub configured on the enrollment
                    foreach(string hubString in hubs)
                    {
                        if (hubString.Contains("-toasters-"))
                            obj.iotHubHostName = hubString;
                    }

                    if (obj.iotHubHostName == null)
                    {
                        message = "No toasters hub found for the enrollment.";
                        log.LogInformation(message);
                        fail = true;
                    }
                    else
                    {
                        // Specify the initial tags for the device.
                        TwinCollection tags = new TwinCollection();
                        tags["deviceType"] = "toaster";

                        // Specify the initial desired properties for the device.
                        TwinCollection properties = new TwinCollection();
                        properties["state"] = "ready";
                        properties["darknessSetting"] = "medium";

                        // Add the initial twin state to the response.
                        TwinState twinState = new TwinState(tags, properties);
                        obj.initialTwin = twinState;
                    }
                }
                // This is a Contoso Heat pump Model 008
                else if (regId.Contains("-contoso-hpsd-088"))
                {
                    //Find the "-heatpumps-" IoT hub configured on the enrollment
                    foreach(string hubString in hubs)
                    {
                        if (hubString.Contains("-heatpumps-"))
                            obj.iotHubHostName = hubString;
                    }

                    if (obj.iotHubHostName == null)
                    {
                        message = "No heat pumps hub found for the enrollment.";
                        log.LogInformation(message);
                        fail = true;
                    }
                    else
                    {
                        // Specify the initial tags for the device.
                        TwinCollection tags = new TwinCollection();
                        tags["deviceType"] = "heatpump";

                        // Specify the initial desired properties for the device.
                        TwinCollection properties = new TwinCollection();
                        properties["state"] = "on";
                        properties["temperatureSetting"] = "65";

                        // Add the initial twin state to the response.
                        TwinState twinState = new TwinState(tags, properties);
                        obj.initialTwin = twinState;
                    }
                }
                // Unrecognized device.
                else
                {
                    fail = true;
                    message = "Unrecognized device registration.";
                    log.LogInformation("Unknown device registration");
                }
            }
        }

        log.LogInformation("\nResponse");
        log.LogInformation((obj.iotHubHostName != null) ? JsonConvert.SerializeObject(obj) : message);

        return (fail)
            ? new BadRequestObjectResult(message) 
            : (ActionResult)new OkObjectResult(obj);
    }

    public class ResponseObj
    {
        public string iotHubHostName {get; set;}
        public TwinState initialTwin {get; set;}
    }
    ```

## <a name="create-the-enrollment"></a>Создание регистрации

In this section, you'll create a new enrollment group that uses the custom allocation policy. Для удобства в этой статье используется [аттестация симметричного ключа](concepts-symmetric-key-attestation.md) с регистрацией. В качестве более безопасного решения рекомендуется использовать [аттестацию сертификатов X.509](concepts-security.md#x509-certificates) с цепочкой доверия.

1. Still on the [Azure portal](https://portal.azure.com), open your provisioning service.

2. Select **Manage enrollments** on the left pane, and then select the **Add enrollment group** button at the top of the page.

3. On **Add Enrollment Group**, enter the following information, and select the **Save** button.

    **Имя группы**: введите **contoso-custom-allocated-devices**.

    **Тип аттестации**: выберите **Симметричный ключ**.

    **Автоматически создавать ключи**: этот флажок должен быть уже установлен.

    **Выберите способ назначения устройств для Центров**: выберите **Custom (Use Azure Function)** (Пользовательский (функция Azure)).

    ![Добавление группы регистрации пользовательского выделения для аттестации симметричного ключа](./media/how-to-use-custom-allocation-policies/create-custom-allocation-enrollment.png)

4. On **Add Enrollment Group**, select **Link a new IoT hub** to link both of your new divisional IoT hubs.

    Execute this step for both of your divisional IoT hubs.

    **Подписка**: если у вас несколько подписок, выберите подписку, в которой вы создали Центры Интернета вещей для отделов.

    **Центр Интернета вещей**: выберите один из созданных центров отделов.

    **Политика доступа**: выберите **iothubowner**.

    ![Связывание Центров Интернета вещей отделов со службой подготовки](./media/how-to-use-custom-allocation-policies/link-divisional-hubs.png)

5. После связывания Центров Интернета вещей обоих отделов необходимо на странице **Добавление группы регистрации** выбрать их в качестве группы Центров Интернета вещей для группы регистрации, как показано ниже.

    ![Создание группы центров отделов для регистрации](./media/how-to-use-custom-allocation-policies/enrollment-divisional-hub-group.png)

6. On **Add Enrollment Group**, scroll down to the **Select Azure Function** section, select the Function app you created in the previous section. Then select the function you created and select Save to save the enrollment group.

    ![Select the function and save the enrollment group](./media/how-to-use-custom-allocation-policies/save-enrollment.png)

7. После сохранения регистрации снова откройте ее и запомните или запишите **первичный ключ**. Для формирования ключей необходимо сначала сохранить регистрацию. Этот ключ будет позднее использоваться для создания уникальных ключей устройства для имитированных устройств.

## <a name="derive-unique-device-keys"></a>Получение производных уникальных ключей

In this section, you create two unique device keys. Один ключ будет использоваться для имитированного устройства toaster. Другой ключ будет использоваться для имитированного устройства heat pump.

To generate the device key, you use the **Primary Key** you noted earlier to compute the [HMAC-SHA256](https://wikipedia.org/wiki/HMAC) of the device registration ID for each device and convert the result into Base64 format. Дополнительные сведения о создании производных ключей устройств с группами регистраций см. в разделе о регистрации групп в статье [Аттестация симметричного ключа](concepts-symmetric-key-attestation.md).

Для примера в этой статье используйте следующие два идентификатора регистрации устройств и вычислите ключи для обоих устройств. Оба идентификатора регистрации содержат допустимый суффикс для работы с примером кода для пользовательской политики выделения:

* **breakroom499-contoso-tstrsd-007**
* **mainbuilding167-contoso-hpsd-088**

### <a name="linux-workstations"></a>Рабочие станции Linux

If you're using a Linux workstation, you can use openssl to generate your derived device keys as shown in the following example.

1. Замените значение **KEY** значением **первичного ключа**, записанным ранее.

    ```bash
    KEY=oiK77Oy7rBw8YB6IS6ukRChAw+Yq6GC61RMrPLSTiOOtdI+XDu0LmLuNm11p+qv2I+adqGUdZHm46zXAQdZoOA==

    REG_ID1=breakroom499-contoso-tstrsd-007
    REG_ID2=mainbuilding167-contoso-hpsd-088

    keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
    devkey1=$(echo -n $REG_ID1 | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64)
    devkey2=$(echo -n $REG_ID2 | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64)

    echo -e $"\n\n$REG_ID1 : $devkey1\n$REG_ID2 : $devkey2\n\n"
    ```

    ```bash
    breakroom499-contoso-tstrsd-007 : JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=
    mainbuilding167-contoso-hpsd-088 : 6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=
    ```

### <a name="windows-based-workstations"></a>Рабочие станции на основе Windows

If you're using a Windows-based workstation, you can use PowerShell to generate your derived device key as shown in the following example.

1. Замените значение **KEY** значением **первичного ключа**, записанным ранее.

    ```powershell
    $KEY='oiK77Oy7rBw8YB6IS6ukRChAw+Yq6GC61RMrPLSTiOOtdI+XDu0LmLuNm11p+qv2I+adqGUdZHm46zXAQdZoOA=='

    $REG_ID1='breakroom499-contoso-tstrsd-007'
    $REG_ID2='mainbuilding167-contoso-hpsd-088'

    $hmacsha256 = New-Object System.Security.Cryptography.HMACSHA256
    $hmacsha256.key = [Convert]::FromBase64String($key)
    $sig1 = $hmacsha256.ComputeHash([Text.Encoding]::ASCII.GetBytes($REG_ID1))
    $sig2 = $hmacsha256.ComputeHash([Text.Encoding]::ASCII.GetBytes($REG_ID2))
    $derivedkey1 = [Convert]::ToBase64String($sig1)
    $derivedkey2 = [Convert]::ToBase64String($sig2)

    echo "`n`n$REG_ID1 : $derivedkey1`n$REG_ID2 : $derivedkey2`n`n"
    ```

    ```powershell
    breakroom499-contoso-tstrsd-007 : JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=
    mainbuilding167-contoso-hpsd-088 : 6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=
    ```

Имитированные устройства будут использовать производные ключи устройств с соответствующим идентификатором регистрации для выполнения аттестации симметричного ключа.

## <a name="prepare-an-azure-iot-c-sdk-development-environment"></a>Подготовка среды разработки для пакета SDK Azure IoT для C

In this section, you prepare the development environment used to build the [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c). В пакет SDK входит пример кода для имитированного устройства. Для этого имитированного устройства будет выполнена попытка подготовки во время последовательности загрузки.

В этом разделе описывается использование рабочей станции Windows. Пример для Linux приведен в инструкциях по настройке виртуальных машин в статье [Подготовка к мультитенантности](how-to-provision-multitenant.md).

1. Скачайте [систему сборки CMake](https://cmake.org/download/).

    **Перед** установкой `CMake` важно установить на компьютер необходимые компоненты Visual Studio (Visual Studio и рабочую нагрузку "Разработка классических приложений на C++"). После установки компонентов и проверки загрузки установите систему сборки CMake.

2. Откройте командную строку или оболочку Git Bash. Выполните следующую команду для клонирования репозитория GitHub пакета SDK Azure IoT для C:

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive
    ```

    Выполнение этой операции может занять несколько минут.

3. Создайте подкаталог `cmake` в корневом каталоге репозитория Git и перейдите в эту папку. 

    ```cmd/sh
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

4. Выполните приведенную ниже команду, чтобы создать версию пакета SDK для используемой клиентской платформы разработки. Эта команда также создает решение Visual Studio для имитированного устройства в каталоге `cmake`. 

    ```cmd
    cmake -Dhsm_type_symm_key:BOOL=ON -Duse_prov_client:BOOL=ON  ..
    ```

    If `cmake` doesn't find your C++ compiler, you might get build errors while running the command. If that happens, try running the command in the [Visual Studio command prompt](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs).

    После успешного создания последние несколько строк выходных данных будут выглядеть следующим образом:

    ```cmd/sh
    $ cmake -Dhsm_type_symm_key:BOOL=ON -Duse_prov_client:BOOL=ON  ..
    -- Building for: Visual Studio 15 2017
    -- Selecting Windows SDK version 10.0.16299.0 to target Windows 10.0.17134.
    -- The C compiler identification is MSVC 19.12.25835.0
    -- The CXX compiler identification is MSVC 19.12.25835.0

    ...

    -- Configuring done
    -- Generating done
    -- Build files have been written to: E:/IoT Testing/azure-iot-sdk-c/cmake
    ```

## <a name="simulate-the-devices"></a>Имитация устройств

In this section, you update a provisioning sample named **prov\_dev\_client\_sample** located in the Azure IoT C SDK you set up previously.

Пример кода имитирует последовательность загрузки устройства, которое отправляет запрос на подготовку в экземпляр службы подготовки устройств. При выполнении последовательности загрузки устройство toaster будет распознано и назначено Центру Интернета вещей с помощью пользовательской политики выделения.

1. На портале Azure выберите вкладку **Обзор** службы подготовки устройств и запишите значение **_области идентификатора_** .

    ![Извлеките сведения о конечной точке службы подготовки устройств из колонки на портале](./media/quick-create-simulated-device-x509/extract-dps-endpoints.png) 

2. В Visual Studio откройте файл решения **azure_iot_sdks.sln**, который был создан ранее в результате запуска CMake. Файл решения должен находиться в следующем расположении:

    ```
    azure-iot-sdk-c\cmake\azure_iot_sdks.sln
    ```

3. В окне *Обозреватель решений* Visual Studio перейдите в папку **Provision\_Samples**. Разверните пример проекта с именем **prov\_dev\_client\_sample**. Разверните **исходные файлы** и откройте **prov\_dev\_client\_sample.c**.

4. Найдите константу `id_scope` и замените ее значение ранее скопированным значением **области идентификатора**. 

    ```c
    static const char* id_scope = "0ne00002193";
    ```

5. Найдите определение функции `main()` в том же файле. Проверьте, чтобы переменной `hsm_type` было задано значение `SECURE_DEVICE_TYPE_SYMMETRIC_KEY`, как показано ниже.

    ```c
    SECURE_DEVICE_TYPE hsm_type;
    //hsm_type = SECURE_DEVICE_TYPE_TPM;
    //hsm_type = SECURE_DEVICE_TYPE_X509;
    hsm_type = SECURE_DEVICE_TYPE_SYMMETRIC_KEY;
    ```

6. Щелкните проект **prov\_dev\_client\_sample** правой кнопкой мыши и выберите пункт **Назначить запускаемым проектом**.

### <a name="simulate-the-contoso-toaster-device"></a>Имитация устройства toaster Contoso

1. Для имитации устройства тостера найдите закомментированный вызов `prov_dev_set_symmetric_key_info()` в **prov\_dev\_client\_sample.c**.

    ```c
    // Set the symmetric key if using they auth type
    //prov_dev_set_symmetric_key_info("<symm_registration_id>", "<symmetric_Key>");
    ```

    Uncomment the function call and replace the placeholder values (including the angle brackets) with the toaster registration ID and derived device key you generated previously. Значение ключа **JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=** , показанное ниже, приведено только в качестве примера.

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("breakroom499-contoso-tstrsd-007", "JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=");
    ```

    Сохраните файл.

2. В меню Visual Studio выберите **Отладка** > **Запуск без отладки**, чтобы запустить решение. In the prompt to rebuild the project, select **Yes**, to rebuild the project before running.

    The following output is an example of the simulated toaster device successfully booting up and connecting to the provisioning service instance to be assigned to the toasters IoT hub by the custom allocation policy:

    ```cmd
    Provisioning API Version: 1.3.6

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: contoso-toasters-hub-1098.azure-devices.net, deviceId: breakroom499-contoso-tstrsd-007

    Press enter key to exit:
    ```

### <a name="simulate-the-contoso-heat-pump-device"></a>Имитация устройства heat pump Contoso

1. Для имитации устройства теплового насоса обновите вызов `prov_dev_set_symmetric_key_info()` в **prov\_dev\_client\_sample.c**, указав идентификатор регистрации теплового насоса и производный ключ устройства, созданный ранее. Значение ключа **6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=** , показанное ниже, приведено только в качестве примера.

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("mainbuilding167-contoso-hpsd-088", "6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=");
    ```

    Сохраните файл.

2. В меню Visual Studio выберите **Отладка** > **Запуск без отладки**, чтобы запустить решение. In the prompt to rebuild the project, select **Yes** to rebuild the project before running.

    The following output is an example of the simulated heat pump device successfully booting up and connecting to the provisioning service instance to be assigned to the Contoso heat pumps IoT hub by the custom allocation policy:

    ```cmd
    Provisioning API Version: 1.3.6

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: contoso-heatpumps-hub-1098.azure-devices.net, deviceId: mainbuilding167-contoso-hpsd-088

    Press enter key to exit:
    ```

## <a name="troubleshooting-custom-allocation-policies"></a>Устранение неполадок пользовательских политик выделения

The following table shows expected scenarios and the results error codes you might receive. Используйте эту таблицу при устранении неполадок в работе пользовательских политик выделения с функциями Azure.

| Сценарий | Результат регистрации из службы подготовки | Результаты пакета SDK для подготовки |
| -------- | --------------------------------------------- | ------------------------ |
| Веб-перехватчик возвращает ответ "200 ОК" со свойством iotHubHostName, которому присвоено допустимое имя узла Центра Интернета вещей | Состояние результата: "Назначено"  | Пакет SDK возвращает PROV_DEVICE_RESULT_OK, а также сведения о центре |
| Веб-перехватчик возвращает ответ "200 ОК" со свойством iotHubHostName, но его значение является пустой строкой или NULL | Состояние результата: "Сбой"<br><br> Код ошибки: CustomAllocationIotHubNotSpecified (400208) | Пакет SDK возвращает PROV_DEVICE_RESULT_HUB_NOT_SPECIFIED |
| Веб-перехватчик возвращает ответ "401 — недостаточно прав" | Состояние результата: "Сбой"<br><br>Код ошибки: CustomAllocationUnauthorizedAccess (400209) | Пакет SDK возвращает PROV_DEVICE_RESULT_UNAUTHORIZED |
| Была создана индивидуальная регистрация для отключения устройства | Состояние результата: "Отключено" | Пакет SDK возвращает PROV_DEVICE_RESULT_DISABLED |
| Веб-перехватчик возвращает код ошибки больше или равный 429 | Попытка повторной оркестрации DPS будет выполнена несколько раз. Текущее состояние политики повтора:<br><br>&nbsp;&nbsp; счетчик попыток: 10;<br>&nbsp;&nbsp; исходный интервал: 1 с;<br>&nbsp;&nbsp; шаг увеличения: 9 с. | Пакет SDK пропустит ошибку и отправит еще одно сообщение для получения состояния в указанное время. |
| Веб-перехватчик возвращает любой другой код состояния | Состояние результата: "Сбой"<br><br>Код ошибки: CustomAllocationFailed (400207) | Пакет SDK возвращает PROV_DEVICE_RESULT_DEV_AUTH_ERROR |

## <a name="clean-up-resources"></a>Очистка ресурсов

If you plan to continue working with the resources created in this article, you can leave them. If you don't plan to continue using the resources, use the following steps to delete all of the resources created in this article to avoid unnecessary charges.

Здесь предполагается, что вы создали все используемые в этой статье ресурсы, как было указано, в одной группе ресурсов, **contoso-us-resource-group**.

> [!IMPORTANT]
> Удаление группы ресурсов — процесс необратимый. Группа ресурсов и все содержащиеся в ней ресурсы удаляются без возможности восстановления. Будьте внимательны, чтобы случайно не удалить не ту группу ресурсов или не те ресурсы. Если вы создали Центр Интернета вещей в группе ресурсов, содержащей ресурсы, которые нужно сохранить, удалите только ресурс Центра Интернета вещей, не удаляя всю группу ресурсов.
>

Удаление группы ресурсов по имени.

1. Войдите в портал [Azure](https://portal.azure.com) и выберите**Группы ресурсов**.

2. Введите в текстовое поле **Фильтровать по имени...** имя вашей группы ресурсов: **contoso-us-resource-group**. 

3. Справа от своей группы ресурсов в списке результатов щелкните **...** , а затем выберите **Удалить группу ресурсов**.

4. You'll be asked to confirm the deletion of the resource group. Снова введите имя группы ресурсов, которую необходимо удалить, и щелкните **Удалить**. Через некоторое время группа ресурсов и все ее ресурсы будут удалены.

## <a name="next-steps"></a>Дальнейшие действия

* To learn more Reprovisioning, see [IoT Hub Device reprovisioning concepts](concepts-device-reprovision.md) 
* To learn more Deprovisioning, see [How to deprovision devices that were previously autoprovisioned](how-to-unprovision-devices.md) 
