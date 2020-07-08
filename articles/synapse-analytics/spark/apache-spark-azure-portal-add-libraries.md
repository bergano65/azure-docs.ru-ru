---
title: Добавление и управление библиотеками для Apache Spark в Azure синапсе Analytics
description: Узнайте, как добавлять библиотеки, используемые Apache Spark, в Azure синапсе Analytics и управлять ими.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: euang
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: cf78a2f7d909fb260c5ff99f80c9d1482f2cd08b
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.contentlocale: ru-RU
ms.lasthandoff: 07/07/2020
ms.locfileid: "86027313"
---
# <a name="add-and-manage-libraries-for-apache-spark-in-azure-synapse-analytics"></a>Добавление и управление библиотеками для Apache Spark в Azure синапсе Analytics

Apache Spark зависит от многих библиотек, предоставляющих функциональные возможности. Эти библиотеки можно дополнить или заменить на дополнительные библиотеки или обновленные версии старых версий.

Пакеты Python можно добавить на уровне пула Spark (Предварительная версия), а пакеты на основе JAR-файлов можно добавить на уровне определения задания Spark.

## <a name="adding-or-updating-python-libraries"></a>Добавление или обновление библиотек Python

Apache Spark в Azure синапсе Analytics содержит полную установку Анакондас, а также дополнительные библиотеки. Список полных библиотек можно найти по адресу [Apache Spark поддержки версий](apache-spark-version-support.md).

При запуске экземпляра Spark создается новая виртуальная среда с использованием этой установки в качестве основы. Кроме того, *requirements.txt* `pip freeze` для обновления виртуальной среды можно использовать файлrequirements.txt(выходные данные команды). Пакеты, перечисленные в этом файле для установки или обновления, загружаются из PyPi во время запуска кластера. Этот файл требований используется при каждом создании экземпляра Spark из пула Spark.

> [!IMPORTANT]
>
> - Если устанавливаемый пакет является большим или занимает много времени, это повлияет на время запуска экземпляра Spark.
> - Пакеты, требующие поддержки компилятора во время установки, например GCC, не поддерживаются.
> - Пакеты не могут быть понижены, только добавляются или обновляются.

### <a name="requirements-format"></a>Формат требований

В следующем фрагменте кода показан формат файла требований. Имя пакета PyPi отображается вместе с точной версией. Этот файл соответствует формату, описанному в справочной документации по [замораживанию PIP](https://pip.pypa.io/en/stable/reference/pip_freeze/) . В этом примере закрепляется конкретная версия. В этом файле также можно указать версии "не больше" и "меньше".

```
absl-py==0.7.0
adal==1.2.1
alabaster==0.7.10
```

### <a name="python-library-user-interface"></a>Пользовательский интерфейс библиотеки Python

Пользовательский интерфейс для добавления библиотек находится на вкладке **Дополнительные параметры** страницы **Создание пула Apache Spark** на портал Azure.

Отправьте файл конфигурации среды с помощью средства выбора файлов в разделе " **пакеты** " страницы.

![Добавление библиотек Python](./media/apache-spark-azure-portal-add-libraries/add-python-libraries.png "Добавление библиотек Python")

### <a name="verifying-installed-libraries"></a>Проверка установленных библиотек

Чтобы проверить, установлены ли правильные версии правильных библиотек, выполните приведенный ниже код.

```python
import pip #needed to use the pip functions
for i in pip.get_installed_distributions(local_only=True):
    print(i)
```

## <a name="next-steps"></a>Дальнейшие действия

- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
- [Документация по Apache Spark](https://spark.apache.org/docs/2.4.4/)
