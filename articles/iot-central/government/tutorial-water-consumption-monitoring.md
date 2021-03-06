---
title: Руководство по созданию приложения для мониторинга потребления воды в Azure IoT Central
description: Руководство по созданию приложения для мониторинга потребления воды с использованием шаблонов приложений Azure IoT Central.
author: miriambrus
ms.author: miriamb
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: 0b100e0bebc7c5b26449e396650da5434334075d
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/15/2019
ms.locfileid: "74112620"
---
# <a name="tutorial-create-a-water-consumption-monitoring-application-in-iot-central"></a>Руководство по созданию приложения для мониторинга потребления воды в IoT Central

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

В этом учебнике описано, как создать приложение для мониторинга потребления воды в Azure IoT Central, используя соответствующий шаблон. 

В этом учебнике рассмотрены следующие темы: 

> [!div class="checklist"]
> * использование шаблона Azure IoT Central **Мониторинг потребления воды** для создания приложения;
> * изучение и настройка панели мониторинга оператора; 
> * изучение шаблона устройства;
> * изучение имитированных устройств;
> * изучение и настройка правил;
> * Настройка заданий
> * настройка фирменной символики приложения с использованием белых меток.

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим учебником требуется:
-  подписка Azure (рекомендуется). Если у вас еще нет подписки, создайте ее на [странице регистрации в Azure](https://aka.ms/createazuresubscription);

## <a name="create-water-consumption-monitoring-app-in-iot-central"></a>Создание приложения для мониторинга потребления воды в Azure IoT Central

В этом разделе мы будем использовать шаблон Azure IoT Central **Мониторинг потребления воды**, чтобы создать приложение для соответствующих целей.

Для этого сделайте следующее:  

1. Перейдите на [домашнюю страницу Azure IoT Central](https://aka.ms/iotcentral).

    Если у вас есть подписка Azure, выполните вход с учетными данными для доступа к ней. Если ее нет, используйте учетную запись Майкрософт:

    ![Ввод учетной записи организации](media/tutorial-waterconsumptionmonitoring/sign-in.png)

2. Выберите раздел **Сборка** на панели слева, а затем вкладку **Государственные организации**. На странице государственных учреждений будут показаны несколько шаблонов приложений для соответствующих организаций.

   ![Шаблоны для сборки приложений для государственных организаций](./media/tutorial-waterconsumptionmonitoring/iotcentral-government-tab-overview1.png)

1. Выберите шаблон приложения **Мониторинг потребления воды**. Он предусматривает шаблон устройства, потребляющего воду, имитированное устройство, панель мониторинга оператора и предварительно настроенные правила мониторинга.    

2. Щелкните **Создать приложение**. Откроется форма создания приложения **Новое приложение** с такими полями:
    * **Имя приложения** — по умолчанию для приложения используется *Мониторинг потребления воды*. За именем следует строка уникального идентификатора, который создает Azure IoT Central. При желании выберите понятное имя для приложения. Имя приложения также можно изменить позже.
    * **URL-адрес**. IoT Central автоматически создаст URL-адрес на основе имени приложения. Вы можете заменить этот URL-адрес на необходимый. Его также можно изменить позже. 
    * Если у вас есть подписка Azure, укажите *каталог, подписку Azure и регион*. Если у вас нет подписки, вы можете включить **семидневную бесплатную пробную версию** и указать необходимые контактные данные.  

    Дополнительные сведения о каталогах и подписках см. в статье [Создание приложения Azure IoT Central](../preview/quick-deploy-iot-central.md).

5. Нажмите кнопку **Создать** внизу страницы. 

    ![Страница создания приложения Azure IoT Central](./media/tutorial-waterconsumptionmonitoring/new-application-waterconsumptionmonitoring.png)

6. Вы создали необходимое приложение, используя шаблон Azure IoT Central **Мониторинг потребления воды**.

Поздравляем! Вы создали приложение для мониторинга качества воды, которое состоит из:
* примеров панелей мониторинга для операторов;
* примеров предопределенных шаблонов измерителя скорости потока воды и клапанов;
* имитированных измерителя скорости потока воды и интеллектуального клапана;
* предварительно настроенных правил и заданий;
* фирменной символики с использованием белых меток. 

Вы можете в любое время вносить изменения в свое приложение. Теперь изучим приложение и настроим его.  

## <a name="explore-and-customize-operator-dashboard"></a>Изучение и настройка панели мониторинга оператора 
После создания приложения вы перейдете к примеру панели мониторинга оператора, которая называется **Wide World water consumption monitoring dashboard** (Панель мониторинга потребления воды Wide World Water).

   ![Панель мониторинга потребления воды](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-dashboardfull.png)

Как сборщик вы можете создавать и настраивать на панели мониторинга представления, которые будут использовать операторы. Но прежде чем что-то настроить, сначала ознакомимся с панелью мониторинга. 

> [!NOTE]
> Все данные, которые отображаются на панели мониторинга, основаны на данных с имитированного устройства, речь о котором пойдет в следующем разделе. 
  
Панель мониторинга состоит из плиток различных видов:

* Первая на панели мониторинга **плитка с изображением службы водоснабжения Wide World Water** — это плитка вымышленной службы Wide World Water. Вы можете настроить эту плитку и добавить свое изображение или вовсе удалить ее. 

* **Плитка средних ключевых показателей эффективности, подаваемых измерителем скорости потока воды,**  — это плитка КПЭ, настроенная показывать, к примеру, *среднее значение за последние 30 мин*. Вы можете настроить плитки КПЭ и задать различные типы и диапазоны времени.

* Справа на панели мониторинга расположены плитки *команд устройства*: **Close valve** (Закрыть клапан), **Open valve** (Открыть клапан) и **Set valve position** (Установить положение клапана). Щелкните команду, чтобы перейти на ее страницу для имитированного устройства. В IoT Central *команда* — это тип *возможности устройства*, который мы рассмотрим далее в разделе о **шаблонах устройства** этого учебника.

*  **Карта области распределения воды** — использует Azure Maps, что можно настроить прямо в Azure IoT Central. На фрагменте карты отображается расположение устройства. Попробуйте навести указатель мыши на карте и использовать на ней элементы управления, такие как *увеличение*, *уменьшение* и *развертывание*. 

    ![Карта на панели мониторинга потребления воды](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-dashboard-map.png)

* **График среднего потока воды** и **график условий окружающей среды** — вы можете визуализировать один или несколько наборов данных телеметрии с устройств за нужный диапазон времени.  

* **Диаграмма в виде тепловой карты для среднего значения давления на клапан** — вы можете выбрать тепловую карту в качестве типа визуализации данных телеметрии с устройства, чтобы просматривать распределение по диапазону времени с индексом цвета.

* **Плитка сброса содержимого порогового значения для оповещения** — вы можете включить вызов плиток содержимого действий, внедрив ссылки на страницы действий. В этом случае при сбросе порогового значения для оповещения вы перейдете к разделу **Задания** в приложении, где можно запустить обновление свойств устройств. Эти задания мы рассмотрим далее в разделе о **настройке заданий** этого учебника.

* **Плитки свойств** — на панели мониторинга отображаются плитки со свойствами устройств **Valve operational info** (Информация о работе клапана), **Flow alert thresholds** (Пороговые значения оповещений для потоков) и **Maintenance info** (Сведения об обслуживании).  


### <a name="customize-dashboard"></a>Настройка панели мониторинга 

Как сборщик вы можете настроить представления на панели мониторинга для операторов. Вы можете сделать следующее:
1. Щелкните меню **Правка**, чтобы настроить **панель мониторинга потребления воды Wide World Water**. Вы можете настроить панель мониторинга, выбирая команды в меню **Правка**. Когда панель мониторинга находится в режиме **правки**, можно добавлять новые плитки или настраивать существующие. 

     ![Изменение панели мониторинга](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-edit-dashboard.png)

2. Нажмите кнопку **+ Создать**, чтобы создать панель мониторинга и настроить ее с нуля. Вы можете создать несколько панелей мониторинга и перемещаться между ними с помощью меню панелей. 

## <a name="explore-device-template"></a>Изучение шаблона устройства
В шаблоне устройства в Azure IoT Central определены возможности устройства, например телеметрия, свойство или команда. Как сборщик вы можете определить один или несколько шаблонов в Azure IoT Central, которые представляют возможности подключаемых устройств. 
 

Приложение **Мониторинг потребления воды** поставляется с двумя шаблонами устройств, представляющими *измеритель скорости потока* и *интеллектуальный клапан*. 

Чтобы просмотреть шаблон устройства, сделайте следующее:

1. В приложении в IoT Central в области навигации слева щелкните раздел **Шаблоны устройств**. 
    В списке шаблонов устройств вы увидите два шаблона **Flow meter** (Измеритель скорости потока) и **Smart Valve** (Интеллектуальный клапан).

   ![Шаблон устройства](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-devicetemplate.png)

2. Щелкните шаблон **Flow meter** (Измеритель скорости потока) и ознакомьтесь с возможностями устройства. 

     ![Шаблон устройства Flow meter (Измеритель скорости потока)](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-devicetemplate-flowmeter.png)

### <a name="customizing-the-device-template"></a>Настройка шаблона устройства

Чтобы настроить шаблон, сделайте следующее:
1. В меню шаблона устройства выберите пункт **Настроить**.
2. Найдите тип телеметрии `Temperature`.
3. Замените имя `Temperature` в столбце **Отображаемое имя** на `Reported temperature`.
4. Измените единицу измерения или установите *минимальное* и *максимальное значения*.
5. Нажмите кнопку **Сохранить**, чтобы сохранить изменения. 

### <a name="add-a-cloud-property"></a>Добавление свойства облака 
1. В меню шаблона устройства перейдите в раздел **Облачное свойство**.
2. Щелкните **+ Добавить облачное свойство**, чтобы добавить новое свойство облака. 
    В IoT Central можно добавить свойство, которое относится к устройству. Одним из примеров такого свойства является пороговое значение для оповещения, относящееся к области установки, сведениям о ресурсе или обслуживании и т. п. 
3. Нажмите кнопку **Сохранить**, чтобы сохранить изменения. 
 
### <a name="views"></a>Представления 
Шаблон устройства для мониторинга потребления воды поставляется с предопределенными представлениями. Ознакомьтесь с ними и внесите изменения. В представлениях определяется, как операторы будут видеть данные устройства, и вводятся облачные свойства. 

  ![Представления для шаблона устройства](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-devicetemplate-views.png)

### <a name="publish"></a>Публикация 
Если вы внесли изменения, обязательно **опубликуйте** шаблон устройства. 

### <a name="create-a-new-device-template"></a>Создание шаблона нового устройства 
- Нажмите кнопку **+ Создать**, чтобы создать шаблон устройства, и выполните всю процедуру. Вы сможете создать настраиваемый шаблон устройства с нуля или выбрать шаблон в каталоге устройств Azure. 

## <a name="explore-simulated-devices"></a>Изучение имитированных устройств
В IoT Central вы также можете создавать имитированные устройства для тестирования шаблона устройства и приложения. В приложении **Мониторинг потребления воды** есть два имитированных устройства, которые соответствуют шаблонам *Flow meter* (Измеритель скорости потока) и *Smart Valve* (Интеллектуальный клапан). 

### <a name="to-view-the-devices"></a>Чтобы просмотреть устройства, сделайте следующее:
1. В IoT Central на панели навигации слева перейдите в раздел **Устройство**. 

   ![Устройства](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-devices.png)

2. Щелкните **Smart Valve 1** (Интеллектуальный клапан 1). 

    ![Устройство 1](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitor-device1.png)

3.  В разделе **Команды устройства** можно увидеть три команды *Open Valve* (Открыть клапан), *Close Valve* (Закрыть клапан) и *Set Valve position* (Установить положение клапана), которые являются возможностями, определенными в шаблоне *Smart Valve* (Интеллектуальный клапан). 
4. Ознакомьтесь со вкладками **Свойства устройства** и **Device Dashboard** (Панель мониторинга устройств). 

> [!NOTE]
> Обратите внимание, что все вкладки настроены с помощью представлений шаблонов устройств.

### <a name="add-new-devices"></a>Добавление новых устройств
* Добавьте новые устройства, нажав кнопку **+ Создать** на вкладке **Устройства**. 

## <a name="explore-and-configure-rules"></a>Изучение и настройка правил

В Azure IoT Central можно создавать правила для автоматических мониторинга телеметрии устройств и активации действий при выполнении одного или нескольких условий. Действия могут включать в себя отправку уведомлений по электронной почте либо активацию действия Microsoft Flow или веб-перехватчика для отправки данных в другие службы.

В созданном приложении **Мониторинг потребления воды** есть три предварительно настроенных правила.

### <a name="to-view-rules"></a>Чтобы просмотреть правила, сделайте следующее:
1. В IoT Central на панели навигации слева перейдите в раздел **Правила**. 

   ![Правила](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-rules.png)

2. Щелкните **High pH alert** (Оповещение о высоком уровне pH), которое является одним из предварительно настроенных правил в приложении.

     ![Предупреждение о высоком уровне pH](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-highflowalert.png)

    Правило `High flow alert` настроено для проверки того, больше ли `Acidity (pH)` (`greater than`), чем `Max flow threshold`. Максимальное пороговое значение потока — это свойство облака, определенное в шаблоне устройства *Smart Valve* (Интеллектуальный клапан). Значение `Max flow threshold` задается для каждого экземпляра устройства. 

Теперь создадим действие отправки сообщения.

Чтобы добавить действие к правилу, выполните следующие действия.

1. Нажмите **+ Электронная почта**. 
1. Задайте *Уведомление о высоком уровне pH* в качестве понятного **отображаемого имени** для действия.
    * Введите адрес электронной почты, связанный с вашей учетной записью IoT Central, в поле **Кому**. 
1. При необходимости введите заметку, которая будет включена в текст сообщения электронной почты.
1. Нажмите кнопку **Готово**, чтобы завершить создание действия.
1. Выберите **Сохранить**, чтобы сохранить и активировать новое правило. 

В течение нескольких минут вы получите сообщение электронной почты, если будет выполнено настроенное **условие**.

> [!NOTE]
> Приложение будет отправлять сообщения электронной почты при каждом выполнении условия. **Отключите** правило, чтобы прекратить получение сообщений при соблюдении условия автоматического правила. 
  
Чтобы создать правило, сделайте следующее: 
- В области навигации слева в разделе **Правила** нажмите кнопку **+ Создать**.

## <a name="configure-jobs"></a>Настройка заданий

В IoT Central с помощью заданий можно активировать обновления свойств устройства или облака на нескольких устройствах. В дополнение к активации свойств, можно также использовать задания для активации команд устройства на нескольких устройствах. IoT Central автоматизирует рабочий процесс. 

1. В области навигации слева выберите раздел **Задания**. 
2. Нажмите кнопку **+ Создать** и настройте одно или несколько заданий. 


## <a name="customize-your-application"></a>Настройка приложения 
Создатель приложения может изменить несколько параметров, чтобы настроить взаимодействие с пользователем в приложении.

1.  Выберите **Администрирование > Настройка приложения**.
3. Используйте кнопку **Изменить**, чтобы выбрать изображение для отправки в качестве **логотипа приложения**.
4. Используйте кнопку **Изменить**, чтобы выбрать изображение как **значок браузера**, который будет отображаться на вкладках браузера.
5. Вы также можете заменить цвета по умолчанию в разделе **Цвета браузера**, добавив шестнадцатеричные коды цветов HTML.

   ![Настройка приложения в Azure IoT Central](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-customize-your-application.png)

1.  Вы также можете изменить изображения приложения. Для этого выберите **Администрирование > Параметры приложения** и нажмите кнопку **Выберите изображение**, чтобы указать изображение и отправить его. 
2. Наконец, можно также изменить **тему**, щелкнув **Параметры** в заголовке приложения. 

  
## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы не собираетесь дальше использовать это приложение, удалите его, выполнив следующие действия.

1. Откройте вкладку "Администрирование" в приложении IoT Central на панели слева. 
2. Выберите параметры приложения и нажмите кнопку "Удалить" в нижней части страницы. 


## <a name="next-steps"></a>Дополнительная информация

* Чтобы узнать больше, обратитесь к статье [Эталонная архитектура решения для мониторинга потребления воды](./concepts-waterconsumptionmonitoring-architecture.md).
