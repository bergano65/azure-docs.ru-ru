---
title: Автоматическое масштабирование кластеров Azure HDInsight
description: Автоматическое Apache Hadoop масштабирования кластеров с помощью функции автомасштабирования Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/22/2019
ms.openlocfilehash: 5a8e641c8a1b29d657fe8b0eabf7657ab5973516
ms.sourcegitcommit: 57eb9acf6507d746289efa317a1a5210bd32ca2c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/01/2019
ms.locfileid: "74666041"
---
# <a name="automatically-scale-azure-hdinsight-clusters"></a>Автоматическое масштабирование кластеров Azure HDInsight

> [!Important]
> Функция автомасштабирования работает только для кластеров Spark, Hive, LLAP и HBase, созданных после 8 мая 2019. 

Функция автомасштабирования кластера Azure HDInsight автоматически масштабирует количество рабочих узлов в кластере. В настоящее время невозможно масштабировать другие типы узлов в кластере.  Во время создания кластера HDInsight можно задать минимальное и максимальное количество рабочих узлов. Затем Автомасштабирование отслеживает требования к ресурсам для аналитической загрузки и масштабирует количество рабочих узлов. Для этой функции дополнительная плата не взимается.

## <a name="cluster-compatibility"></a>Совместимость кластера

В следующей таблице описаны типы и версии кластеров, совместимые с функцией автомасштабирования.

| Версия | Spark | Hive | LLAP | hbase | Kafka | Storm | ML |
|---|---|---|---|---|---|---|---|
| HDInsight 3,6 без ESP | Да только 2,3| ДА | Нет | Нет | Нет | Нет | Нет |
| HDInsight 4,0 без ESP | ДА | ДА | Нет | Нет | Нет | Нет | Нет |
| HDInsight 3,6 с ESP | Да только 2,3 | ДА | Нет | Нет | Нет | Нет | Нет |
| HDInsight 4,0 с ESP | ДА | ДА | Нет | Нет | Нет | Нет | Нет |

## <a name="how-it-works"></a>Принципы работы

Можно выбрать масштабирование на основе нагрузки или масштабирование на основе расписания для кластера HDInsight. Масштабирование на основе загрузки изменяет количество узлов в кластере в пределах заданного диапазона для обеспечения оптимального использования ЦП и снижения затрат на выполнение.

Масштабирование на основе расписания изменяет количество узлов в кластере на основе условий, которые вступают в силу в определенное время. Эти условия позволяют масштабировать кластер до нужного количества узлов.

### <a name="metrics-monitoring"></a>Мониторинг метрик

Автомасштабирование постоянно выполняет мониторинг кластера и собирает следующие метрики:

* **Всего ожидаемого ЦП**: общее число ядер, необходимое для начала выполнения всех ожидающих контейнеров.
* **Всего ожидающих памяти**: общий объем памяти (в МБ), необходимый для начала выполнения всех ожидающих контейнеров.
* **Всего свободных ЦП**: сумма всех неиспользуемых ядер на активных рабочих узлах.
* **Общий объем свободной памяти**: сумма неиспользуемой памяти (в МБ) на активных рабочих узлах.
* **Используемая память на узел**: нагрузка на рабочий узел. Рабочий узел, на котором используется 10 ГБ памяти, пребывает под большей нагрузкой, чем рабочий узел с 2 ГБ используемой памяти.
* **Число хозяев приложения на узел**: количество контейнеров главного приложения (AM), работающих на рабочем узле. Рабочий узел, на котором размещаются два контейнера AM, считается более важным, чем рабочий узел, в котором размещаются нулевые контейнеры.

Эти метрики проверяются каждые 60 секунд. Автомасштабирование позволяет принимать решения по масштабированию и уменьшению масштаба на основе этих метрик.

### <a name="load-based-cluster-scale-up"></a>Масштабирование кластера на основе нагрузки

При обнаружении следующих условий Автомасштабирование выдаст запрос на увеличение масштаба:

* Всего ожидающих ЦП превышает общий свободный ЦП более 3 минут.
* Общий объем незавершенной памяти превышает общий объем свободной памяти в течение более 3 минут.

Служба HDInsight вычисляет, сколько новых рабочих узлов требуется для удовлетворения текущих требований к ЦП и памяти, а затем выдает запрос на масштабирование для добавления требуемого количества узлов.

### <a name="load-based-cluster-scale-down"></a>Масштабирование кластера на основе нагрузки

При обнаружении следующих условий Автомасштабирование выдаст запрос на горизонтальное масштабирование:

* Общее число ожидающих ЦП меньше, чем общее число свободных ЦП в течение более чем 10 минут.
* Общий объем ожидающей памяти меньше, чем общее количество свободной памяти в течение более чем 10 минут.

С учетом количества контейнеров AM на узел и текущих требований к ЦП и памяти, автомасштабирование выдает запрос на удаление определенного числа узлов. Служба также определяет, какие узлы являются кандидатами на удаление в зависимости от текущего выполнения задания. Операция уменьшения масштаба сначала выведет из эксплуатации узлы, а затем удаляет их из кластера.

## <a name="get-started"></a>Начать

### <a name="create-a-cluster-with-load-based-autoscaling"></a>Создание кластера с автомасштабированием на основе нагрузки

Чтобы использовать Автомасштабирование в кластере, необходимо включить параметр **включить Автомасштабирование** при создании кластера. 

Чтобы включить функцию автомасштабирования с масштабированием на основе нагрузки, выполните следующие действия в рамках обычного процесса создания кластера:

1. На вкладке **Настройка и цены** установите флажок **включить Автомасштабирование** .
1. Выберите **на основе нагрузки** **тип автомасштабирования**.
1. Введите нужные значения для следующих свойств:  

    * Начальное **число узлов** для **рабочего узла**.
    * **Минимальное** число рабочих узлов.
    * **Максимальное** число рабочих узлов.

    ![Включить автомасштабирование на основе загрузки рабочих узлов](./media/hdinsight-autoscale-clusters/azure-portal-cluster-configuration-pricing-autoscale.png)

Начальное количество рабочих узлов должно быть в диапазоне между максимальным и минимальным количеством. Это значение определяет начальный размер кластера при его создании. Минимальное число рабочих узлов должно быть равно трем или более. . Масштабирование кластера до трех узлов может привести к зависанию в защищенном режиме из-за недостаточной репликации файлов. Дополнительные сведения см. в статье о [залипания в защищенном режиме]( https://docs.microsoft.com/ azure/hdinsight/hdinsight-scaling-best-practices#getting-stuck-in-safe-mode) .

### <a name="create-a-cluster-with-schedule-based-autoscaling"></a>Создание кластера с автомасштабированием на основе расписания

Чтобы включить функцию автомасштабирования с масштабированием на основе расписания, выполните следующие действия в рамках обычного процесса создания кластера:

1. На вкладке **Настройка и цены** установите флажок **включить Автомасштабирование** .
1. Введите **количество узлов** для **рабочего узла**, которое управляет пределом масштабирования кластера.
1. Выберите параметр **Расписание — на основе** **типа автомасштабирования**.
1. Нажмите кнопку **настроить** , чтобы открыть окно **Конфигурация автомасштабирования** .
1. Выберите часовой пояс и нажмите кнопку **+ Добавить условие** .
1. Выберите дни недели, к которым должно применяться новое условие.
1. Измените время, когда условие должно вступить в силу, и число узлов, до которого будет масштабироваться кластер.
1. При необходимости добавьте дополнительные условия.

    ![Включить создание на основе расписания рабочих узлов](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-schedule-creation.png)

Число узлов должно быть в диапазоне от 3 до максимального количества рабочих узлов, введенных перед добавлением условий.

### <a name="final-creation-steps"></a>Заключительные шаги создания

Для масштабирования на основе нагрузки и на основе расписания выберите тип виртуальной машины для рабочих узлов, выбрав виртуальную машину из раскрывающегося списка в разделе **размер узла**. После выбора типа виртуальной машины для каждого типа узла можно просмотреть диапазон оценочных затрат для всего кластера. Настройте типы виртуальных машин в соответствии с бюджетом.

![Включить размер узла автомасштабирования на основе расписания рабочего узла](./media/hdinsight-autoscale-clusters/azure-portal-cluster-configuration-pricing-vmsize.png)

Ваша подписка имеет квоту емкости для каждого региона. Общее число ядер на головных узлах в сочетании с максимальным числом рабочих узлов не может превышать квоту емкости. Тем не менее эта квота — нестрогое ограничение. Вы всегда можете создать запрос в службу поддержки, чтобы легко ее повысить.

> [!Note]  
> Если превышено ограничение общей квоты ядра, вы получите сообщение об ошибке: "максимальный узел превышает доступные ядра в этом регионе. Выберите другой регион или обратитесь в службу поддержки, чтобы увеличить квоту."

Дополнительные сведения о создании кластера HDInsight с помощью портала Azure см. в статье [Создание кластеров под управлением Linux в HDInsight с помощью портала Azure](hdinsight-hadoop-create-linux-clusters-portal.md).  

### <a name="create-a-cluster-with-a-resource-manager-template"></a>Создание кластера с помощью шаблона Resource Manager

#### <a name="load-based-autoscaling"></a>Автомасштабирование на основе загрузки

Вы можете создать кластер HDInsight с автомасштабированием на основе нагрузки с помощью шаблона Azure Resource Manager, добавив `autoscale` узел в раздел `computeProfile` > `workernode` со свойствами `minInstanceCount` и `maxInstanceCount`, как показано в фрагменте кода JSON ниже.

```json
{
  "name": "workernode",
  "targetInstanceCount": 4,
  "autoscale": {
      "capacity": {
          "minInstanceCount": 3,
          "maxInstanceCount": 10
      }
  },
  "hardwareProfile": {
      "vmSize": "Standard_D13_V2"
  },
  "osProfile": {
      "linuxOperatingSystemProfile": {
          "username": "[parameters('sshUserName')]",
          "password": "[parameters('sshPassword')]"
      }
  },
  "virtualNetworkProfile": null,
  "scriptActions": []
}
```

Дополнительные сведения о создании кластеров с помощью шаблонов Resource Manager см. в статье [Создание кластеров Apache Hadoop в HDInsight с помощью шаблонов Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md).  

#### <a name="schedule-based-autoscaling"></a>Автомасштабирование на основе расписания

Вы можете создать кластер HDInsight с автоматическим масштабированием на основе расписания Azure Resource Manager шаблон, добавив `autoscale` узел в раздел `computeProfile` > `workernode`. Узел `autoscale` содержит `recurrence` с `timezone` и `schedule`, описывающие, когда будет выполнено изменение.

```json
{
  "autoscale": {
    "recurrence": {
      "timeZone": "Pacific Standard Time",
      "schedule": [
        {
          "days": [
            "Monday",
            "Tuesday",
            "Wednesday",
            "Thursday",
            "Friday"
          ],
          "timeAndCapacity": {
            "time": "11:00",
            "minInstanceCount": 10,
            "maxInstanceCount": 10
          }
        },
      ]
    }
  },
  "name": "workernode",
  "targetInstanceCount": 4,
}
```

### <a name="enable-and-disable-autoscale-for-a-running-cluster"></a>Включение и отключение автомасштабирования для работающего кластера

#### <a name="using-the-azure-portal"></a>Использование портала Azure

Чтобы включить Автомасштабирование в работающем кластере, выберите **Размер кластера** в разделе " **Параметры**". Затем щелкните **включить Автомасштабирование**. Выберите нужный тип автомасштабирования и введите параметры масштабирования на основе нагрузки или по расписанию. Наконец, нажмите кнопку **Сохранить**.

![Включение автомасштабирования на основе расписания рабочих узлов в кластере](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-enable-running-cluster.png)

#### <a name="using-the-rest-api"></a>Использование REST API

Чтобы включить или отключить Автомасштабирование в работающем кластере с помощью REST API, выполните запрос POST к конечной точке автомасштабирования, как показано в следующем фрагменте кода:

```
https://management.azure.com/subscriptions/{subscription Id}/resourceGroups/{resourceGroup Name}/providers/Microsoft.HDInsight/clusters/{CLUSTERNAME}/roles/workernode/autoscale?api-version=2018-06-01-preview
```

Используйте соответствующие параметры в полезных данных запроса. Полезные данные JSON, приведенные ниже, можно использовать для включения автомасштабирования. Используйте `{autoscale: null}` полезных данных, чтобы отключить Автомасштабирование.

```json
{ autoscale: { capacity: { minInstanceCount: 3, maxInstanceCount: 2 } } }
```

Полное описание всех параметров полезных данных см. в предыдущем разделе о [включении автомасштабирования на основе загрузки](#load-based-autoscaling) .

## <a name="best-practices"></a>Рекомендации

### <a name="choosing-load-based-or-schedule-based-scaling"></a>Выбор масштаба на основе нагрузки или по расписанию

Прежде чем принимать решение о выборе режима, примите во внимание следующие факторы.

* Включить Автомасштабирование во время создания кластера.
* Минимальное число узлов должно быть не менее трех.
* Дисперсия нагрузки: выполняет загрузку кластера в определенное время в определенные дни. Если нет, лучше использовать планирование с учетом нагрузки.
* Требования к соглашению об уровне обслуживания: масштабирование автомасштабирования активно, а не прогнозное. Будет ли существовать достаточная задержка между началом загрузки и увеличением размера кластера на целевой. Если соблюдены требования соглашения об уровне обслуживания, а загрузка является фиксированной известной моделью, лучшим вариантом является "расписание на основе".

### <a name="consider-the-latency-of-scale-up-or-scale-down-operations"></a>Учитывайте задержку операций увеличения или уменьшения масштаба.

Выполнение операции масштабирования может занять от 10 до 20 минут. При настройке настроенного расписания запланируйте эту задержку. Например, если требуется, чтобы размер кластера был 20 в 9:00 AM, задайте для триггера расписания более раннее время, например 8:30 AM, чтобы операция масштабирования была выполнена 9:00.

### <a name="preparation-for-scaling-down"></a>Подготовка к уменьшению масштаба

Во время процесса масштабирования кластера, автомасштабирование приведет к списанию узлов в соответствие с целевым размером. Если на этих узлах выполняются задачи, автомасштабирование будет ожидать завершения задач. Так как каждый рабочий узел также обслуживает роль в HDFS, временные данные будут перемещены на оставшиеся узлы. Поэтому следует убедиться, что на оставшихся узлах достаточно места для размещения всех временных данных.

Выполняемые задания будут по-прежнему выполняться и завершены. Ожидающие задания будут ожидать расписанию как обычные с меньшим числом доступных рабочих узлов.

### <a name="minimum-cluster-size"></a>Минимальный размер кластера

Не уменьшайте размер кластера до трех узлов. Масштабирование кластера до трех узлов может привести к зависанию в защищенном режиме из-за недостаточной репликации файлов. Дополнительные сведения см. в статье о [залипания в защищенном режиме]( https://docs.microsoft.com/ azure/hdinsight/hdinsight-scaling-best-practices#getting-stuck-in-safe-mode) .

## <a name="monitoring"></a>Мониторинг

### <a name="cluster-status"></a>Состояние кластера

Состояние кластера, указанное в портал Azure, может помочь в мониторинге действий автомасштабирования.

![Включить состояние кластера автомасштабирования на основе рабочей нагрузки для рабочих узлов](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-cluster-status.png)

Все сообщения о состоянии кластера, которые вы можете увидеть, описаны в списке ниже.

| Состояние кластера | Пояснение |
|---|---|
| Выполнение | Кластер работает нормально. Все предыдущие действия автомасштабирования успешно завершены. |
| Обновить  | Выполняется обновление конфигурации автомасштабирования кластера.  |
| Конфигурация HDInsight  | Выполняется операция увеличения или уменьшения масштаба кластера.  |
| Ошибка обновления  | В HDInsight возникли проблемы во время обновления конфигурации автомасштабирования. Клиенты могут либо повторить обновление, либо отключить Автомасштабирование.  |
| Ошибка  | В кластере что-то не так, и его использование невозможно. Удалите этот кластер и создайте новый.  |

Чтобы просмотреть текущее число узлов в кластере, перейдите на диаграмму **Размер кластера** на странице **обзора** кластера или щелкните **Размер кластера** в разделе " **Параметры**".

### <a name="operation-history"></a>Журнал операций

Вы можете просматривать журнал масштабирования и масштабирования кластера в составе метрик кластера. Можно также перечислить все действия масштабирования за последний день, неделю или другой период времени.

Выберите **метрики** в разделе **мониторинг**. Затем в раскрывающемся списке **Метрика** выберите **Добавить метрику** и **число активных рабочих ролей** . Нажмите кнопку в правом верхнем углу, чтобы изменить диапазон времени.

![Включить метрику автомасштабирования на основе расписания рабочих узлов](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-chart-metric.png)

## <a name="next-steps"></a>Дальнейшие действия

* Прочитайте рекомендации по масштабированию кластеров вручную [здесь](hdinsight-scaling-best-practices.md).
