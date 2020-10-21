---
title: Устранение неполадок с выставлением счетов EA для Azure с помощью сводных таблиц файлов использования
description: Эта статья поможет вам устранить неполадки с выставлением счетов в рамках Соглашения Enterprise (EA) с помощью сводных таблиц, созданных из CSV-файлов использования.
author: banders
ms.reviewer: isvargas
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: troubleshooting
ms.date: 10/09/2020
ms.author: banders
ms.openlocfilehash: b32bb979176af8a8a3751db8edf9c129caf1002e
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/16/2020
ms.locfileid: "92131995"
---
# <a name="troubleshoot-ea-billing-issues-with-usage-file-pivot-tables"></a>Устранение неполадок с выставлением счетов EA с помощью сводных таблиц файлов использования

Эта статья поможет вам устранить неполадки с выставлением счетов в рамках EA с помощью сводных таблиц, созданных из файлов использования. Файлы использования Azure содержат все сведения об использовании и потреблении Azure. Сведения в файле помогут вам понять следующее:

- Как использовать и применять резервирования Azure.
- Как согласовываются сведения в службе "Управление затратами Azure" с выставленным счетом.
- Как устранить неполадки со скачком затрат.
- Как вычислить суммы возмещения для соглашения об уровне обслуживания.

Используя сведения из файлов использования, можно получить более полное представление о проблемах использования и диагностировать их. Файлы использования создаются в формате (CSV) с разделителями-запятыми. Так как файлы использования могут быть большими CSV-файлами, их легче обрабатывать и просматривать как сводные таблицы в приложениях электронных таблиц, таких как Excel. В примерах, приведенных в этой статье, используется Excel, но можно использовать любое приложение для работы с электронными таблицами.

Только администраторы EA, владельцы учетных записей и администраторы отделов имеют необходимые права доступа для скачивания файлов использования.

## <a name="get-the-data-and-format-it"></a>Получение данных и их форматирование

Так как файлы использования Azure имеют формат CSV, необходимо подготовить данные для использования в Excel. Чтобы представить эти данные в виде таблицы, выполните следующие действия.

1. Скачайте файл "Сведения об использовании версии 2, все расходы (использование и покупки)", следуя инструкциям в разделе [Скачивание сведений об использовании для клиентов EA](./download-azure-invoice-daily-usage-date.md#download-usage-for-ea-customers).
1. Откройте файл в Excel.
1. Неформатированные данные соответствуют приведенным в следующем примере.  
    :::image type="content" source="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/raw-csv-data-ea.png" alt-text="Пример неформатированных данных в формате Excel" lightbox="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/raw-csv-data-ea.png" :::
1. Выберите первое поле в таблице, содержащее заголовок первого столбца, **BillingAccountID**.
1. Нажмите клавиши CTRL + SHIFT + СТРЕЛКА ВНИЗ, а затем CTRL + SHIFT + СТРЕЛКА ВПРАВО, чтобы выбрать все данные в таблице.
1. В верхнем меню выберите **Вставка** > **Таблица**. В поле "Создать таблицу" выберите **Таблица с заголовками**, а затем нажмите **ОК**.  
    :::image type="content" source="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/create-table-dialog.png" alt-text="Пример неформатированных данных в формате Excel" :::
1. В верхнем меню выберите **Вставка** > **Сводная таблица**, а затем щелкните **ОК**. В результате будет создан новый лист в файле и откроется область сводной таблицы в правой части листа.  
    :::image type="content" source="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/pivot-table-fields.png" alt-text="Пример неформатированных данных в формате Excel" lightbox="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/pivot-table-fields.png" :::

Область полей сводной таблицы — это область перетаскивания. Перейдите к следующему разделу для создания сводной таблицы.

## <a name="create-pivot-table-to-view-azure-costs-by-resources"></a>Создание сводной таблицы для просмотра затрат Azure по ресурсам

В рамках этого раздела вы создадите сводную таблицу, в которой можно устранить неполадки общего использования Azure. С помощью примера таблицы можно узнать, какая служба потребляет больше всего ресурсов. Кроме того, можно просмотреть ресурсы с наибольшей стоимостью и узнать, как взимается плата за услугу.

1. В области полей сводной таблицы перетащите пункты **Meter Category** и **Product** в раздел **Строки**. Поместите пункт **Product** под пункт **Meter Category**.  
    :::image type="content" source="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/rows-section.png" alt-text="Пример неформатированных данных в формате Excel" lightbox="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/rows-section.png" :::
1. Затем добавьте столбец **Cost** в раздел **Значения**. Вы также можете использовать столбец Consumed Quantity, чтобы получить сведения о единицах потребления и транзакциях. Например, гигабайты и часы. Или транзакции вместо затрат в разных валютах, таких как USD, EUR и INR.  
    :::image type="content" source="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/add-pivot-table-fields.png" alt-text="Пример неформатированных данных в формате Excel" lightbox="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/add-pivot-table-fields.png" :::
1. Теперь у вас есть панель мониторинга для обобщенного исследования потребления. Для конкретной службы можно выполнить фильтрацию с помощью параметров фильтрации в сводной таблице.  
    :::image type="content" source="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/pivot-table-filter-option-row-label.png" alt-text="Пример неформатированных данных в формате Excel" lightbox="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/pivot-table-filter-option-row-label.png" :::
    Чтобы отфильтровать второй уровень в сводной таблице, например ресурс, выберите элемент второго уровня в таблице.  
    :::image type="content" source="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/pivot-table-filter-option-select-field.png" alt-text="Пример неформатированных данных в формате Excel" lightbox="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/pivot-table-filter-option-select-field.png" :::
1. Перетащите столбец **ResourceID** в область **Строки** под пункт **Product**, чтобы увидеть стоимость каждой службы по ресурсам. Чтобы просмотреть подробные сведения о ценах, просмотрите цену за единицу для своей организации и выполните поиск по **Product** в первом столбце прайс-листа.
1. Добавьте столбец **date** в область **Столбцы**, чтобы увидеть ежедневное потребление для продукта.  
    :::image type="content" source="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/pivot-table-date.png" alt-text="Пример неформатированных данных в формате Excel" lightbox="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/pivot-table-date.png" :::
1. Разворачивание и сворачивание месяцев с помощью символов **+** для столбца каждого месяца.  
    :::image type="content" source="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/pivot-table-month-expand-collapse.png" alt-text="Пример неформатированных данных в формате Excel" lightbox="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/pivot-table-month-expand-collapse.png" :::  
    Добавление столбцов **Cost** и **Quantity** в области **Значения** необязательно. При этом создаются два столбца для каждого раздела данных под каждым месяцем и днем, если столбец даты находится в разделе столбцов сводной таблицы.
1. Чтобы использовать дополнительные фильтры, можно добавить столбцы SubscriptionID, Department, ResourceGroup, Tags или Cost Center в область **Фильтры** и выбрать нужный элемент.

## <a name="create-pivot-table-to-view-cost-for-a-specific-resource"></a>Создание сводной таблицы для просмотра затрат по определенному ресурсу

По одному ресурсу может взиматься плата за различные службы. Например, виртуальная машина может повлечь за собой оплату службы вычислений, лицензирования ОС, пропускной способности (передачи данных), использования RI и хранения моментальных снимков. При необходимости просмотра общего использования конкретных ресурсов ниже приведены инструкции по созданию панели мониторинга для просмотра общего использования с помощью файлов использования.

1. В меню справа перетащите **ResourceID** в раздел **Фильтр** в меню сводной таблицы.
1. Выберите ресурс, по которому нужно просмотреть затраты. Чтобы найти имя ресурса, воспользуйтесь полем **поиска**.
1. Добавьте пункты **Meter Category** и **Product** в раздел "Строки". Поместите пункт **Product** под пункт **Meter Category**.  
    :::image type="content" source="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/pivot-table-fields-meter-category.png" alt-text="Пример неформатированных данных в формате Excel" lightbox="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/pivot-table-fields-meter-category.png" :::
1. Затем добавьте столбец **Cost** в раздел **Значения**. Вы также можете использовать столбец Consumed Quantity, чтобы получить сведения о единицах потребления и транзакциях. Например, гигабайты и часы. Или транзакции вместо затрат в разных валютах, таких как USD, EUR и INR. Теперь у вас есть панель мониторинга, в которой отображаются все службы, которые использует ресурс.
1. Добавьте столбец **Date** в раздел **Столбцы**. Это даст возможность увидеть ежедневное использование.
1. Вы можете разворачивать и уменьшать символы **+** в колонке каждого месяца.  
    :::image type="content" source="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/pivot-table-month-expand-collapse.png" alt-text="Пример неформатированных данных в формате Excel" :::

[!INCLUDE [Transform data before using large usage files](../../../includes/cost-management-billing-transform-data-before-using-large-usage-files.md)]

## <a name="next-steps"></a>Дальнейшие действия

- [Изучение и анализ затрат с помощью функции анализа затрат](../costs/quick-acm-cost-analysis.md).