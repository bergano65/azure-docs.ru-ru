---
title: Политики пользовательского распределения с службой обеспечения устройств Концентратора Azure
description: Как использовать политики распределения пользовательских устройств с помощью службы обеспечения устройств устройств Azure IoT (DPS)
author: wesmc7777
ms.author: wesmc
ms.date: 11/14/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 87ffca1957d4ec449753f1966ed05cf3948f5ca2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75453942"
---
# <a name="how-to-use-custom-allocation-policies"></a>Как использовать пользовательские политики выделения

Пользовательская политика выделения обеспечивает больший контроль над назначением устройств в Центре Интернета вещей. Это достигается с помощью пользовательского кода в [функции Azure](../azure-functions/functions-overview.md), используемого для назначения устройств Центру Интернета вещей. Служба подготовки устройств вызывает код функции Azure, предоставляя всю необходимую информацию об устройстве и регистрации. Код функции выполняется и возвращает данные Центра Интернета вещей, используемые при подготовке устройства.

Используя политики распределения пользовательских устройств, вы определяете свои собственные политики распределения, когда политики, предоставляемые Службой обеспечения устройств, не отвечают требованиям вашего сценария.

Например, возможно, вы хотите изучить сертификат, который использует устройство во время подготовки, и назначить устройство кузню IoT на основе свойства сертификата. Или, может быть, у вас есть информация, хранящаяся в базе данных для ваших устройств и необходимо запросить базу данных, чтобы определить, какой концентратор IoT должно быть назначено.

В этой статье показана работа с пользовательскими политиками с помощью кода функции Azure на языке C#. Создаются два новых Центра Интернета вещей, представляющие *отдел тостеров Contoso* и *отдел тепловых насосов Contoso*. Устройства, запрашивающие подготовку, должны иметь идентификатор регистрации с одним из следующих суффиксов, чтобы они были приняты для подготовки:

* **-contoso-tstrsd-007**: отдел тостеров Contoso;
* **-contoso-hpsd-088**: отдел тепловых насосов Contoso.

Устройства будут подготовлены в соответствии с этими обязательными суффиксами в идентификаторе регистрации. Эти устройства будут имитироваться с помощью примера для подготовки из [пакета SDK Azure IoT для C](https://github.com/Azure/azure-iot-sdk-c).

Вы выполняете следующие шаги в этой статье:

* Использование Azure CLI для создания центров Интернета вещей для двух отделов Contoso (**отдела тостеров Contoso** и **отдела тепловых насосов Contoso**).
* Создание группы регистрации с помощью функции Azure для пользовательской политики выделения.
* Создание ключей устройства для имитации двух устройств.
* Подготовка среды разработки для пакета SDK Azure IoT для C.
* Имитировать устройства и убедиться, что они подготовлены в соответствии с примером кода в политике пользовательского распределения

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Предварительные требования

Приведенные ниже предварительные требования касаются среды разработки Windows. При использовании Linux или macOS ознакомьтесь с соответствующим разделом в статье [Подготовка среды разработки](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) из документации к пакету SDK.

* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) с включенной рабочей нагрузкой [Разработка классических приложений на C++](https://docs.microsoft.com/cpp/?view=vs-2019#pivot=workloads). Visual Studio 2015 или Visual Studio 2017 также поддерживаются.

* Установите последнюю версию [Git](https://git-scm.com/download/).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-the-provisioning-service-and-two-divisional-iot-hubs"></a>Создание службы обеспечения и двух отделочных центров IoT

В этом разделе используется облачная оболочка Azure для создания службы обеспечения и двух узлов IoT, представляющих **подразделение Contoso Toasters** и **подразделение Contoso Heat Pumps.**

> [!TIP]
> Команды, используемые в этой статье, создают службу обеспечения и другие ресурсы в западной части США. Мы рекомендуем вам создавать ресурсы в ближайшем к вам регионе, которые поддерживают службу обеспечения устройств. Чтобы просмотреть список доступных расположений, выполните команду `az provider show --namespace Microsoft.Devices --query "resourceTypes[?resourceType=='ProvisioningServices'].locations | [0]" --out table`, или перейдите на страницу [Состояние Azure](https://azure.microsoft.com/status/) и введите в строке поиска "Служба подготовки устройств". В командах места могут быть указаны либо в одном слове, либо в формате с несколькими словами; например: Westus, Западная США, WEST США и т.д. Значение не является чувствительным к делу. Если для указания расположения вы используете несколько слов, укажите значение поиска в кавычках, например `-- location "West US"`.
>

1. В Azure Cloud Shell создайте группу ресурсов с помощью команды [az group create](/cli/azure/group#az-group-create). Группа ресурсов Azure является логическим контейнером, в котором происходит развертывание ресурсов Azure и управление ими.

    Следующий пример создает группу ресурсов, названную *контозо-нас-ресурс-группой* в регионе *Westus.* Рекомендуется использовать эту группу для всех ресурсов, созданных в рамках этой статьи. Этот подход упростит очистку после завершения.

    ```azurecli-interactive 
    az group create --name contoso-us-resource-group --location westus
    ```

2. Используйте облачную оболочку Azure для создания службы подготовки устройств с [помощью команды az iot dps.](/cli/azure/iot/dps#az-iot-dps-create) Услуга обеспечения будет добавлена в *контозо-нас-ресурс-группу*.

    Следующий пример создает службу обеспечения, названную *contoso-provisioning-service-1098* в *западном* месте. Вы должны использовать уникальное имя службы. Составьте свой собственный суффикс в названии службы вместо **1098**.

    ```azurecli-interactive 
    az iot dps create --name contoso-provisioning-service-1098 --resource-group contoso-us-resource-group --location westus
    ```

    Выполнение этой команды может занять несколько минут.

3. В Azure Cloud Shell создайте Центр Интернета вещей **отдела тостеров Contoso** с помощью команды [az iot hub create](/cli/azure/iot/hub#az-iot-hub-create). Этот Центр Интернета вещей будет добавлен в группу *contoso-us-resource-group*.

    Следующий пример создает концентратор IoT под названием *contoso-toasters-hub-1098* в месте *westus.* Вы должны использовать уникальное название концентратора. Придумайте собственный суффикс в имени центра вместо **1098**. В примере кода для пользовательской политики выделения требуется наличие слова `-toasters-` в имени центра.

    ```azurecli-interactive 
    az iot hub create --name contoso-toasters-hub-1098 --resource-group contoso-us-resource-group --location westus --sku S1
    ```

    Выполнение этой команды может занять несколько минут.

4. В Azure Cloud Shell создайте Центр Интернета вещей **отдела тепловых насосов Contoso** с помощью команды [az iot hub create](/cli/azure/iot/hub#az-iot-hub-create). Этот Центр Интернета вещей также будет добавлен в группу *contoso-us-resource-group*.

    Следующий пример создает концентратор IoT под названием *contoso-heatpumps-hub-1098* в *западном* месте. Вы должны использовать уникальное название концентратора. Придумайте собственный суффикс в имени центра вместо **1098**. В примере кода для пользовательской политики выделения требуется наличие слова `-heatpumps-` в имени центра.

    ```azurecli-interactive 
    az iot hub create --name contoso-heatpumps-hub-1098 --resource-group contoso-us-resource-group --location westus --sku S1
    ```

    Выполнение этой команды может занять несколько минут.

## <a name="create-the-custom-allocation-function"></a>Создание функции пользовательского распределения

В этом разделе создается функция Azure, которая реализует политику пользовательского распределения. Эта функция определяет, какой дивизион IoT-концентратор устройства должен быть зарегистрирован на основе того, его идентификатор регистрации содержит **строку-contoso-tstrsd-007** или **-contoso-hpsd-088**. Он также устанавливает исходное состояние близнеца устройства в зависимости от того, является ли устройство тостером или тепловым насосом.

1. Войдите на [портал Azure](https://portal.azure.com). На **своей**домашней странице выберите ресурс .

2. В поле поиска *Marketplace* введите "Функциональное приложение". Из выпадающего списка выберите **функциональное приложение,** а затем выберите **Создать**.

3. На странице **создания функции App** под вкладкой **Basics** введите следующие настройки для нового приложения функции и выберите **Обзор :**

    **Группа ресурсов**: Выберите **контозо-нас-ресурс-группу,** чтобы сохранить все ресурсы, созданные в этой статье вместе.

    **Имя приложения функции**: Введите уникальное имя приложения функции. В этом примере используется **contoso-функция-приложение-1098**.

    **Опубликовать**: Проверить, что **код** выбран.

    **Runtime Stack**: Выберите **ядро .NET** из выпадения вниз.

    **Регион**: Выберите тот же регион, что и группа ресурсов. Этот пример использует **Запад США**.

    > [!NOTE]
    > По умолчанию включено приложение Insights. Анализ приложений не является необходимым для этой статьи, но это может помочь вам понять и исследовать любые проблемы, с которыми вы сталкиваетесь с пользовательским распределением. Если вы предпочитаете, вы можете отключить Application Insights, выбрав вкладку **Мониторинга,** а затем выбрав **«Нет** для **анализов приложений включить».**

    ![Создание приложения функции Azure для размещения функции распределения пользовательских способов](./media/how-to-use-custom-allocation-policies/create-function-app.png)

4. На странице **Резюме** выберите **Создать** для создания приложения функции. Развертывание может занять несколько минут. Когда он завершится, выберите **Перейти к ресурсу**.

5. На левой панели страницы функции приложения **+** **Обзор,** выберите рядом **с функциями,** чтобы добавить новую функцию.

    ![Добавление функции в функциональное приложение](./media/how-to-use-custom-allocation-policies/create-function.png)

6. На **Azure Functions для .NET - начало** страницы, для шага **CHOOSE A DEPLOYMENT ENVIRONMENT,** выберите **в-портал** плитку, затем выберите **Продолжить**.

    ![Выберите среду разработки портала](./media/how-to-use-custom-allocation-policies/function-choose-environment.png)

7. На следующей странице для шага **CREATE A FUNCTION** выберите плитку **Webhook и API,** а затем выберите **«Создать».** Создается функция под названием **HttpTrigger1,** и портал отображает содержимое файла кода **run.csx.**

8. Справка требует Nuget пакеты. Для создания исходного двойника устройства функция пользовательского распределения использует классы, которые определяются в двух пакетах Nuget, которые должны быть загружены в среду хостинга. С функциями Azure пакеты Nuget ссылаются с помощью файла *function.host.* На этом этапе вы сохраняете и загружаете файл *function.host.*

    1. Копируйте следующие строки в свой любимый редактор и сохраните файл на компьютере в качестве *функции.*

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

    2. На функции **HttpTrigger1** расширьте вкладку **«Файлы представления»** на правой стороне окна.

        ![Файлы открытого представления](./media/how-to-use-custom-allocation-policies/function-open-view-files.png)

    3. Выберите **Upload,** просмотрите файл **function.proj** и выберите **Open** для загрузки файла.

        ![Выберите файл загрузки](./media/how-to-use-custom-allocation-policies/function-choose-upload-file.png)

9. Замените код для функции **HttpTrigger1** следующим кодом и выберите **Сохранить:**

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

В этом разделе вы создадите новую группу регистрации, используюую политику пользовательского распределения. Для удобства в этой статье используется [аттестация симметричного ключа](concepts-symmetric-key-attestation.md) с регистрацией. В качестве более безопасного решения рекомендуется использовать [аттестацию сертификатов X.509](concepts-security.md#x509-certificates) с цепочкой доверия.

1. Все еще на [портале Azure](https://portal.azure.com), откройте свой сервис подготовки.

2. Выберите **Управление регистрацией** на левом стеле, а затем выберите кнопку **группы добавления** в верхней части страницы.

3. При **добавленной группе регистрации**введите следующую информацию и выберите кнопку **«Сохранить».**

    **Имя группы**: введите **contoso-custom-allocated-devices**.

    **Тип аттестации**: выберите **Симметричный ключ**.

    **Автоматически создавать ключи**: этот флажок должен быть уже установлен.

    **Выберите способ назначения устройств для Центров**: выберите **Custom (Use Azure Function)** (Пользовательский (функция Azure)).

    ![Добавление группы регистрации пользовательского выделения для аттестации симметричного ключа](./media/how-to-use-custom-allocation-policies/create-custom-allocation-enrollment.png)

4. На **добавленной группе регистрации**выберите **ссылку на новый концентратор IoT,** чтобы связать оба новых концентрата IoT.

    Выполните этот шаг для обоих ваших отделочных концентраторов IoT.

    **Подписка**: если у вас несколько подписок, выберите подписку, в которой вы создали Центры Интернета вещей для отделов.

    **Центр Интернета вещей**: выберите один из созданных центров отделов.

    **Политика доступа**: выберите **iothubowner**.

    ![Связывание Центров Интернета вещей отделов со службой подготовки](./media/how-to-use-custom-allocation-policies/link-divisional-hubs.png)

5. После связывания Центров Интернета вещей обоих отделов необходимо на странице **Добавление группы регистрации** выбрать их в качестве группы Центров Интернета вещей для группы регистрации, как показано ниже.

    ![Создание группы центров отделов для регистрации](./media/how-to-use-custom-allocation-policies/enrollment-divisional-hub-group.png)

6. При **добавленной группе регистрации**прокрутите вниз в раздел **«Функция выбора azure»** выберите приложение «Функция, созданное в предыдущем разделе». Затем выберите созданную функцию и выберите Сохранить, чтобы сохранить группу регистрации.

    ![Выберите функцию и сохраните группу регистрации](./media/how-to-use-custom-allocation-policies/save-enrollment.png)

7. После сохранения регистрации снова откройте ее и запомните или запишите **первичный ключ**. Для формирования ключей необходимо сначала сохранить регистрацию. Этот ключ будет позднее использоваться для создания уникальных ключей устройства для имитированных устройств.

## <a name="derive-unique-device-keys"></a>Получение производных уникальных ключей

В этом разделе вы создаете два уникальных ключа устройства. Один ключ будет использоваться для имитированного устройства toaster. Другой ключ будет использоваться для имитированного устройства heat pump.

Для создания ключа устройства используется **основной ключ,** который вы отметили ранее, для вычисления идентификатора регистрации [hMAC-SHA256](https://wikipedia.org/wiki/HMAC) для каждого устройства и преобразования результата в формат Base64. Дополнительные сведения о создании производных ключей устройств с группами регистраций см. в разделе о регистрации групп в статье [Аттестация симметричного ключа](concepts-symmetric-key-attestation.md).

Для примера в этой статье используйте следующие два идентификатора регистрации устройств и вычислите ключи для обоих устройств. Оба идентификатора регистрации содержат допустимый суффикс для работы с примером кода для пользовательской политики выделения:

* **breakroom499-contoso-tstrsd-007**
* **mainbuilding167-contoso-hpsd-088**

### <a name="linux-workstations"></a>Рабочие станции Linux

Если вы используете рабочую станцию Linux, вы можете использовать openssl для генерации ключей производных устройств, как показано в следующем примере.

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

### <a name="windows-based-workstations"></a>Рабочие станции для Windows

Если вы используете рабочую станцию на базе Windows, вы можете использовать PowerShell для генерации ключа производных устройств, как показано в следующем примере.

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

## <a name="prepare-an-azure-iot-c-sdk-development-environment"></a>Подготовка среды разработки для пакета SDK Azure IoT для C

В этом разделе вы подготовьте среду разработки, используемую для создания [Azure IoT C SDK.](https://github.com/Azure/azure-iot-sdk-c) В пакет SDK входит пример кода для имитированного устройства. Для этого имитированного устройства будет выполнена попытка подготовки во время последовательности загрузки.

В этом разделе описывается использование рабочей станции Windows. Пример для Linux приведен в инструкциях по настройке виртуальных машин в статье [Подготовка к мультитенантности](how-to-provision-multitenant.md).

1. Скачайте [систему сборки CMake](https://cmake.org/download/).

    **Перед** установкой `CMake` очень важно установить на компьютер необходимые компоненты Visual Studio (Visual Studio с рабочей нагрузкой "Разработка классических приложений на C++"). После установки компонентов и проверки загрузки установите систему сборки CMake.

2. Найдите имя тега для [последнего выпуска](https://github.com/Azure/azure-iot-sdk-c/releases/latest) пакета SDK.

3. Откройте командную строку или оболочку Git Bash. Выполните приведенные ниже команды, чтобы клонировать репозиторий GitHub с последним выпуском [пакета SDK Azure IoT для C](https://github.com/Azure/azure-iot-sdk-c). Используйте найденный тег в качестве значения для параметра `-b`:

    ```cmd/sh
    git clone -b <release-tag> https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Выполнение этой операции может занять несколько минут.

4. Создайте подкаталог `cmake` в корневом каталоге репозитория Git и перейдите в эту папку. В каталоге `azure-iot-sdk-c` выполните следующие команды:

    ```cmd/sh
    mkdir cmake
    cd cmake
    ```

5. Выполните приведенную ниже команду, чтобы создать версию пакета SDK для используемой клиентской платформы разработки. Эта команда также создает решение Visual Studio для имитированного устройства в каталоге `cmake`. 

    ```cmd
    cmake -Dhsm_type_symm_key:BOOL=ON -Duse_prov_client:BOOL=ON  ..
    ```

    Если `cmake` не найдется компилятор C', вы можете получить ошибки сборки во время выполнения команды. Если это произойдет, попробуйте запустить команду в [команде Visual Studio.](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs)

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

В этом разделе вы обновляете образец подготовки под названием **prov\_\_dev client,\_** расположенный в SDK Azure IoT C, который вы создали ранее.

Пример кода имитирует последовательность загрузки устройства, которое отправляет запрос на подготовку в экземпляр службы подготовки устройств. При выполнении последовательности загрузки устройство toaster будет распознано и назначено Центру Интернета вещей с помощью пользовательской политики выделения.

1. На портале Azure выберите вкладку **Обзор** службы подготовки устройств и запишите значение **_области идентификатора_**.

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

    Не комментируйте вызов функции и замените значения заполнителя (включая угловые скобки) идентификатором регистрации тостера и ключом производного устройства, который вы создали ранее. Значение ключа **JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=**, показанное ниже, приведено только в качестве примера.

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("breakroom499-contoso-tstrsd-007", "JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=");
    ```

    Сохраните файл.

2. В меню Visual Studio выберите **Отек** > **Start без отладки** для запуска решения. В запросе на восстановление проекта выберите **Yes,** чтобы восстановить проект перед запуском.

    Следующим выводом является пример успешной загрузки и подключения с моделируемого тостерного устройства к экземпляру службы обеспечения, который будет назначен центру тостеров IoT по политике пользовательского распределения:

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

1. Для имитации устройства теплового насоса обновите вызов `prov_dev_set_symmetric_key_info()` в **prov\_dev\_client\_sample.c**, указав идентификатор регистрации теплового насоса и производный ключ устройства, созданный ранее. Значение ключа **6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=**, показанное ниже, приведено только в качестве примера.

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("mainbuilding167-contoso-hpsd-088", "6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=");
    ```

    Сохраните файл.

2. В меню Visual Studio выберите **Отек** > **Start без отладки** для запуска решения. При появлении запроса перестроить проект щелкните **Да**, чтобы перестроить его перед запуском.

    Следующим результатом является пример смоделированной тепловой насосной устройства, успешно загружающего и подключаемого к экземпляру службы обеспечения, который будет назначен к концентратору IoT-центра Contoso по политике специального распределения:

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

В следующей таблице показаны ожидаемые сценарии и коды ошибок результатов, которые вы можете получить. Используйте эту таблицу при устранении неполадок в работе пользовательских политик выделения с функциями Azure.

| Сценарий | Результат регистрации из службы подготовки | Результаты пакета SDK для подготовки |
| -------- | --------------------------------------------- | ------------------------ |
| Веб-перехватчик возвращает ответ "200 ОК" со свойством iotHubHostName, которому присвоено допустимое имя узла Центра Интернета вещей | Состояние результата: "Назначено"  | Пакет SDK возвращает PROV_DEVICE_RESULT_OK, а также сведения о центре |
| Веб-перехватчик возвращает ответ "200 ОК" со свойством iotHubHostName, но его значение является пустой строкой или NULL | Состояние результата: "Сбой"<br><br> Код ошибки: CustomAllocationIotHubNotSpecified (400208) | Пакет SDK возвращает PROV_DEVICE_RESULT_HUB_NOT_SPECIFIED |
| Веб-перехватчик возвращает ответ "401 — недостаточно прав" | Состояние результата: "Сбой"<br><br>Код ошибки: CustomAllocationUnauthorizedAccess (400209) | Пакет SDK возвращает PROV_DEVICE_RESULT_UNAUTHORIZED |
| Была создана индивидуальная регистрация для отключения устройства | Состояние результата: "Отключено" | Пакет SDK возвращает PROV_DEVICE_RESULT_DISABLED |
| Веб-перехватчик возвращает код ошибки больше или равный 429 | Попытка повторной оркестрации DPS будет выполнена несколько раз. Текущее состояние политики повтора:<br><br>&nbsp;&nbsp; счетчик попыток: 10;<br>&nbsp;&nbsp; исходный интервал: 1 с;<br>&nbsp;&nbsp; шаг увеличения: 9 с. | Пакет SDK пропустит ошибку и отправит еще одно сообщение для получения состояния в указанное время. |
| Веб-перехватчик возвращает любой другой код состояния | Состояние результата: "Сбой"<br><br>Код ошибки: CustomAllocationFailed (400207) | Пакет SDK возвращает PROV_DEVICE_RESULT_DEV_AUTH_ERROR |

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы планируете продолжить работу с ресурсами, созданными в этой статье, вы можете оставить их. Если вы не планируете продолжать использовать ресурсы, используйте следующие шаги, чтобы удалить все ресурсы, созданные в этой статье, чтобы избежать ненужных платежей.

Здесь предполагается, что вы создали все используемые в этой статье ресурсы, как было указано, в одной группе ресурсов, **contoso-us-resource-group**.

> [!IMPORTANT]
> Удаление группы ресурсов — процесс необратимый. Группа ресурсов и все содержащиеся в ней ресурсы удаляются без возможности восстановления. Будьте внимательны, чтобы случайно не удалить не ту группу ресурсов или не те ресурсы. Если вы создали Центр Интернета вещей в группе ресурсов, содержащей ресурсы, которые нужно сохранить, удалите только ресурс Центра Интернета вещей, не удаляя всю группу ресурсов.
>

Удаление группы ресурсов по имени.

1. Вопийте на [портале Azure](https://portal.azure.com) и выберите **группы ресурсов.**

2. Введите в текстовое поле **Фильтровать по имени...** имя вашей группы ресурсов: **contoso-us-resource-group**. 

3. Справа от своей группы ресурсов в списке результатов щелкните **...**, а затем выберите **Удалить группу ресурсов**.

4. Вам будет предложено подтвердить удаление группы ресурсов. Снова введите имя группы ресурсов, которую необходимо удалить, и щелкните **Удалить**. Через некоторое время группа ресурсов и все ее ресурсы будут удалены.

## <a name="next-steps"></a>Дальнейшие действия

* Чтобы узнать больше о [IoT Hub Device reprovisioning concepts](concepts-device-reprovision.md) переподготовке, см. 
* Чтобы узнать больше деобеспечения, [см. Как дезавуировать устройства, которые ранее были автопродезированы](how-to-unprovision-devices.md) 
