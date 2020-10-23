---
title: включить файл
description: включить файл
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: include
ms.date: 10/09/2020
ms.author: banders
ms.reviwer: ''
ms.custom: include file
ms.openlocfilehash: fb34b4ef1cf209a15c7e6ac9a59cc11dd4b0056e
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/14/2020
ms.locfileid: "92026735"
---
## <a name="transform-data-before-using-large-usage-files"></a>Преобразование данных перед применением больших файлов использования

Иногда файл использования или сверки слишком велик для открытия. Или может потребоваться лишь часть информации для устранения проблемы. Например, может потребоваться информация только о конкретном ресурсе или потреблении нескольких служб или групп ресурсов. Перед созданием сводных таблиц можно преобразовать данные для их обобщения.

1. Откройте пустую книгу в Excel.
1. В верхнем меню щелкните **Данные** > **Из текстового или CSV-файла**, выберите файл использования, а затем нажмите **Импорт**.
1. В нижней части окна нажмите кнопку **Преобразовать данные**. В новом окне отображается сводка по данным.  
    :::image type="content" source="./media/cost-management-billing-transform-data-before-using-large-usage-files/summarized-data.png" alt-text="Пример отображения сводных данных" lightbox="./media/cost-management-billing-transform-data-before-using-large-usage-files/summarized-data.png" :::
1. Если у вас есть Клиентское соглашение Майкрософт, пропустите этот шаг и переходите к следующему, так как файлы использования MCA обычно содержат заголовки столбцов в первых строках. Подготовьте данные, создав таблицу. Удалите верхние строки, оставляя только заголовки. Выберите **Удалить строки** > **Удалить верхние строки**.  
     :::image type="content" source="./media/cost-management-billing-transform-data-before-using-large-usage-files/remove-top-rows.png" alt-text="Пример отображения сводных данных" :::
1. В окне удаления верхних строк введите количество строк, которые нужно удалить в верхней части. Для EA обычно 2, для CSP — 1. Щелкните **ОК**.
1. Выберите **Использовать первую строку в качестве заголовков**.  
    :::image type="content" source="./media/cost-management-billing-transform-data-before-using-large-usage-files/use-first-row-as-header.png" alt-text="Пример отображения сводных данных" (категория измерения), "Идентификатор экземпляра", "Группа ресурсов". Кроме того, в одном документе можно использовать несколько фильтров. Рекомендуется применить все возможные фильтры, чтобы уменьшить размер документа, что поможет в дальнейшей работе.
1. После применения фильтров выберите **Закрыть и загрузить**.  
    :::image type="content" source="./media/cost-management-billing-transform-data-before-using-large-usage-files/close-and-load.png" alt-text="Пример отображения сводных данных" :::

После загрузки файла отображается таблица с отфильтрованными данными об использовании. Теперь можно создать новую сводную таблицу для устранения проблем с использованием.