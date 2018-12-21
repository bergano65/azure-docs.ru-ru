---
title: Написание пользовательской функции C# для задания Edge в Azure Stream Analytics с помощью Visual Studio (предварительная версия)
description: Вы узнаете, как писать пользовательские функции на C# для заданий Edge в Stream Analytics с помощью Visual Studio.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: tutorial
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: 5597109a65a8af88bf286977d039656635565ed9
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53086915"
---
# <a name="tutorial-write-a-c-user-defined-function-for-azure-stream-analytics-edge-job-preview"></a>Руководство. Написание определяемой пользователем функции на C# для задания Azure Stream Analytics на IoT Edge (предварительная версия)

Пользовательские функции C#, создаваемые в Visual Studio, позволяют расширить язык запросов Azure Stream Analytics за счет собственных функций. Вы можете повторно использовать уже существующий код (включая DLL), а также математическую или комплексную логику с C#. Существует три способа реализации пользовательских функций: Файлы кода программной части в проекте Stream Analytics, пользовательские функции из локального проекта C# и пользовательские функции из существующего пакета в учетной записи хранения. В этом руководстве для реализации базовой функции C# используется метод кода программной части. Пользовательские функции для заданий Edge в Stream Analytics сейчас находятся на этапе предварительной версии, и их не следует использовать в рабочих нагрузках.

Из этого руководства вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * Создание пользовательской функции C# с помощью кода программной части
> * Тестирование задания Edge в Stream Analytics на локальном компьютере
> * Публикация задания Edge в Azure

## <a name="prerequisites"></a>Предварительные требования

Прежде чем приступить к работе, выполните следующие предварительные требования:

* Если у вас еще нет подписки Azure, создайте [бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Установите [средства Stream Analytics для Visual Studio](stream-analytics-tools-for-visual-studio-install.md) и рабочие нагрузки **разработки Azure** или **службы хранения и обработки данных**.
* Ознакомьтесь с [руководством по разработке Edge для Stream Analytics](stream-analytics-tools-for-visual-studio-edge-jobs.md).

## <a name="create-a-container-in-your-azure-storage-account"></a>Создание контейнера в учетной записи хранения

Созданный вами контейнер будет использоваться для хранения скомпилированного пакета C# и развертывания этого пакета на устройстве IoT Edge. Для каждого задания Stream Analytics используйте отдельный контейнер. Использование одного и того же контейнера для разных заданий Edge в Stream Analytics не поддерживается. Если у вас уже есть учетная запись хранения с существующими контейнерами, вы можете их использовать. Если нет, [создайте новый контейнер](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal). 

## <a name="create-a-stream-analytics-edge-project-in-visual-studio"></a>Создание проекта Edge в Stream Analytics с помощью Visual Studio

1. Запустите Visual Studio.

2. Выберите **Файл > Создать > Проект**.

3. Из списка шаблонов слева выберите **Stream Analytics** и щелкните **Приложение Azure Stream Analytics**.

4.  Введите в проекте**Имя**, **Расположение** и **Имя решения**, а затем нажмите кнопку **ОК**.

    ![Создание проекта Edge в Azure Stream Analytics с помощью Visual Studio](./media/stream-analytics-edge-csharp-udf/stream-analytics-create-edge-app.png)

## <a name="configure-assembly-package-path"></a>Настройка пути к пакету сборки

1. Откройте Visual Studio и перейдите к **обозревателю решений**.

2. Дважды щелкните файл конфигурации задания `EdgeJobConfig.json`.

3. Разверните раздел **Настройка пользовательского кода** и укажите следующие предложенные значения в конфигурации:

    |**Параметр**  |**Рекомендуемое значение**  |
    |---------|---------|
    |Источник сборки  |  Ссылка на локальный проект или код программной части   |
    |Ресурс  |  Выберите данные текущей учетной записи   |
    |Подписка  |  Выберите свою подписку.   |
    |Учетная запись хранения  |  Выберите учетную запись хранения.   |
    |Контейнер  |  Выберите контейнер, созданный в учетной записи хранения.   |

    ![Создание конфигурации задания Edge в Azure Stream Analytics с помощью Visual Studio](./media/stream-analytics-edge-csharp-udf/stream-analytics-edge-job-config.png)


## <a name="write-a-c-udf-with-codebehind"></a>Написание пользовательской функции C# с помощью кода программной части
Файл кода программной части — это файл C#, связанный с одним сценарием запросов Edge в ASA. Средства Visual Studio автоматически архивируют файл кода программной части и после передачи отправляют его в вашу учетную запись хранения. Все классы должны быть определены как *public*, а объекты — как *static public*.

1. В **обозревателе решений** разверните **Script.asql** и найдите файл кода программной части **Script.asaql.cs**.

2. Замените код на приведенный ниже:

    ```csharp
        using System; 
        using System.Collections.Generic; 
        using System.IO; 
        using System.Linq; 
        using System.Text; 
    
        namespace ASAEdgeUDFDemo 
        { 
            public class Class1 
            { 
                // Public static function 
                public static Int64 SquareFunction(Int64 a) 
                { 
                    return a * a; 
                } 
            } 
        } 
    ```

## <a name="implement-the-udf"></a>Реализация пользовательской функции

1. В **обозревателе решений** откройте файл **Script.asaql**.

2. Замените имеющийся запрос на следующий:

    ```sql
        SELECT machine.temperature, udf.ASAEdgeUDFDemo_Class1_SquareFunction(try_cast(machine.temperature as bigint))
        INTO Output
        FROM Input 
    ```

## <a name="local-testing"></a>Локальное тестирование

1. Скачайте [файл данных с примером симулятора температуры](https://raw.githubusercontent.com/Azure/azure-stream-analytics/master/Sample%20Data/TemperatureSampleData.json) Edge.

2. В **обозревателе решений** разверните **Входные данные**, щелкните правой кнопкой мыши **Input.json** и выберите команду **Добавить локальный ввод**.

   ![Добавление локальных входных данных в задание Stream Analytics в Visual Studio](./media/stream-analytics-edge-csharp-udf/stream-analytics-add-local-input.png)

3. Укажите путь к локальному файлу входных данных с загруженным вами примером и нажмите **Сохранить**.

    ![Конфигурация локальных входных данных для задания Stream Analytics в Visual Studio](./media/stream-analytics-edge-csharp-udf/stream-analytics-local-input-config.png)

4. В редакторе запросов нажмите **Запустить локально**. После того как локальное выполнение успешно сохранит выходные данные, нажмите любую клавишу, чтобы отобразить результаты в виде таблицы. 

    ![Выполнение задания Azure Stream Analytics на локальном компьютере с помощью Visual Studio](./media/stream-analytics-edge-csharp-udf/stream-analytics-run-locally.png)

5. Вы также можете выбрать команду **Открыть папку результатов**, чтобы увидеть необработанные файлы в формате JSON и CSV.

    ![Просмотр результатов локального задания Azure Stream Analytics с помощью Visual Studio](./media/stream-analytics-edge-csharp-udf/stream-analytics-view-local-results.png)

## <a name="debug-a-udf"></a>Отладка пользовательской функции
Отладку пользовательских функций C# можно выполнять локально, точно так же, как отладку стандартного кода C#. 

1. Добавьте точки останова в функцию C#.

    ![Добавление точек останова в пользовательскую функцию Stream Analytics в Visual Studio](./media/stream-analytics-edge-csharp-udf/stream-analytics-udf-breakpoints.png)

2. Нажмите клавишу **F5**, чтобы запустить отладку. Программа будет останавливаться в точках останова.

    ![Просмотр результатов отладки определяемой пользователем функции Stream Analytics](./media/stream-analytics-edge-csharp-udf/stream-analytics-udf-debug.png)

## <a name="publish-your-job-to-azure"></a>Публикация задания в Azure
Протестировав свой запрос локально, выберите команду **Отправить в Azure** в редакторе сценариев, чтобы опубликовать задание в Azure.

![Отправка задания Edge в Stream Analytics из Visual Studio в Azure](./media/stream-analytics-edge-csharp-udf/stream-analytics-udf-submit-job.png)

## <a name="deploy-to-iot-edge-devices"></a>Развертывание на устройствах IoT Edge
Теперь задание Stream Analytics готово к развертыванию в качестве модуля IoT Edge. Согласно [краткому руководству по IoT Edge](https://docs.microsoft.com/azure/iot-edge/quickstart) создайте центр IoT, зарегистрируйте устройство IoT Edge, а затем установите и запустите среду выполнения IoT Edge на своем устройстве. Далее в соответствии с руководством по [развертыванию заданий](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-stream-analytics#deploy-the-job) разверните задание Stream Analytics как модуль IoT Edge. 

## <a name="next-steps"></a>Дополнительная информация

В этом руководстве вы создали простую пользовательскую функцию C#, используя код программной части, опубликовали ее в Azure и развернули задание на устройствах IoT Edge через портал центра IoT. 

Дополнительные сведения о различных способах использования пользовательских функций C# для выполнения заданий Edge в Stream Analytics см. в этой статье:

> [!div class="nextstepaction"]
> [Разработка пользовательских функций .NET Standard для заданий Edge в Azure Stream Analytics (предварительная версия)](stream-analytics-edge-csharp-udf-methods.md)
