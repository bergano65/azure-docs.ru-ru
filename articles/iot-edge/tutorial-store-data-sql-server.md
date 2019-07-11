---
title: Руководство по хранению данных с помощью модуля SQL — Azure IoT Edge | Документация Майкрософт
description: Узнайте, как хранить данные локально на устройстве IoT Edge, используя модуль SQL Server
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/28/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: b77b44856e9623235051470bc087885765ee12c9
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67080435"
---
# <a name="tutorial-store-data-at-the-edge-with-sql-server-databases"></a>Руководство. Хранение данных в граничной системе с помощью баз данных SQL Server

Развертывание модуля SQL Server для хранения данных на устройстве с Linux, где используется Azure IoT Edge.

Используйте Azure IoT Edge и SQL Server для хранения и запроса данных в пограничной системе. Azure IoT Edge предоставляет базовые функции хранения для кэширования сообщений при переходе устройства в автономный режим и последующей их переадресацией при повторном подключении. Однако вам могут потребоваться дополнительные возможности хранения данных, например возможность запрашивать данные локально. С помощью локальных баз данных устройства IoT Edge могут выполнять более сложные вычисления без необходимости поддерживать соединение с Центром Интернета вещей. 

В этой статье содержатся сведения для развертывания базы данных SQL Server на устройство IoT Edge. Функции Azure, запущенные на устройстве IoT Edge, структурируют входящие данные, а затем отправляют их в базу данных. Шаги, описанные в этой статье, также можно применить к другим базам данных, которые работают в контейнерах, например MySQL или PostgreSQL.

Из этого руководства вы узнаете, как выполнять следующие задачи: 

> [!div class="checklist"]
> * Использование Visual Studio Code для создания функции Azure
> * Развертывание базы данных SQL на устройстве IoT Edge
> * Использование кода Visual Studio для создания модулей и их развертывание на устройстве IoT Edge
> * Просмотр сформированных данных

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Предварительные требования

Предполагается, что перед началом работы с этим руководством вы прошли предыдущее, в рамках которого настроили окружение для разработки контейнеров Linux: [Tutorial: Develop IoT Edge modules for Linux devices](tutorial-develop-for-linux.md) (Руководство. Разработка модулей IoT Edge для устройств с Linux). После работы с ним у вас должны быть готовы все необходимые компоненты: 

* [Центр Интернета вещей](../iot-hub/iot-hub-create-through-portal.md) ценовой категории "Бесплатный" или "Стандартный" в Azure.
* [устройство Linux, на котором выполняется Azure IoT Edge](quickstart-linux.md);
* реестр контейнеров, например [Реестр контейнеров Azure](https://docs.microsoft.com/azure/container-registry/);
* средство [Visual Studio Code](https://code.visualstudio.com/), настроенное с помощью [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools);
* выпуск [Docker CE](https://docs.docker.com/install/), настроенный для выполнения контейнеров Linux.

Для отправки данных в SQL Server в рамках этого руководства используется модуль Функций Azure. Для разработки модуля IoT Edge с использованием Функций Azure установите на компьютере разработки следующие дополнительные компоненты: 

* [Расширение C# для Visual Studio Code (на платформе OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) для Visual Studio Code. 
* [Пакет SDK для .NET Core 2.1](https://www.microsoft.com/net/download). 

## <a name="create-a-function-project"></a>Создание проекта функции

Чтобы отправить данные в базу данных, необходим модуль, который может правильно структурировать данные, а затем сохранить их в таблице. 

### <a name="create-a-new-project"></a>Создание нового проекта

Описанный ниже процесс позволяет создать функцию IoT Edge с помощью Visual Studio Code и средств Azure IoT.

1. Откройте Visual Studio Code.

2. Выберите **Представление** > **Палитра команд** для открытия палитры команд VS Code.

3. В палитре команд введите и выполните команду **Azure IoT Edge: New IoT Edge Solution** (Azure IoT Edge: создать решение IoT Edge). В палитре команд укажите следующие сведения для создания решения: 

   | Поле | Значение |
   | ----- | ----- |
   | Выбор папки | Выберите расположение на компьютере разработчика для VS Code, чтобы создать файлы решения. |
   | Введите название решения. | Введите описательное имя для решения, например **SqlSolution**, или примите значение по умолчанию. |
   | Выбор шаблона модуля | Выберите **Функции Azure — C#** . |
   | Указание имени модуля | Назовите модуль **sqlFunction**. |
   | Указание репозитория изображений Docker для модуля | Репозиторий изображений включает в себя имя реестра контейнеров и имя образа контейнера. Образ контейнера предварительно заполняется на последнем шаге. Замените **localhost:5000** на значение сервера входа из реестра контейнеров Azure. Вы можете извлечь сервер входа на странице "Обзор" реестра контейнеров на портале Azure. <br><br>Последняя строка выглядит следующим образом: \<имя реестра\>.azurecr.io/sqlfunction. |

   Окно VS Code загружает рабочую область решения IoT Edge. 
   
### <a name="add-your-registry-credentials"></a>Добавление учетных данных реестра

Файл среды хранит учетные данные для реестра контейнеров и совместно использует их со средой выполнения IoT Edge. Среде выполнения нужны эти учетные данные, чтобы извлечь частный образ на устройство IoT Edge.

1. Откройте в обозревателе VS Code файл с расширением ENV.
2. Обновите поля с **именем пользователя** и **паролем**, скопированные из своего реестра контейнера Azure.
3. Сохраните этот файл.

### <a name="select-your-target-architecture"></a>Выбор целевой архитектуры

Сейчас Visual Studio Code позволяет разрабатывать модули C для устройств Linux AMD64 и Linux ARM32v7. Для каждого решения вам нужно выбрать одну целевую платформу, так как сборка и запуск контейнера для разных архитектур различается. По умолчанию используется Linux AMD64. 

1. Откройте палитру команд и выполните поиск **Azure IoT Edge: Set Default Target Platform for Edge Solution** (Azure IoT Edge: установить целевую платформу по умолчанию для решения Edge) или выберите значок ярлыка на боковой панели в нижней части окна. 

2. В палитре команд выберите целевую архитектуру из списка параметров. В рамках этого руководства мы используем в качестве устройства IoT Edge виртуальную машину Ubuntu. Поэтому сохраним значение по умолчанию **amd64**. 

### <a name="update-the-module-with-custom-code"></a>Обновление модуля с помощью пользовательского кода

1. В обозревателе VS Code откройте **Модули** > **sqlFunction** > **sqlFunction.cs**.

2. Замените все содержимое файла следующим кодом:

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

3. В строке 35 замените строку **\<sql connection string\>** следующей строкой. Свойство **Источник данных** ссылается на контейнер SQL Server, который еще не создан, но будет создан с именем **SQL** в следующем разделе. 

   ```csharp
   Data Source=tcp:sql,1433;Initial Catalog=MeasurementsDB;User Id=SA;Password=Strong!Passw0rd;TrustServerCertificate=False;Connection Timeout=30;
   ```

4. Сохраните файл **sqlFunction.cs**. 

5. Откройте файл **sqlFunction.csproj**.

6. Найдите группу ссылок на пакеты и добавьте новую для SqlClient. 

   ```csproj
   <PackageReference Include="System.Data.SqlClient" Version="4.5.1"/>
   ```

7. Сохраните файл **sqlFunction.csproj**.

## <a name="add-the-sql-server-container"></a>Добавление контейнера SQL Server

[Манифест развертывания](module-composition.md) объявляет модули, которые среда выполнения IoT Edge установит на вашем устройстве IoT Edge. Вы добавили код для создания настраиваемого модуля службы "Функции" в предыдущем разделе, но модуль SQL Server уже создан и доступен в Azure Marketplace. Необходимо сообщить среде выполнения IoT Edge о том, что его нужно включить и настроить на устройстве. 

1. В Visual Studio Code откройте палитру команд, выбрав **Представление** > **Палитра команд**.

2. В палитре команд введите и выполните команду **Azure IoT Edge: Add IoT Edge Module** (Azure IoT Edge: добавить модуль IoT Edge). В палитре команд укажите следующую информацию для добавления нового модуля: 

   | Поле | Значение | 
   | ----- | ----- |
   | Выбор файла шаблона развертывания | Палитра команд выделяет файл deployment.template.json в текущей папке решения. Выберите этот файл.  |
   | Выбор шаблона модуля | Выберите **Module from Azure Marketplace** (Модуль в Azure Marketplace). |

3. В окне Azure IoT Edge Module Marketplace найдите и выберите **Модуль SQL Server**. 

4. Измените имя модуля на **sql** (полностью прописными буквами). Это имя совпадает с именем контейнера, объявленного в строке подключения в файле sqlFunction.cs. 

5. Выберите команду **Импорт**, чтобы добавить модуль в свое решение. 

6. Откройте файл **deployment.template.json** в папке решения. 

7. Перейдите в раздел **Модули**. Вы должны увидеть три модуля. Модуль *tempSensor* по умолчанию добавляется в новые решения и предоставляет тестовые данные для использования с другими модулями. Модуль *sqlFunction* создается в самом начале, и в него добавляется новый код. Наконец, модуль *sql* был импортирован из Azure Marketplace. 

   >[!Tip]
   >Модуль SQL Server поставляется с паролем по умолчанию, заданным в переменных среды манифеста развертывания. Каждый раз при создании контейнера SQL Server в рабочей среде нужно [изменять стандартный пароль системного администратора](https://docs.microsoft.com/sql/linux/quickstart-install-connect-docker).

8. Закройте файл **deployment.template.json**.

## <a name="build-your-iot-edge-solution"></a>Сборка решения IoT Edge

В предыдущих разделах было создано решение с одним модулем, а затем добавлено другое в шаблон манифеста развертывания. Модуль SQL Server размещается корпорацией Майкрософт в открытом доступе, но чтобы им воспользоваться, нужно поместить код в модуле функции. В этом разделе выполняется сборка решения, создание образов контейнеров для модуля sqlFunction и отправка образа в реестр контейнеров. 

1. В Visual Studio Code откройте интегрированный терминал, последовательно выбрав элементы **Вид** > **Терминал**.  

1. Войдите в реестр контейнеров в Visual Studio Code, чтобы вы могли отправить свои образы в реестр. Используйте те же учетные данные Реестра контейнеров Azure, добавленные в файле ENV. Введите следующую команду в окне интегрированного терминала:

    ```csh/sh
    docker login -u <ACR username> -p <ACR password> <ACR login server>
    ```
    
    На экране может отобразиться предупреждение системы безопасности с рекомендацией использовать параметр password-stdin. Хотя его использование выходит за рамки данной статьи, мы рекомендуем следовать данной рекомендации. Чтобы получить дополнительные сведения, ознакомьтесь с описанием команды [docker login](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin). 

2. В обозревателе VS Code щелкните правой кнопкой мыши файл **deployment.template.json** и выберите **Build and Push IoT Edge solution** (Создать и отправить решение IoT Edge). 

После указания службе Visual Studio Code создать решение, сначала она запишет данные в шаблон развертывания, а затем в новой папке **config** создаст файл deployment.json. Затем выполняются две команды в интегрированном терминале: `docker build` и `docker push`. Они создают код, упаковывают модуль в контейнер и отправляют его в реестр контейнеров, который был указан при инициализации решения. 

Вы можете убедиться, что модуль sqlFunction был успешно отправлен в реестр контейнеров. На портале Azure перейдите в реестр контейнеров. Выберите **Репозитории** и выполните поиск по имени **sqlFunction**. Другие два модуля, tempSensor и sql, не помещены в реестр контейнеров, так как вы уже использовали ссылки на их репозитории в реестрах Microsoft.

## <a name="deploy-the-solution-to-a-device"></a>Развертывание решения на устройстве

Модули на устройстве можно задать с помощью Центра Интернета вещей, однако доступ к Центру Интернета вещей и устройствам можно также получить через Visual Studio Code. В этом разделе настраивается доступ к Центру Интернета вещей. Используйте VS Code для развертывания решения на устройстве IoT Edge. 

1. В обозревателе VS Code разверните раздел **Azure IoT Hub Devices** (Устройства Центра Интернета вещей Azure). 

2. Щелкните правой кнопкой мыши имя устройства, которое необходимо сделать целевым объектом развертывания, и выберите **Create Deployment for IoT Edge** (Создание развертывания для устройств IoT Edge). 

3. В проводнике файлов перейдите к папке **config** в решении и выберите **deployment.amd64**. Щелкните **Select Edge Deployment Manifest** (Выбрать манифест развертывания Edge). 

   Не используйте файл deployment.template.json в качестве манифеста развертывания.

Если развертывание завершено успешно, сообщение о подтверждении будет выведено в выходных данных VS Code. 

Обновите состояние вашего устройства в разделе Visual Studio Code "Устройства Центра Интернета вещей Azure". Новые модули указаны, и через несколько минут после установки и запуска контейнеров они начнут потоковое выполнение. Кроме того, можно проверить, все ли модули отображаются и запущены на своем устройстве. На своем устройстве IoT Edge выполните следующую команду, чтобы увидеть состояние модулей. 

   ```cmd/sh
   iotedge list
   ```

## <a name="create-the-sql-database"></a>Создание базы данных SQL

При применении манифеста развертывания на устройстве вы получаете три выполняемых модуля. Модуль tempSensor создает данные имитируемой среды. Модуль sqlFunction принимает данные и форматирует их для базы данных. В этом разделе показано, как настроить базу данных SQL для хранения данных температуры, полученных с датчиков. 

Выполните следующие команды на устройстве IoT Edge. Эти команды подключаются к модулю **sql**, запущенному на устройстве, и который создает базу данных и таблицу для хранения отправляемых ему данных температуры. 

1. В программе командной строки устройства IoT Edge подключитесь к базе данных. 
      ```bash
      sudo docker exec -it sql bash
      ```

2. Откройте программу командной строки для SQL.
      ```bash
      /opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P 'Strong!Passw0rd'
      ```

3. Создайте базу данных: 
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


## <a name="next-steps"></a>Дополнительная информация

В этом руководстве вы создали модуль службы "Функции Azure", который содержит код для фильтрации необработанных данных, созданных вашим устройством IoT Edge. Когда вы будете готовы создавать собственные модули, вы можете узнать больше о том, как [разрабатывать решения службы "Функции Azure" с помощью Azure IoT Edge для Visual Studio Code](how-to-develop-csharp-function.md). 

Если вы хотите применить другой метод хранения данных на пограничных устройствах, прочитайте о том, как использовать хранилище BLOB-объектов Azure в IoT Edge. 

> [!div class="nextstepaction"]
> [Хранение данных на пограничных устройствах с использованием хранилища BLOB-объектов Azure в IoT Edge](how-to-store-data-blob.md)
