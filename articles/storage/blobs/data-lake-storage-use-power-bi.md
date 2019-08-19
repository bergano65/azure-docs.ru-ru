---
title: Анализ данных в Azure Data Lake Storage 2-го поколения с помощью Power BI | Документация Майкрософт
description: Использование Power BI для анализа данных, хранящихся в Azure Data Lake Storage 2-го поколения
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: conceptual
ms.date: 07/18/2019
ms.author: normesta
ms.reviewer: bensack
ms.openlocfilehash: cb82d47be11570baf7865468c804162a20a7685f
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/08/2019
ms.locfileid: "68855470"
---
# <a name="analyze-data-in-azure-data-lake-storage-gen2-by-using-power-bi"></a>Анализ данных в Azure Data Lake Storage 2-го поколения с помощью Power BI

В этой статье вы узнаете, как использовать Power BI Desktop для анализа и визуализации данных, хранящихся в учетной записи хранения с иерархическим пространством имен (Azure Data Lake Storage 2-го поколения).

## <a name="prerequisites"></a>Предварительные требования

Перед началом работы с этим учебником необходимо иметь следующее:

> [!div class="checklist"]
> * Подписка Azure. См. страницу [бесплатной пробной версии Azure](https://azure.microsoft.com/pricing/free-trial/).
> * Учетная запись хранения с иерархическим пространством имен. Чтобы создать его, выполните [следующие](data-lake-storage-quickstart-create-account.md) инструкции.
> В этой статье предполагается, что вы создали учетную запись с именем `myadlsg2`.
> * Образец файла данных с именем `Drivers.txt` , который находится в вашей учетной записи хранения.
> Вы можете скачать этот пример из [репозитория Git Azure Data Lake](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceDataDrivers.txt), а затем передать этот файл в учетную запись хранения.
> * **Power BI Desktop**. Это средство можно скачать в [Центре загрузки Майкрософт](https://www.microsoft.com/download/details.aspx?id=45331). 

## <a name="create-a-report-in-power-bi-desktop"></a>Создание отчета в Power BI Desktop

1. Запустите Power BI Desktop на своем компьютере.
2. На вкладке ленты **Главная** на ленте щелкните **получить данные**, а затем нажмите кнопку **Дополнительно**.
3. В диалоговом окне **Получение данных** выберите **Azure**, щелкните **Azure Data Lake Store Gen2 (бета-версия)** , а затем нажмите кнопку **подключить**.

    ![Страница «получение данных»](media/data-lake-storage-use-power-bi/get-data-page.png)

4. В диалоговом окне **Azure Data Lake Storage 2-го поколения** можно указать URL-адрес учетной записи Azure Data Lake Storage 2-го поколения, файловой системы или вложенной папки с помощью формата конечных точек в файловой системе. URL-адреса для Data Lake Storage 2-го поколения имеют следующий `https://<accountname>.dfs.core.windows.net/<filesystemname>/<subfolder>` шаблон и нажмите кнопку **ОК**.

    ![URL](media/data-lake-storage-use-power-bi/adls-url.png)

5. В следующем диалоговом окне щелкните **войти** , чтобы войти в учетную запись хранения. Вы будете перенаправлены на страницу входа вашей организации. Следуйте инструкциям для входа в учетную запись.

    ![Страница входа](media/data-lake-storage-use-power-bi/sign-in.png)

6. После успешного входа нажмите кнопку **подключить**.

    ![Страница входа](media/data-lake-storage-use-power-bi/signed-in.png)

7. В следующем диалоговом окне отображаются все файлы по URL-адресу, указанному на шаге 4 выше, включая файл, отправленный в учетную запись хранения. Проверьте сведения и нажмите кнопку **загрузить**.

    ![Файловые системы](media/data-lake-storage-use-power-bi/file-systems.png)

8. После успешной загрузки данных в Power BI на вкладке " **поля** " отобразятся следующие поля.

    ![Вкладка "поля"](media/data-lake-storage-use-power-bi/fields.png)

    Однако для визуализации и анализа данных предпочтительно использовать данные в следующих полях.

    ![Поля](media/data-lake-storage-use-power-bi/preferred-fields.png)

    В следующих шагах мы обновим запрос, чтобы преобразовать импортированные данные в нужный формат.

9. На вкладке **Главная** на ленте щелкните **изменить запросы**.

    ![Запросы](media/data-lake-storage-use-power-bi/queries.png)

10. В **редакторе запросов**в столбце **содержимое** щелкните **двоичный**. Файл будет автоматически обнаружен как CSV, и вы увидите выходные данные, как показано ниже. Данные теперь имеют формат, который можно использовать для создания визуализаций.

    ![Вывод](media/data-lake-storage-use-power-bi/binary.png)

11. На вкладке **Главная** на ленте нажмите кнопку **Закрыть** и **Применить**, а затем нажмите кнопку **Закрыть** и **Применить**.

    ![Закрыть и применить](media/data-lake-storage-use-power-bi/close-apply.png)

12. После обновления запроса на вкладке **Поля** появятся новые поля, доступные для визуализации.

    ![Новые поля](media/data-lake-storage-use-power-bi/new-fields.png)

13. Создадим круговую диаграмму, на которой будет представлено количество водителей в каждом городе данной страны. Для этого выполним указанные ниже действия.

    На вкладке **визуализации** щелкните символ круговой диаграммы.

    ![Визуализации](media/data-lake-storage-use-power-bi/visualizations.png)

    Столбцы, которые мы будем использовать, — это столбец 4 (название города) и столбец 7 (название страны). Перетащите их с вкладки **Поля** на вкладку **Визуализации**, как показано ниже.

    ![Перетащите поля](media/data-lake-storage-use-power-bi/visualizations-drag-fields.png)

    Круговая диаграмма теперь должна выглядеть так, как показано ниже.

    ![Круговая диаграмма](media/data-lake-storage-use-power-bi/pie-chart.png)

14. Выбрав определенную страну в фильтрах уровня страницы, вы можете теперь увидеть количество водителей в каждом городе этой страны. Например, на вкладке **Визуализации** в списке **Фильтры** уровня страницы выберите **Бразилия**.

    ![Фильтры страниц](media/data-lake-storage-use-power-bi/page-filters.png)

15. Круговая диаграмма автоматически обновится, и вы увидите количество водителей в городах Бразилии.

    ![Бразилия](media/data-lake-storage-use-power-bi/pie-chart-updated.png)

16. В меню **Файл** выберите пункт **Сохранить**, чтобы сохранить визуализацию как файл Power BI Desktop.

## <a name="publish-report-to-power-bi-service"></a>Публикация отчета в службе Power BI

Создав визуализации в Power BI Desktop, вы можете поделиться ею с другими пользователями, опубликовав их в служба Power BI. Инструкции см. в разделе [Публикация из Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-upload-desktop-files/).