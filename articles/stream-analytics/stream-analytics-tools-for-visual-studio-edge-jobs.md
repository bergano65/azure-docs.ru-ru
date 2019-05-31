---
title: Задания Stream Analytics Edge поле данных в инструментах Azure Stream Analytics для Visual Studio
description: В этой статье описывается, как создавать, отлаживать и создания заданий Edge поле данных для Stream Analytics с помощью средств Stream Analytics для Visual Studio.
services: stream-analytics
author: su-jie
ms.author: sujie
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: 242fb2daebfe9eb6e5a0c73c2c4c0e91a3131032
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/29/2019
ms.locfileid: "66304163"
---
# <a name="develop-stream-analytics-data-box-edge-jobs-using-visual-studio-tools"></a>Разработка заданий Edge поле данных для Stream Analytics, с помощью средств Visual Studio

В этом руководстве вы узнаете, как использовать средства Stream Analytics для Visual Studio. Вы узнаете, как для создания, отладки и создания заданий Edge поле данных для Stream Analytics. После создания и тестирования задания можно перейти на портал Azure, чтобы развернуть его на устройствах. 

## <a name="prerequisites"></a>Технические условия

Для работы с данным руководством вам потребуется:

* Установка [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/), [Visual Studio 2015](https://www.visualstudio.com/vs/older-downloads/), или [Visual Studio 2013 с обновлением 4](https://www.microsoft.com/download/details.aspx?id=45326). Поддерживаются выпуски Enterprise (Ultimate/Premium), Professional и Community. Выпуск Express не поддерживается.  

* Следуйте [инструкциям по установке](stream-analytics-tools-for-visual-studio-edge-jobs.md), чтобы установить средства Stream Analytics для Visual Studio.
 
## <a name="create-a-stream-analytics-data-box-edge-project"></a>Создание проекта данных поле Stream Analytics Edge 

В Visual Studio выберите **Файл** > **Создать** > **Проект**. Перейдите к списку **Шаблоны** слева, разверните **Azure Stream Analytics** > **Stream Analytics Edge** > **Azure Stream Analytics Edge Application** (Приложение Azure Stream Analytics Edge). Укажите имя, расположение и имя решения для вашего проекта, а затем нажмите кнопку **ОК**.

![Новый проект Edge поле данных для Stream Analytics в Visual Studio](./media/stream-analytics-tools-for-visual-studio-edge-jobs/new-stream-analytics-edge-project.png)

После создания проекта перейдите в **обозреватель решений**, чтобы просмотреть иерархию папок.

![Представление обозревателя решений задания Edge поле данных для Stream Analytics](./media/stream-analytics-tools-for-visual-studio-edge-jobs/edge-project-in-solution-explorer.png)

 
## <a name="choose-the-correct-subscription"></a>Выбор соответствующей подписки

1. В меню **Вид** в Visual Studio выберите **Обозреватель серверов**.  

2. Щелкните правой кнопкой мыши **Azure**, выберите **Подключиться к подписке Microsoft Azure** и войдите в систему с помощью учетной записи Azure.

## <a name="define-inputs"></a>Определение входных данных

1. В **обозревателе решений** разверните узел **входных данных**. Должен появиться файл входных данных с именем **EdgeInput.json**. Дважды щелкните его, чтобы просмотреть параметры.  

2. В качестве типа источника данных выберите **Поток данных**. В качестве источника выберите **Концентратор Edge**, в качестве формата сериализации событий — **Json**, а в качестве кодировки — **UTF8**. При желании вы также можете переименовать **псевдоним входных данных** (в этом примере оставим указанное значение). В случае переименования псевдонима входных данных используйте имя, указанное при определении запроса. Нажмите кнопку **Сохранить**, чтобы сохранить параметры.  
   ![Настройка входных данных задания Stream Analytics](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-input-configuration.png)
 


## <a name="define-outputs"></a>Определение выходных данных

1. В **обозревателе решений** разверните узел **выходных данных**. Должен появиться файл выходных данных с именем **EdgeOutput.json**. Дважды щелкните его, чтобы просмотреть параметры.  

2. Не забудьте задать в качестве приемника для выбора **концентратор Edge**, равным формат сериализации событий **Json**, задайте кодировку **UTF8**и задать формат **массива**. При желании вы также можете переименовать **псевдоним выходных данных** (в этом примере оставим значение по умолчанию). В случае переименования псевдонима выходных данных используйте имя, указанное при определении запроса. Нажмите кнопку **Сохранить**, чтобы сохранить параметры. 
   ![Настройка выходных данных задания Stream Analytics](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-output-configuration.png)
 
## <a name="define-the-transformation-query"></a>Определение запроса преобразования

Задания Stream Analytics, развертывания в средах Edge поле данных для Stream Analytics поддерживают большую часть [справочнике по языку запросов Stream Analytics](https://msdn.microsoft.com/azure/stream-analytics/reference/stream-analytics-query-language-reference?f=255&MSPPError=-2147217396). Тем не менее следующие операции еще не поддерживаются для заданий Edge поле данных для Stream Analytics: 


|**Категория**  | **Команда**  |
|---------|---------|
|Геопространственные операторы |<ul><li>CreatePoint</li><li>CreatePolygon;</li><li>CreateLineString;</li><li>ST_DISTANCE;</li><li>ST_WITHIN;</li><li>ST_OVERLAPS;</li><li>ST_INTERSECTS</li></ul> |
|Другие операторы | <ul><li>PARTITION BY;</li><li>TIMESTAMP BY OVER</li><li>DISTINCT</li><li>Параметр выражения в операторе COUNT</li><li>Микросекунды в функциях даты и времени</li><li>JavaScript UDA (эта функция находится в режиме предварительной версии для заданий, развернутых в облаке)</li></ul>   |

При создании задания Edge поле данных для Stream Analytics на портале компилятор автоматически предупредит вас Если вы не используете является поддерживаемым оператором.

В Visual Studio определите следующий запрос преобразования в редакторе запросов (**script.asaql file**).

```sql
SELECT * INTO EdgeOutput
FROM EdgeInput 
```

## <a name="test-the-job-locally"></a>Локальное тестирование задания

Чтобы протестировать запрос локально, необходимо передать образец данных. Образец данных можно получить, скачав регистрационные данные из [репозитория GitHub](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/Registration.json) и сохранив их на локальном компьютере. 

1. Чтобы передать образец данных, щелкните правой кнопкой мыши файл **EdgeInput.json** и выберите **Добавить локальные входные данные**.  

2. Во всплывающем окне **перейдите** к примеру данных из локальной папки и нажмите кнопку **Сохранить**.
   ![Настройка локальных входных данных в Visual Studio](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-local-input-configuration.png)
 
3. Файл **local_EdgeInput.json** будет автоматически добавлен в папку входных данных.  
4. можно запустить его локально или отправить в Azure. Чтобы проверить запрос, выберите **запустить локально**.  
   ![Параметры запуска заданий Stream Analytics в Visual Studio](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-visual-stuidio-run-options.png)
 
5. В окне командной строки показано состояние задания. Если задание выполняется успешно, в папке проекта "Visual Studio 2015\Projects\MyASAEdgejob\MyASAEdgejob\ASALocalRun\2018-02-23-11-31-42" создается папка, например, "2018-02-23-11-31-42". Перейдите к этой папке, чтобы просмотреть результаты в локальной папке:

   Вы также можете войти на портал Azure и убедиться, что задание создано. 

   ![Папка результатов задания Stream Analytics](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-job-result-folder.png)

## <a name="submit-the-job-to-azure"></a>Отправка задания в Azure

1. Прежде чем отправлять задание, необходимо подключиться к подписке Azure. Откройте **обозреватель сервера**, щелкните правой кнопкой мыши **Azure** > **Подключиться к подписке Microsoft Azure** и войдите в вашу подписку Azure.  

2. Чтобы отправить задание в Azure, перейдите к редактору запросов и выберите **Отправить в Azure**.  

3. Откроется всплывающее окно. Выберите для обновления существующего задания Stream Analytics данных поле Edge или создайте новую. При обновлении существующего задания, она заменит все параметры задания, в этом сценарии, будет опубликовано новое задание. Выберите **Create a New Azure Stream Analytics Job** (Создать задание Azure Stream Analytics), введите имя для этого задания, например **MyASAEdgeJob**, выберите необходимые **подписку**, **группу ресурсов** и **расположение**, а затем нажмите кнопку **Отправить**.

   ![Отправка задания Stream Analytics в Azure из Visual Studio](./media/stream-analytics-tools-for-visual-studio-edge-jobs/submit-stream-analytics-job-to-azure.png)
 
   Теперь ваши данные поле Stream Analytics Edge задание будет создано. Можно ссылаться на [выполнение заданий в IoT Edge руководстве](stream-analytics-edge.md) чтобы узнать, как развернуть его на устройствах. 

## <a name="manage-the-job"></a>Управление заданием 

Вы можете просмотреть состояние и схему заданий в обозревателе сервера. Из **Stream Analytics** в **обозревателя серверов**, разверните подписку и группу ресурсов, где развернуто задание Edge поле данных для Stream Analytics. Вы увидите задание MyASAEdgejob с состоянием **Создано**. Разверните узел задания и дважды щелкните его, чтобы открыть представление задания.

![Параметры управления заданиями обозревателя сервера](./media/stream-analytics-tools-for-visual-studio-edge-jobs/server-explorer-options.png)
 
В окне представления задания вы можете обновить задание, удалить задание и открыть задание с портала Azure.

![Схема задания и другие параметры в Visual Studio](./media/stream-analytics-tools-for-visual-studio-edge-jobs/job-diagram-and-other-options.png) 

## <a name="next-steps"></a>Дальнейшие действия

* [Дополнительные сведения об Azure IoT Edge](../iot-edge/about-iot-edge.md)
* [Deploy Azure Stream Analytics as an IoT Edge module - preview](../iot-edge/tutorial-deploy-stream-analytics.md) (Развертывание Azure Stream Analytics в качестве модуля IoT Edge (предварительная версия))
* [Отправьте отзыв команде с помощью этого опроса](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR2czagZ-i_9Cg6NhAZlH9ypUMjNEM0RDVU9CVTBQWDdYTlk0UDNTTFdUTC4u) 
