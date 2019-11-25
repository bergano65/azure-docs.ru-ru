---
title: Создание задачи автоматического развертывания на портале Azure — Azure IoT Edge | Документация Майкрософт
description: Создание задач автоматического развертывания для групп устройств IoT Edge с помощью портала Azure
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/17/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 286bab7b7fdbe42190c32dabb42c59d6fc094b2a
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/24/2019
ms.locfileid: "74457366"
---
# <a name="deploy-and-monitor-iot-edge-modules-at-scale-using-the-azure-portal"></a>Развертывание и мониторинг большого числа модулей IoT Edge с помощью портала Azure

Create an **IoT Edge automatic deployment** in the Azure portal to manage ongoing deployments for many devices at once. Automatic deployments for IoT Edge are part of the [automatic device management](/azure/iot-hub/iot-hub-automatic-device-management) feature of IoT Hub. Deployments are dynamic processes that enable you to deploy multiple modules to multiple devices, track the status and health of the modules, and make changes when necessary. 

For more information, see [Understand IoT Edge automatic deployments for single devices or at scale](module-deployment-monitoring.md).

## <a name="identify-devices-using-tags"></a>Определение устройств с помощью тегов

Перед созданием развертывания необходимо указать устройства, на которые вы хотите повлиять. Azure IoT Edge определяет устройства с помощью **тегов** в двойнике устройства. Each device can have multiple tags that you define in any way that makes sense for your solution. Например, при управлении кампусом интеллектуальных зданий можно добавлять к устройству следующие теги:

```json
"tags":{
  "location":{
    "building": "20",
    "floor": "2"
  },
  "roomtype": "conference",
  "environment": "prod"
}
```

Дополнительные сведения о двойниках устройств и тегах см. в статье [Общие сведения о двойниках устройств и их использование в Центре Интернета вещей](../iot-hub/iot-hub-devguide-device-twins.md).

## <a name="create-a-deployment"></a>Создание развертывания

1. Найдите нужный Центр Интернета вещей на [портале Azure](https://portal.azure.com). 
1. Выберите **IoT Edge**.
1. Выберите **Add IoT Edge Deployment** (Добавить развертывание IoT Edge).

Процедура создания развертывания состоит из пяти шагов. В следующих разделах описан каждый из этих шагов. 

### <a name="step-1-name-and-label"></a>Шаг 1. Имя и метка

1. Присвойте своему развертыванию уникальное имя, содержащее до 128 букв в нижнем регистре. Не используйте пробелы и следующие недопустимые символы: `& ^ [ ] { } \ | " < > /`.
1. Вы можете добавить метки в виде пар "ключ —значение" для отслеживания развертываний. Например, **HostPlatform** и **Linux** или **Версия** и **3.0.1**.
1. Нажмите кнопку **Далее**, чтобы перейти ко второму шагу. 

### <a name="step-2-add-modules-optional"></a>Шаг 2. Добавление модулей (необязательно)

You can add up to 20 modules to a deployment. 

If you create a deployment with no modules, it removes any current modules from the target devices. 

Чтобы добавить модуль из Azure Stream Analytics, сделайте следующее:

1. В разделе **Модули развертывания** на этой странице щелкните **Добавить**.
1. Выберите модуль **Azure Stream Analytics**.
1. В раскрывающемся меню выберите свою **подписку**.
1. Choose your IoT **Edge job** from the drop-down menu.
1. Выберите **Сохранить**, чтобы добавить модуль в развертывание. 

Чтобы добавить пользовательский код в качестве модуля или вручную добавить модуль службы Azure, сделайте следующее:

1. В разделе **Параметры реестра контейнеров** на этой странице введите имена и учетные данные всех реестров частных контейнеров, которые содержат образы модулей для этого развертывания. The IoT Edge Agent will report error 500 if it can't find the container registry credential for a Docker image.
1. В разделе **Модули развертывания** на этой странице щелкните **Добавить**.
1. Выберите **Модуль IoT Edge**.
1. Укажите **имя** модуля.
1. В поле **URI образа** введите URI образа контейнера для этого модуля. 
1. Укажите любые **параметры создания контейнера**, которые следует передать в контейнер. Дополнительные сведения см. в статье [о создании Docker](https://docs.docker.com/engine/reference/commandline/create/).
1. В раскрывающемся меню выберите **политику перезагрузки**. Выберите один из следующих параметров: 
   * **Всегда.** Модуль всегда перезапускается, если он завершил работу по любой причине.
   * **never** - The module never restarts if it shuts down for any reason.
   * **on-failure** - The module restarts if it crashes, but not if it shuts down cleanly. 
   * **on-unhealthy** - The module restarts if it crashes or returns an unhealthy status. Реализация функции состояния работоспособности зависит от модуля. 
1. В раскрывающемся меню выберите **требуемое состояние** модуля. Выберите один из следующих параметров:
   * **running** - Running is the default option. Модуль начнет выполняться сразу после развертывания.
   * **stopped** - After being deployed, the module will remain idle until called upon to start by you or another module.
1. Выберите **Установить требуемые свойства двойника модуля**, если необходимо добавить теги или другие свойства в двойник модуля.
1. Введите **Переменные среды** для этого модуля. Environment variables provide configuration information to a module.
1. Выберите **Сохранить**, чтобы добавить модуль в развертывание. 

Настроив все модули для развертывания, нажмите кнопку **Далее**, чтобы перейти к шагу 3.

### <a name="step-3-specify-routes-optional"></a>Шаг 3. Указание маршрутов (необязательно)

Маршруты определяют, как модули взаимодействуют между собой в рамках развертывания. По умолчанию мастер предлагает маршрут с именем **route**, который определен как **FROM /* INTO $upstream**. Этот маршрут направляет все сообщения, выводимые любыми модулями, в Центр Интернета вещей.  

Добавьте в маршруты информацию из раздела [Объявление маршрутов](module-composition.md#declare-routes), затем щелкните **Далее**, чтобы перейти к разделу проверки.

### <a name="step-4-specify-metrics-optional"></a>Step 4: Specify Metrics (optional)

Метрики предоставляют общее количество различных состояний, которые устройство может передавать в результате применения содержимого конфигурации.

1. Введите имя для параметра **Имя метрики**.

1. Введите запрос для параметра **Metric Criteria** (Критерии метрики). Запрос основан на [переданных свойствах](module-edgeagent-edgehub.md#edgehub-reported-properties) двойников модуля центра IoT Edge. Метрика представляет количество строк, возвращаемых запросом.

   Пример.

   ```sql
   SELECT deviceId FROM devices
     WHERE properties.reported.lastDesiredStatus.code = 200
   ```

### <a name="step-5-target-devices"></a>Step 5: Target Devices

С помощью свойств тегов ваших устройств можно назначить конкретные устройства, к которым будет применяться это развертывание. 

Так как несколько развертываний могут предназначаться для одного устройства, каждому развертыванию необходимо присвоить номер приоритета. If there's ever a conflict, the deployment with the highest priority (larger values indicate higher priority) wins. Когда оба развертывания имеют одинаковый номер приоритета, побеждает то, которое было создано недавно. 

1. Введите положительное целое число для **приоритета** развертывания.
1. Введите **условие назначения**, чтобы определить, для каких устройств будет предназначено это развертывание. The condition is based on device twin tags or device twin reported properties and should match the expression format. For example, `tags.environment='test'` or `properties.reported.devicemodel='4000x'`. 
1. Нажмите кнопку **Далее**, чтобы перейти к последнему шагу.

### <a name="step-6-review-deployment"></a>Step 6: Review Deployment

Просмотрите сведения о развертывании, а затем выберите **Отправить**.

## <a name="deploy-modules-from-azure-marketplace"></a>Развертывание модулей из Azure Marketplace

Azure Marketplace — это интернет-магазин приложений и служб, где вы можете просматривать разнообразные корпоративные приложения и решения, сертифицированные и оптимизированные для работы в Azure, включая [модули IoT Edge](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules). Доступ к Azure Marketplace возможен также с помощью портала Azure в разделе **Создать ресурс**.

You can deploy an IoT Edge module from either Azure Marketplace or the Azure portal:

1. Найдите модуль и начните процесс развертывания.

   * Azure portal: Find a module and select **Create**.

   * Azure Marketplace:

     1. Найдите модуль и выберите **Получить**.
     1. Подтвердите условия использования и политику конфиденциальности поставщика, выбрав **Продолжить**.

1. Выберите свою подписку и Центр Интернета вещей, к которому подключено целевое устройство.

1. Выберите **Развертывание в масштабе**.

1. Выберите, следует ли добавить модуль в новое развертывание или в клон существующего развертывания. При клонировании выберите существующее развертывание из списка.

1. Выберите **Создать**, чтобы продолжить процесс создания развертывания в масштабе. Вы сможете указать те же данные, что и для любого развертывания.

## <a name="monitor-a-deployment"></a>Мониторинг развертывания

Чтобы просмотреть сведения о развертывании и узнать, какое устройство его выполняет, сделайте следующее:

1. Войдите на [портал Azure](https://portal.azure.com) и перейдите к своему Центру Интернета вещей. 
1. Выберите **IoT Edge**.
1. Выберите **IoT Edge deployments** (Развертывания IoT Edge). 

   ![Просмотр развертываний IoT Edge](./media/how-to-deploy-monitor/iot-edge-deployments.png)

1. Изучите список развертывания. For each deployment, you can view the following details:
   * **Идентификатор**. Имя развертывания.
   * **Target condition** (Условие назначения). Тег, используемый для определения целевых устройств.
   * **Приоритет.** Номер приоритета, назначенный для развертывания.
   * **Системные метрики** - **Целевые устройства**. Число двойников устройств в Центре Интернета вещей, которые соответствуют заданному условию. **Применено**. Число устройств, к двойникам которых уже применено содержимое этого развертывания в Центре Интернета вещей. 
   * **Device metrics** - the number of IoT Edge devices in the deployment reporting success or errors from the IoT Edge client runtime.
   * **Custom metrics** - the number of IoT Edge devices in the deployment reporting data for any metrics that you defined for the deployment.
   * **Время создания.** Метка времени, когда было создано развертывание. Метка времени используется, чтобы разорвать связи, если два развертывания имеют одинаковый приоритет. 
1. Выберите развертывание, которое вы хотите отслеживать.  
1. Изучите сведения о развертывании. Вы можете использовать вкладки для просмотра сведений о развертывании.

## <a name="modify-a-deployment"></a>Изменение развертывания

При изменении развертывания изменения немедленно реплицируются во все целевые устройства. 

Если обновить целевое условие, произойдут следующие обновления:

* Если устройство не соответствует предыдущему условию назначения, однако соответствует новому и это развертывание имеет самый высокий приоритет для устройства, то оно будет применено к устройству. 
* Если устройство, на котором сейчас выполняется развертывание, больше не соответствует условию назначения, оно удаляет это развертывание и выполняет следующее развертывание с самым высоким приоритетом. 
* Если устройство, на котором сейчас выполняется развертывание, больше не соответствует условию назначения, а также условию назначения любого другого развертывания, в устройстве не происходит никаких изменений. Устройство продолжает выполнять текущие модули в их актуальном состоянии, однако больше не управляется как часть этого развертывания. Если устройство соответствует условию назначения любого другого развертывания, оно удаляет это развертывание и выполняет новое. 

Чтобы изменить развертывание, сделайте следующее: 

1. Войдите на [портал Azure](https://portal.azure.com) и перейдите к своему Центру Интернета вещей. 
1. Выберите **IoT Edge**.
1. Выберите **IoT Edge deployments** (Развертывания IoT Edge). 

   ![Просмотр развертываний IoT Edge](./media/how-to-deploy-monitor/iot-edge-deployments.png)

1. Выберите развертывание, которое вы хотите изменить. 
1. Внесите изменения в следующие поля: 
   * Условие назначения
   * Метрики. Вы можете изменить или удалить метрики, которые определили, или добавить новые.
   * Метки
   * Приоритет
1. Щелкните **Сохранить**.
1. Для отслеживания выполнения изменений следуйте указаниям раздела [Мониторинг развертывания](#monitor-a-deployment). 

## <a name="delete-a-deployment"></a>Удаление развертывания

При удалении развертывания все устройства выполняют следующее развертывание, которое имеет наивысший приоритет. Если устройства не соответствуют условиям назначения любого другого развертывания, то модули не будут удалены при удалении развертывания. 

1. Войдите на [портал Azure](https://portal.azure.com) и перейдите к своему Центру Интернета вещей. 
1. Выберите **IoT Edge**.
1. Выберите **IoT Edge deployments** (Развертывания IoT Edge). 

   ![Просмотр развертываний IoT Edge](./media/how-to-deploy-monitor/iot-edge-deployments.png)

1. Установите флажок, чтобы выбрать развертывание, которое необходимо удалить. 
1. Нажмите кнопку **Удалить**.
1. Появится запрос с сообщением о том, что это действие удалит развертывание и будут возвращены предыдущие состояния всех устройств.  This means that a deployment with a lower priority will apply.  If no other deployment is targeted, no modules will be removed. Чтобы удалить все модули из устройства, создайте развертывание без модулей и разверните его на этом устройстве. Select **Yes** to continue. 

## <a name="next-steps"></a>Дальнейшие действия

Learn more about [Deploying modules to IoT Edge devices](module-deployment-monitoring.md).
