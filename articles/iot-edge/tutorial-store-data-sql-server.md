---
title: Руководство по хранению данных с помощью модуля SQL — Azure IoT Edge | Документация Майкрософт
description: Узнайте, как хранить данные локально на устройстве IoT Edge, используя модуль SQL Server
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/04/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: 426e4fe05890f1669859545db3d731943a12428a
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/14/2019
ms.locfileid: "54260181"
---
# <a name="tutorial-store-data-at-the-edge-with-sql-server-databases"></a>Руководство. Хранение данных в граничной системе с помощью баз данных SQL Server

Используйте Azure IoT Edge и SQL Server для хранения и запроса данных в пограничной системе. Azure IoT Edge предоставляет базовые функции хранения для кэширования сообщений при переходе устройства в автономный режим и последующей их переадресацией при повторном подключении. Однако вам могут потребоваться дополнительные возможности хранения данных, например возможность запрашивать данные локально. При использовании локальных баз данных устройства IoT Edge могут выполнять более сложные вычисления без необходимости поддерживать соединение с Центром Интернета вещей. Например, датчик на компьютере отправляет данные в облако раз в месяц для отчетности и оптимизации модуля машинного обучения. При этом данные датчика за последние несколько дней доступны для пользователей на локальном компьютере.

В этой статье содержатся сведения для развертывания базы данных SQL Server на устройство IoT Edge. Функции Azure, запущенные на устройстве IoT Edge, структурируют входящие данные, а затем отправляют их в базу данных. Шаги, описанные в этой статье, также можно применить к другим базам данных, которые работают в контейнерах, например MySQL или PostgreSQL.

Из этого руководства вы узнаете, как выполнять следующие задачи: 

> [!div class="checklist"]
> * Использование Visual Studio Code для создания функции Azure
> * Развертывание базы данных SQL на устройстве IoT Edge
> * Использование кода Visual Studio для создания модулей и их развертывание на устройстве IoT Edge
> * Просмотр сформированных данных

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Предварительные требования

Устройство Azure IoT Edge.

* В качестве устройства Azure IoT Edge можно использовать компьютер, на котором ведется разработка, или виртуальную машину. Для этого выполните действия, описанные в кратком руководстве для устройств [Linux](quickstart-linux.md) или [Windows](quickstart.md). 

Облачные ресурсы.

* [Центр Интернета вещей](../iot-hub/iot-hub-create-through-portal.md) ценовой категории "Бесплатный" или "Стандартный" в Azure. 

Ресурсы разработки.

* [Visual Studio Code](https://code.visualstudio.com/). 
* [Расширение C# для Visual Studio Code (на платформе OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) для Visual Studio Code. 
* [Средства Azure IoT для Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge). 
* [Пакет SDK для .NET Core 2.1](https://www.microsoft.com/net/download). 
* [Docker CE](https://docs.docker.com/install/). 

## <a name="create-a-container-registry"></a>Создание реестра контейнеров

В этом руководстве описано, как с помощью средств Azure IoT для Visual Studio Code создать модуль и **образ контейнера** из файлов. Затем вы отправите этот образ в **реестр**, содержащий ваши образы и управляющий ими. Наконец, вы развернете свой образ из реестра для выполнения на устройстве IoT Edge.  

Для хранения образов контейнеров можно использовать любые реестры, совместимые с Docker. Две популярные службы реестров Docker — [Реестр контейнеров Azure](https://docs.microsoft.com/azure/container-registry/) и [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags). В этом руководстве используется реестр контейнеров Azure. 

Если вы еще не создали реестр контейнеров, выполните эти действия, чтобы создать реестр в Azure:

1. На [портале Azure](https://portal.azure.com) выберите **Создать ресурс** > **Контейнеры** > **Реестр контейнеров**.

2. Предоставьте следующие значения для создания реестра контейнеров:

   | Поле | Значение | 
   | ----- | ----- |
   | Имя реестра | Укажите уникальное имя. |
   | Подписка | Из раскрывающегося списка выберите подписку. |
   | Группа ресурсов | Мы рекомендуем использовать одну группу ресурсов для всех тестовых ресурсов, которые вы создаете во время работы с краткими руководствами по IoT Edge. Например, **IoTEdgeResources**. |
   | Расположение | Выберите расположение рядом с вами. |
   | Пользователь-администратор | Установите значение **Включить**. |
   | SKU | Выберите **Базовый**. | 

5. Нажмите кнопку **Создать**.

6. После создания реестра контейнеров перейдите к нему, а затем выберите **Ключи доступа**. 

7. Скопируйте значения **Сервер входа**, **Имя пользователя** и **Пароль**. Они потребуются позже в этом руководстве для предоставления доступа к реестру контейнеров.  

## <a name="create-a-function-project"></a>Создание проекта функции

Чтобы отправить данные в базу данных, необходим модуль, который может правильно структурировать данные, а затем сохранить их в таблице. 

Описанный ниже процесс позволяет создать функцию IoT Edge с помощью Visual Studio Code и средств Azure IoT.

1. Откройте Visual Studio Code.

2. Выберите **Представление** > **Палитра команд** для открытия палитры команд VS Code.

3. В палитре команд введите и выполните команду **Azure IoT Edge: New IoT Edge Solution** (Azure IoT Edge: создать решение IoT Edge). В палитре команд укажите следующие сведения для создания решения: 

   | Поле | Значение |
   | ----- | ----- |
   | Выбор папки | Выберите расположение на компьютере разработчика для VS Code, чтобы создать файлы решения. |
   | Введите название решения. | Введите описательное имя для решения, например **SqlSolution**, или примите значение по умолчанию. |
   | Выбор шаблона модуля | Выберите **Функции Azure — C#**. |
   | Указание имени модуля | Назовите модуль **sqlFunction**. |
   | Указание репозитория изображений Docker для модуля | Репозиторий изображений включает в себя имя реестра контейнеров и имя образа контейнера. Образ контейнера предварительно заполняется на последнем шаге. Замените **localhost:5000** на значение сервера входа из реестра контейнеров Azure. Вы можете извлечь сервер входа на странице "Обзор" реестра контейнеров на портале Azure. Последняя строка выглядит следующим образом: \<имя реестра\>.azurecr.io/sqlFunction. |

   Окно VS Code загружает рабочую область решения IoT Edge. 
   
4. Откройте ENV-файл с расширением в рабочей области решения IoT Edge\. 

   При создании решения IoT Edge VS Code предлагает ввести учетные данные реестра в файл с расширением \.env. Этот файл игнорируется Git. Расширение IoT Edge использует его позже для предоставления устройству IoT Edge доступа к реестру. 

   У вас не будет ENV-файла, если вы не предоставили реестр контейнеров на предыдущем шаге, но приняли по умолчанию localhost:5000\.

5. В ENV-файле предоставьте среде выполнения IoT Edge учетные данные реестра, чтобы она могла получить доступ к образам модулей. Найдите переменные **CONTAINER_REGISTRY_USERNAME** и **CONTAINER_REGISTRY_PASSWORD** и вставьте свои учетные данные после знака равенства: 

   ```env
   CONTAINER_REGISTRY_USERNAME_yourregistry=<username>
   CONTAINER_REGISTRY_PASSWORD_yourregistry=<password>
   ```

6. Сохраните ENV-файл.

7. В обозревателе VS Code откройте **Модули** > **sqlFunction** > **sqlFunction.cs**.

8. Замените содержимое файла на код, приведенный ниже.

   ```csharp
   using System;
   using System.Collections.Generic;
   using System.IO;
   using System.Text;
   using System.Threading.Tasks;
   using Microsoft.Azure.Devices.Client;
   using Microsoft.Azure.WebJobs;
   using Microsoft.Azure.WebJobs.Extensions.EdgeHub;
   using Microsoft.Azure.WebJobs.Host;
   using Microsoft.Extensions.Logging;
   using Newtonsoft.Json;
   using Sql = System.Data.SqlClient;

   namespace Functions.Samples
   {
       public static class sqlFunction
       {
           [FunctionName("sqlFunction")]
           public static async Task FilterMessageAndSendMessage(
               [EdgeHubTrigger("input1")] Message messageReceived,
               [EdgeHub(OutputName = "output1")] IAsyncCollector<Message> output,
               ILogger logger)
           {
               const int temperatureThreshold = 20;
               byte[] messageBytes = messageReceived.GetBytes();
               var messageString = System.Text.Encoding.UTF8.GetString(messageBytes);

               if (!string.IsNullOrEmpty(messageString))
               {
                   logger.LogInformation("Info: Received one non-empty message");
                   // Get the body of the message and deserialize it.
                   var messageBody = JsonConvert.DeserializeObject<MessageBody>(messageString);

                   //Store the data in SQL db
                   const string str = "<sql connection string>";
                   using (Sql.SqlConnection conn = new Sql.SqlConnection(str))
                   {
                       conn.Open();
                       var insertMachineTemperature = "INSERT INTO MeasurementsDB.dbo.TemperatureMeasurements VALUES (CONVERT(DATETIME2,'" + messageBody.timeCreated + "', 127), 'machine', " + messageBody.machine.temperature + ");";
                       var insertAmbientTemperature = "INSERT INTO MeasurementsDB.dbo.TemperatureMeasurements VALUES (CONVERT(DATETIME2,'" + messageBody.timeCreated + "', 127), 'ambient', " + messageBody.ambient.temperature + ");"; 
                       using (Sql.SqlCommand cmd = new Sql.SqlCommand(insertMachineTemperature + "\n" + insertAmbientTemperature, conn))
                       {
                           //Execute the command and log the # rows affected.
                           var rows = await cmd.ExecuteNonQueryAsync();
                           logger.LogInformation($"{rows} rows were updated");
                       }
                   }

                   if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
                   {
                       // Send the message to the output as the temperature value is greater than the threashold.
                       var filteredMessage = new Message(messageBytes);
                       // Copy the properties of the original message into the new Message object.
                       foreach (KeyValuePair<string, string> prop in messageReceived.Properties)
                       {filteredMessage.Properties.Add(prop.Key, prop.Value);}
                       // Add a new property to the message to indicate it is an alert.
                       filteredMessage.Properties.Add("MessageType", "Alert");
                       // Send the message.       
                       await output.AddAsync(filteredMessage);
                       logger.LogInformation("Info: Received and transferred a message with temperature above the threshold");
                   }
               }
           }
       }
       //Define the expected schema for the body of incoming messages.
       class MessageBody
       {
           public Machine machine {get; set;}
           public Ambient ambient {get; set;}
           public string timeCreated {get; set;}
       }
       class Machine
       {
           public double temperature {get; set;}
           public double pressure {get; set;}         
       }
       class Ambient
       {
           public double temperature {get; set;}
           public int humidity {get; set;}         
       }
   }
   ```

6. В строке 35 замените строку **\<sql connection string\>** следующей строкой. Свойство **Data Source** ссылается на имя контейнера SQL Server, который будет создан с именем **SQL** в следующем разделе. 

   ```csharp
   Data Source=tcp:sql,1433;Initial Catalog=MeasurementsDB;User Id=SA;Password=Strong!Passw0rd;TrustServerCertificate=False;Connection Timeout=30;
   ```

7. Сохраните файл **sqlFunction.cs**. 

8. Откройте файл **sqlFunction.csproj**.

9. Найдите группу ссылок на пакеты и добавьте новую для SqlClient. 

   ```csproj
   <PackageReference Include="System.Data.SqlClient" Version="4.5.1"/>
   ```

10. Сохраните файл **sqlFunction.csproj**.

## <a name="add-a-sql-server-container"></a>Добавление контейнера SQL Server

[Манифест развертывания](module-composition.md) объявляет модули, которые среда выполнения IoT Edge установит на вашем устройстве IoT Edge. Вы добавили код для создания настраиваемого модуля службы "Функции" в предыдущем разделе, но модуль SQL Server уже создан. Необходимо сообщить среде выполнения IoT Edge о том, что его нужно включить и настроить на устройстве. 

1. В обозревателе кода Visual Studio откройте файл **deployment.template.json**. 

2. Перейдите в раздел **Модули**. Должно быть перечислено два модуля: **tempSensor**, создающий имитируемые данные, и модуль **sqlFunction**.

3. Если вы используете контейнеры Windows, измените раздел **sqlFunction.settings.image**.

   ```json
   "image": "${MODULES.sqlFunction.windows-amd64}"
   ```

4. Добавьте приведенный ниже код для объявления третьего модуля. Добавьте запятую после раздела sqlFunction и вставьте следующий код:

   ```json
   "sql": {
     "version": "1.0",
     "type": "docker",
     "status": "running",
     "restartPolicy": "always",
     "env":{},
     "settings": {
       "image": "",
       "createOptions": ""
     }
   }
   ```

   ![Добавление модуля SQL Server в манифест](./media/tutorial-store-data-sql-server/view_json_sql.png)

5. В зависимости от типа контейнеров Docker на устройстве IoT Edge обновите параметры модуля **sql**, используя следующий код:
   * Контейнеры Windows:

      ```json
      "env": {
        "ACCEPT_EULA": {"value": "Y"},
        "SA_PASSWORD": {"value": "Strong!Passw0rd"}
      },
      "settings": {
        "image": "microsoft/mssql-server-windows-developer",
        "createOptions": {
          "HostConfig": {
            "Mounts": [{"Target": "C:\\mssql","Source": "sqlVolume","Type": "volume"}],
            "PortBindings": {
              "1433/tcp": [{"HostPort": "1401"}]
            }
          }
        }
      }
      ```

   * Контейнеры Linux:

      ```json
      "env": {
        "ACCEPT_EULA": {"value": "Y"},
        "SA_PASSWORD": {"value": "Strong!Passw0rd"}
      },
      "settings": {
        "image": "mcr.microsoft.com/mssql/server:latest",
        "createOptions": {
          "HostConfig": {
            "Mounts": [{"Target": "/var/opt/mssql","Source": "sqlVolume","Type": "volume"}],
            "PortBindings": {
              "1433/tcp": [{"HostPort": "1401"}]
            }
          }
        }
      }
      ```

   >[!Tip]
   >Каждый раз при создании контейнера SQL Server в рабочей среде нужно [изменять стандартный пароль системного администратора](https://docs.microsoft.com/sql/linux/quickstart-install-connect-docker#change-the-sa-password).

6. Сохраните файл **deployment.template.json**.

## <a name="build-your-iot-edge-solution"></a>Сборка решения IoT Edge

В предыдущих разделах было создано решение с одним модулем, а затем добавлено другое в шаблон манифеста развертывания. Теперь необходимо собрать решение, создать образы контейнеров для модулей и отправить образы в реестр контейнеров. 

1. Войдите в реестр контейнеров в Visual Studio Code, чтобы вы могли отправить свои образы в реестр. Используйте те же учетные данные, которые вы добавили в ENV-файл. Введите следующую команду в окне интегрированного терминала:

    ```csh/sh
    docker login -u <ACR username> <ACR login server>
    ```
    
    Появится запрос на ввод пароля. Вставьте пароль в командную строку (он будет скрыт в целях безопасности) и нажмите клавишу **ВВОД**. 

    ```csh/sh
    Password: <paste in the ACR password and press enter>
    Login Succeeded
    ```

2. В обозревателе VS Code щелкните правой кнопкой мыши файл **deployment.template.json** и выберите **Build and Push IoT Edge solution** (Создать и отправить решение IoT Edge). 

После указания службе Visual Studio Code создать решение, сначала она запишет данные в шаблон развертывания, а затем в новой папке **config** создаст файл deployment.json. Затем выполняются две команды в интегрированном терминале: `docker build` и `docker push`. Они создают код, упаковывают модуль в контейнер и отправляют его в реестр контейнеров, который был указан при инициализации решения. 

## <a name="deploy-the-solution-to-a-device"></a>Развертывание решения на устройстве

Модули на устройстве можно задать с помощью Центра Интернета вещей, однако доступ к Центру Интернета вещей и устройствам можно также получить через Visual Studio Code. В этом разделе настраивается доступ к Центру Интернета вещей. Используйте VS Code для развертывания решения на устройстве IoT Edge. 

1. В палитре команд VS Code выберите **Azure IoT Hub: Select IoT Hub** (Azure IoT Hub: выбрать Центр Интернета вещей).

2. Следуйте указаниям по входу в учетную запись Azure. 

3. В палитре команд выберите подписку Azure, а затем выберите свой Центр Интернета вещей. 

4. В обозревателе VS Code разверните раздел **Azure IoT Hub Devices** (Устройства Центра Интернета вещей Azure). 

5. Щелкните правой кнопкой мыши имя устройства, которое необходимо сделать целевым объектом развертывания, и выберите **Create Deployment for IoT Edge** (Создание развертывания для устройств IoT Edge). 

   ![Создание развертывания для одного устройства](./media/tutorial-store-data-sql-server/create-deployment.png)

6. В проводнике файлов перейдите к папке **config** в решении и выберите **deployment.amd64**. Щелкните **Select Edge Deployment Manifest** (Выбрать манифест развертывания Edge). 

Если развертывание завершено успешно, сообщение о подтверждении будет выведено в выходных данных VS Code. 

Обновите состояние вашего устройства в разделе Visual Studio Code "Устройства Центра Интернета вещей Azure". Новые модули указаны, и через несколько минут после установки и запуска контейнеров они начнут потоковое выполнение. Кроме того, можно проверить, все ли модули отображаются и запущены на своем устройстве. На своем устройстве IoT Edge выполните следующую команду, чтобы увидеть состояние модулей. 

   ```cmd/sh
   iotedge list
   ```

## <a name="create-the-sql-database"></a>Создание базы данных SQL

При применении манифеста развертывания на устройстве вы получаете три выполняемых модуля. Модуль tempSensor создает данные имитируемой среды. Модуль sqlFunction принимает данные и форматирует их для базы данных. В этом разделе показано, как настроить базу данных SQL для хранения данных температуры, полученных с датчиков. 

Выполните следующие команды на устройстве IoT Edge. Эти команды подключаются к модулю **sql**, запущенному на устройстве, и который создает базу данных и таблицу для хранения отправляемых ему данных температуры. 

1. В программе командной строки устройства IoT Edge подключитесь к базе данных. 
   * Контейнер Windows:
   
      ```cmd
      docker exec -it sql cmd
      ```
    
   * Контейнер Linux: 

      ```bash
      sudo docker exec -it sql bash
      ```

2. Откройте программу командной строки для SQL.
   * Контейнер Windows:

      ```cmd
      sqlcmd -S localhost -U SA -P "Strong!Passw0rd"
      ```

   * Контейнер Linux: 

      ```bash
      /opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P 'Strong!Passw0rd'
      ```

3. Создайте базу данных: 

   * Контейнер Windows
      ```sql
      CREATE DATABASE MeasurementsDB
      ON
      (NAME = MeasurementsDB, FILENAME = 'C:\mssql\measurementsdb.mdf')
      GO
      ```

   * Контейнер Linux
      ```sql
      CREATE DATABASE MeasurementsDB
      ON
      (NAME = MeasurementsDB, FILENAME = '/var/opt/mssql/measurementsdb.mdf')
      GO
      ```

4. Определите таблицу.

   ```sql
   CREATE TABLE MeasurementsDB.dbo.TemperatureMeasurements (measurementTime DATETIME2, location NVARCHAR(50), temperature FLOAT)
   GO
   ```

Можно настроить файл Docker SQL Server таким образом, чтобы SQL Server автоматически настраивался для развертывания на нескольких устройствах IoT Edge. Дополнительные сведения см. в статье о [демонстрационном проекте контейнера Microsoft SQL Server](https://github.com/twright-msft/mssql-node-docker-demo-app). 

## <a name="view-the-local-data"></a>Просмотр локальных данных

После создания таблицы модуль sqlFunction начинает сохранять данные в локальную базу данных SQL Server 2017 на устройстве IoT Edge. 

Изнутри программы командной строки SQL выполните следующую команду, чтобы просмотреть данные отформатированной таблицы: 

   ```sql
   SELECT * FROM MeasurementsDB.dbo.TemperatureMeasurements
   GO
   ```

   ![Просмотр содержимого локальной базы данных](./media/tutorial-store-data-sql-server/view-data.png)



## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы планируете перейти к следующей рекомендуемой статье, можно сохранить созданные и повторно используемые ресурсы и конфигурации. Это же устройство IoT Edge также можно использовать в качестве тестового устройства. 

В противном случае чтобы избежать расходов, можно удалить локальные конфигурации и ресурсы Azure, созданные в рамках этой статьи. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

[!INCLUDE [iot-edge-clean-up-local-resources](../../includes/iot-edge-clean-up-local-resources.md)]



## <a name="next-steps"></a>Дополнительная информация

В этом руководстве вы создали модуль службы "Функции Azure", который содержит код для фильтрации необработанных данных, созданных вашим устройством IoT Edge. Когда вы будете готовы создавать собственные модули, вы можете узнать больше о том, как [разрабатывать решения службы "Функции Azure" с помощью Azure IoT Edge для Visual Studio Code](how-to-develop-csharp-function.md). 

Перейдите к следующим руководствам, чтобы узнать о других способах, с помощью которых Azure IoT Edge может помочь вам превратить данные в бизнес-аналитику на пограничном устройстве.

> [!div class="nextstepaction"]
> [Фильтрация данных датчика, с помощью кода C#](tutorial-csharp-module.md)
