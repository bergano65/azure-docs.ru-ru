---
title: Устранение неполадок с выставлением счетов Azure CSP с помощью сводных таблиц файлов использования
description: Эта статья поможет вам устранить неполадки с выставлением счетов для Поставщика облачных речений Azure (CSP) с помощью сводных таблиц, созданных из CSV-файлов использования.
author: banders
ms.reviewer: isvargas
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: troubleshooting
ms.date: 10/09/2020
ms.author: banders
ms.openlocfilehash: 6525d809805da3a19a0efe423306f18d8e67a646
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/14/2020
ms.locfileid: "92026874"
---
# <a name="troubleshoot-csp-billing-issues-with-usage-file-pivot-tables"></a>Устранение неполадок с выставлением счетов CSP с помощью сводных таблиц файлов использования

Эта статья поможет вам устранить неполадки с выставлением счетов для Поставщика облачных речений (CSP) с помощью сводных таблиц в файлах сверки (использования) Центра партнеров. Файлы использования Azure содержат все сведения об использовании и потреблении Azure. Сведения в файле помогут вам понять следующее:

- Как использовать и применять резервирования Azure.
- Как согласовываются сведения в службе "Управление затратами Azure" с выставленным счетом.
- Как устранить неполадки со скачком затрат.
- Как вычислить суммы возмещения для соглашения об уровне обслуживания.

Используя сведения из файлов использования, можно получить более полное представление о проблемах использования и диагностировать их. Файлы использования создаются в формате (CSV) с разделителями-запятыми. Так как файлы использования могут быть большими CSV-файлами, их легче обрабатывать и просматривать как сводные таблицы в приложениях электронных таблиц, таких как Excel. В примерах, приведенных в этой статье, используется Excel, но можно использовать любое приложение для работы с электронными таблицами.

Только администраторы выставления счетов и глобальные администраторы имеют необходимые права доступа для скачивания файлов сверки. Дополнительные сведения см. в разделе [Правила чтения позиций строк в файлах сверки Центра партнеров](/partner-center/use-the-reconciliation-files).

## <a name="get-the-data-and-format-it"></a>Получение данных и их форматирование

Так как файлы использования Azure имеют формат CSV, необходимо подготовить данные для использования в Excel. Чтобы представить эти данные в виде таблицы, выполните следующие действия.

1. Скачайте файл использования, следуя инструкциям, приведенным в разделе [Поиск счета](/partner-center/read-your-bill#find-your-bill).
1. Откройте файл в Excel.
1. Неформатированные данные соответствуют приведенным в следующем примере.  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/raw-csv-data.png" alt-text="Пример неформатированных данных в формате Excel" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/raw-csv-data.png" :::
1. Выберите в таблице первое поле **PartnerID**.
1. Нажмите клавиши CTRL + SHIFT + СТРЕЛКА ВНИЗ, а затем CTRL + SHIFT + СТРЕЛКА ВПРАВО, чтобы выбрать все данные в таблице.
1. В верхнем меню выберите **Вставка** > **Таблица**. В поле "Создать таблицу" выберите **Таблица с заголовками**, а затем нажмите **ОК**.  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/create-table-dialog.png" alt-text="Пример неформатированных данных в формате Excel" :::
1. В верхнем меню выберите **Вставка** > **Сводная таблица**, а затем щелкните **ОК**. Действие создает лист в файле и переходит к области сводной таблицы в правой части листа.  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/pivot-table-fields.png" alt-text="Пример неформатированных данных в формате Excel" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/pivot-table-fields.png" :::

Область полей сводной таблицы — это область перетаскивания. Перейдите к следующему разделу для создания сводной таблицы.

## <a name="create-pivot-table-to-view-azure-costs-by-resources"></a>Создание сводной таблицы для просмотра затрат Azure по ресурсам

В рамках этого раздела вы создадите сводную таблицу, в которой можно устранить неполадки общего использования Azure. С помощью примера таблицы можно узнать, какая служба потребляет больше всего ресурсов. Кроме того, можно просмотреть ресурсы с наибольшей стоимостью и узнать, как взимается плата за услугу.

1. В области полей сводной таблицы перетащите **Service Name** и **Resource** в область **Строки**. Поместите **Resource** под **Service Name**.  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/rows-section.png" alt-text="Пример неформатированных данных в формате Excel" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/rows-section.png" :::
1. Затем поместите **Post-Tax Total** в область **Значения**. Вы также можете использовать столбец Consumed Quantity, чтобы получить сведения о единицах потребления и транзакциях. Например, гигабайты и часы. Или транзакции вместо затрат в разных валютах, таких как USD, EUR и INR.  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/add-pivot-table-fields.png" alt-text="Пример неформатированных данных в формате Excel" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/add-pivot-table-fields.png" :::
1. Теперь у вас есть панель мониторинга для обобщенного исследования потребления. Для конкретной службы можно выполнить фильтрацию с помощью параметров фильтрации в сводной таблице.  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/pivot-table-filter-option-row-label.png" alt-text="Пример неформатированных данных в формате Excel" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/pivot-table-filter-option-row-label.png" :::
    Чтобы отфильтровать второй уровень в сводной таблице, например ресурс, выберите элемент второго уровня в таблице.
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/pivot-table-filter-option-select-field.png" alt-text="Пример неформатированных данных в формате Excel" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/pivot-table-filter-option-select-field.png" :::
1. Чтобы использовать дополнительные фильтры, можно добавить столбцы **SubscriptionID** и **Customer Company Name** в область **Фильтры** и выбрать нужную область.

## <a name="create-a-pivot-table-to-view-azure-usage-by-date"></a>Создание сводной таблицы для просмотра сведений об использовании Azure по датам

В этом разделе описано, как создать сводную таблицу, которая позволит устранить неполадки, связанные с общим использованием Azure, просматривая данные по использованному количеству и дате. Очень полезно выявлять пики в использовании по дате и службе. Кроме того, можно просмотреть ресурсы с наибольшей стоимостью и узнать, как взимается плата за услугу.

Файл сверки содержит две таблицы. Одна расположена сверху (основная таблица), а другая — в нижней части документа. Эта вторая таблица содержит преимущественно ту же информацию, но в ней нет сведений о ценах и затратах. При этом в ней указана дата использования и потребленное количество.

:::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/reconciliation-file-two-tables.png" alt-text="Пример неформатированных данных в формате Excel" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/reconciliation-file-two-tables.png" :::

1. Выполните действия, описанные в разделе [Получение данных и их форматирование](#get-the-data-and-format-it), чтобы создать таблицу Excel с данными в нижней части файла сверки.
1. Когда таблица будет готова и у вас будет лист сводной таблицы, выполните действия из раздела "Создание сводной таблицы для просмотра затрат Azure по ресурсам", чтобы подготовить панель мониторинга. Вместо того чтобы использовать сумму после уплаты налогов (Post-Tax Total), поместите **Consumed quantity** в область **Значения**.
1. Добавьте **Usage Date** в раздел столбцов. Сводная таблица должна выглядеть примерно так, как показано в следующем примере.  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/final-pivot-table-fields.png" alt-text="Пример неформатированных данных в формате Excel" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/final-pivot-table-fields.png" :::
1. Теперь у вас есть панель мониторинга, на которой показано использование для каждой даты. Вы можете развернуть каждый месяц, выбрав символ **+** .

На панели мониторинга отображается потребленное количество единиц, например ГБ, часов и операций передачи.

Чтобы просмотреть цену за день, можно добавить **Resource GUID** в область **Строки**. В верхней таблице добавьте цену за единицу (**ListPrice**) для ресурса. Умножьте **ListPrice** на **Consumed quantity**, чтобы вычислить расходы до уплаты налогов. Суммы должны совпадать.

Для некоторых ресурсов (служб) цены могут меняться в зависимости от потребленного количества. Например, для некоторых ресурсов цена за первые потребленные 100 ГБ выше, чем за последующие гигабайты. При расчете затрат вручную учитывайте такое изменение цены в зависимости от потребления.

## <a name="create-pivot-table-to-view-cost-for-a-specific-resource"></a>Создание сводной таблицы для просмотра затрат по определенному ресурсу

По одному ресурсу может взиматься плата за различные службы. Например, виртуальная машина может повлечь за собой оплату службы вычислений, лицензирования ОС, пропускной способности (передачи данных), использования RI и хранения моментальных снимков. При необходимости просмотра общего использования конкретных ресурсов ниже приведены инструкции по созданию панели мониторинга для просмотра общего использования с помощью файлов использования.

Файлы сверки не содержат сведений, касающихся ресурсов. Поэтому применяется агрегированный файл использования. Обратитесь в [службу поддержки выставления счетов Azure](https://go.microsoft.com/fwlink/?linkid=2083458) с просьбой предоставить вам агрегированный файл использования для вашей подписки. Агрегированные файлы создаются на уровне подписки. Неформатированные данные соответствуют приведенным в следующем примере.

:::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/raw-aggregated-usage-file.png" alt-text="Пример неформатированных данных в формате Excel" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/raw-aggregated-usage-file.png" :::

Файл содержит следующие столбцы.

- **UsageStart** и **UsageEnd**. Дата для каждой позиции строки (каждой единицы использования). Например, каждый день.
- **MeteredResourceID**. В Azure этот параметр соответствует идентификатору единицы измерения.
- **Properties**. Содержит идентификатор экземпляра (имя ресурса) вместе с другими сведениями, например расположением.
- **Quantity**. Потребленное количество в файле сверки.

1. Выберите в таблице первое поле **PartnerID**.  
1. Нажмите клавиши CTRL + SHIFT + СТРЕЛКА ВНИЗ, а затем CTRL + SHIFT + СТРЕЛКА ВПРАВО, чтобы выбрать все данные в таблице.
1. В верхнем меню выберите **Вставка** > **Таблица**. В поле "Создать таблицу" выберите **Таблица с заголовками**, а затем нажмите **ОК**.  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/create-table-dialog.png" alt-text="Пример неформатированных данных в формате Excel" :::
1. В верхнем меню выберите **Вставка** > **Сводная таблица**, а затем щелкните **ОК**. Действие создает лист в файле и переходит к области сводной таблицы в правой части листа.  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/pivot-table-fields-reconciliation.png" alt-text="Пример неформатированных данных в формате Excel" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/pivot-table-fields-reconciliation.png" :::
1. Затем добавьте **MeteredResourceID** в область **Строки**, а **Quantity** — в область **Значения**. Результаты содержат общую информацию об использовании. Для получения дополнительных сведений поместите **UsageEndDateTime** в область **Столбцы**.  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/overall-usage.png" alt-text="Пример неформатированных данных в формате Excel" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/overall-usage.png" :::
1. Чтобы просмотреть общий отчет, добавьте **Properties** в область **Строки** под **MeteredResourceID**. Тогда вы сможете просмотреть полную панель мониторинга по использованию.
1. Чтобы выполнить фильтрацию по определенному ресурсу, добавьте **Properties** в область **Фильтры** и выберите нужное использование. Для поиска имени ресурса можно использовать функцию поиска.
    Чтобы просмотреть затраты на ресурс, найдите общее потребленное количество и умножьте его на цену по прайс-листу. Цена по прайс-листу зависит от идентификатора GUID ресурса (MeteredResourceID). Если ресурс использует несколько идентификаторов MeteredResourceID, необходимо записать общее значение для каждого из них.

[!INCLUDE [Transform data before using large usage files](../../../includes/cost-management-billing-transform-data-before-using-large-usage-files.md)]

## <a name="next-steps"></a>Дальнейшие действия

- [Начало работы со службой "Управление затратами Azure" для партнеров](../costs/get-started-partners.md).