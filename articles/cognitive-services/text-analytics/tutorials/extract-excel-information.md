---
title: Извлекайте информацию в Excel с помощью текстовой аналитики и автоматизации питания
titleSuffix: Azure Cognitive Services
description: Узнайте, как извлечь текст Excel без необходимости писать код, используя text Analytics и Power Automate.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 02/27/2019
ms.author: aahi
ms.openlocfilehash: fd70fe14d3765fb7c21b92f62b4d73564176baa2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78201191"
---
# <a name="extract-information-in-excel-using-text-analytics-and-power-automate"></a>Извлекайте информацию в Excel с помощью текстовой аналитики и автоматизации питания 

В этом уроке вы создадите поток Power Automate для извлечения текста в таблице Excel без необходимости написания кода. 

Этот поток будет принимать таблицу вопросов, сообщаемых о жилом комплексе, и классифицировать их на две категории: сантехника и другие. Он также будет извлекать имена и номера телефонов арендаторов, которые послали их. Наконец, поток будет пригоствыть к листу Excel. 

Из этого руководства вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * Используйте Power Automate для создания потока
> * Загрузка данных Excel от OneDrive для бизнеса
> * Извлеките текст из Excel и отправьте его на API аналитики текста 
> * Используйте информацию из API для обновления листа Excel.

## <a name="prerequisites"></a>Предварительные требования

- Учетная запись Microsoft Azure. ([начните работу в бесплатной пробной версии](https://azure.microsoft.com/free/) или [войдите в существующую](https://portal.azure.com/)).
- Ресурс текстовой аналитики. Если у вас его нет, вы можете создать его на [портале Azure](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) и использовать бесплатный уровень для завершения этого учебника.
- [Ключ и конечная точка,](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) которая была создана для вас во время регистрации.
- Электронная таблица, содержащая проблемы с арендаторами. Примеры данных предоставляются на GitHub
- Office 365, с OneDrive для бизнеса.

## <a name="add-the-excel-file-to-onedrive-for-business"></a>Добавьте файл Excel в OneDrive для бизнеса

Скачать пример файла Excel с [GitHub](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/TextAnalytics/sample-data/ReportedIssues.xlsx). Этот файл должен храниться в вашей учетной записи OneDrive for Business.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/example-data.png" alt-text="Примеры из файла Excel.":::

О проблемах сообщается в сыром тексте. Мы будем использовать имя объекта Text Analytics API для извлечения имени и номера телефона. Затем поток будет искать слово "сантехника" в описании, чтобы классифицировать вопросы. 

## <a name="create-a-new-power-automate-workflow"></a>Создание нового рабочего процесса Power Automate

Перейдите на [сайт Power Automate](https://preview.flow.microsoft.com/)и ввизай. Затем нажмите **Создать** и **запланированный поток**.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/flow-creation.png" alt-text="Экран создания потока.":::


На странице **сборки запланированного потока** инициализируйте поток следующими полями:

|Поле |Значение  |
|---------|---------|
|**Имя потока**     | **Запланированный обзор** или другое имя.         |
|**Начиная**     |  Введите текущую дату и время.       |
|**Повторите каждый**     | **1 час**.        |

## <a name="add-variables-to-the-flow"></a>Добавление переменных в поток

> [!NOTE]
> Если вы хотите увидеть изображение завершенного потока, вы можете загрузить его с [GitHub.](https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/TextAnalytics/flow-diagrams) 

Создавайте переменные, представляющие информацию, которая будет добавлена в файл Excel. Нажмите **Новый шаг** и ищите **переменную Инициализации.** Сделайте это четыре раза, чтобы создать четыре переменные.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/initialize-variables.png" alt-text="Инициализовать переменные.":::

Добавьте следующую информацию к созданным переменным. Они представляют собой столбцы файла Excel. Если какие-либо переменные свернуты, вы можете нажать на них, чтобы расширить их.

| Действие |name   | Тип | Значение |
|---------|---------|---|---|
| Инициализация переменной | var_person | Строка | Модель Person |
| Инициализация переменной 2 | var_phone | Строка | Phone_Number |
| Инициализация переменной 3 | var_plumbing | Строка | Сантехника |
| Инициализация переменной 4 | var_other | Строка | Другие | 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/flow-variables.png" alt-text="информация, содержащаяся в переменных потока":::

## <a name="read-the-excel-file"></a>Читать файл Excel

Нажмите **«Новый шаг»** и введите **Excel,** затем выберите **строки списка, присутствующие в таблице,** из списка действий.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/list-excel-rows.png" alt-text="добавить excel строки.":::

Добавьте файл Excel в поток, заполнив поля в этом действии. Этот учебник требует, чтобы файл был загружен на OneDrive для бизнеса.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/list-excel-rows-options.png" alt-text="добавить excel строки.":::

Нажмите **новый шаг** и добавьте **применить к каждому** действию.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/add-apply-action.png" alt-text="добавить команду применить.":::

Нажмите на **выберите выход из предыдущего шага.** В появляется поле динамического содержимого, выберите **значение.**

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/select-output.png" alt-text="Выберите выход из файла Excel.":::

## <a name="send-a-request-to-the-text-analytics-api"></a>Отправить запрос на API аналитики текста

Если вы еще не сделали этого, необходимо создать [ресурс Text Analytics](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) на портале Azure.

### <a name="create-a-text-analytics-connection"></a>Создание подключения к текстовой аналитике

В **применить к каждому,** нажмите **Добавить действие**. Перейдите на **страницу ресурса** Text Analytics на портале Azure и получите ключ и конечную точку для ресурса Text Analytics.

В потоке введите следующую информацию для создания нового соединения Text Analytics.

> [!NOTE]
> Если вы уже создали подключение Text Analytics и хотите изменить данные о подключении, нажмите на эллипсис в правом верхнем углу и нажмите **кнопку «Добавить новое соединение».**

| Поле           | Значение                                                                                                             |
|-----------------|-------------------------------------------------------------------------------------------------------------------|
| Имя подключения | Имя для подключения к ресурсу Text Analytics. Например, `TAforPowerAutomate`. |
| Ключ учетной записи     | Ключ для ресурса Text Analytics.                                                                                   |
| URL-адрес сайта        | Конечная точка для ресурса Text Analytics.                                                       |

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/add-credentials.png" alt-text="Добавьте учетные данные Text Analytics в свой поток.":::

## <a name="extract-the-excel-content"></a>Извлекайте содержание Excel 

После создания соединения ищите **текстовые аналитики** и выберите **сущности.** Это позволит извлечь информацию из столбца описания проблемы.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/extract-info.png" alt-text="Добавьте учетные данные Text Analytics в свой поток.":::

Нажмите в поле **текста** и выберите **Описание** из появляется динамического содержимого. Введите `en` для языка. (Нажмите Показать расширенные варианты, если вы не видите язык)

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/description-from-dynamic-content.png" alt-text="Добавьте учетные данные Text Analytics в свой поток.":::


## <a name="extract-the-person-name"></a>Извлеките имя человека

Далее мы найдем тип сущности в выводе Text Analytics. В рамках **Применить к каждому,** нажмите **Добавить действие**, и создать еще одно применение к **каждому** действию. Нажмите внутри текстового окна и выберите **сущности** в появляетсяок окна динамического содержимого.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/add-apply-action-2.png" alt-text="Добавьте учетные данные Text Analytics в свой поток.":::

В недавно созданном **применить к каждому 2** действия, нажмите **Добавить действие**, и добавить элемент управления **состоянием.**

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/create-condition.png" alt-text="Добавьте учетные данные Text Analytics в свой поток.":::

В окне условия нажмите на первый текстовый ящик. В окне динамического содержимого вынань **сущности** и выберите его.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/choose-entities-value.png" alt-text="Добавьте учетные данные Text Analytics в свой поток.":::

Убедитесь, что вторая коробка установлена **равна.** Затем выберите третью коробку и ищите `var_person` в окне динамического содержимого. 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/choose-variable-value.png" alt-text="Добавьте учетные данные Text Analytics в свой поток.":::

В состоянии **Если да,** введите Excel, затем выберите **обновление строки.**

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/yes-column-action.png" alt-text="Добавьте учетные данные Text Analytics в свой поток.":::

Введите информацию Excel и обновите поля **Key Column,** **Key Value** и **PersonName.** Это позволит приспособить имя, обнаруженное API, к листу Excel. 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/yes-column-action-options.png" alt-text="Добавьте учетные данные Text Analytics в свой поток.":::

## <a name="get-the-phone-number"></a>Получить номер телефона

Свести к минимуму **применение к каждому 2** действию, нажав на имя. Затем добавьте еще одно **Apply к каждому** действию, как и раньше. он будет **называться Применить к каждому 3**. Выберите текстовое поле и добавьте **сущности** в качестве вывода для этого действия. 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/add-apply-action-3.png" alt-text="Добавьте учетные данные Text Analytics в свой поток.":::

В **рамках Применения к каждому 3**, добавить управление **состоянием.** Он будет называться **Условие 2**. В первом текстовом поле ищите и добавляйте **тип сущностей** из окна динамического содержимого. Убедитесь, что центральная коробка **установлена равна.** Затем, в правом текстовом поле, введите `var_phone`. 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/condition-2-options.png" alt-text="Добавьте учетные данные Text Analytics в свой поток.":::

В состоянии **Если да,** добавьте действие **строки обновления.** Затем введите информацию, как мы сделали выше, для телефонных номеров столбца листа Excel. Это позволит придать номер телефона, обнаруженный API, к листу Excel. 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/condition-2-yes-column.png" alt-text="Добавьте учетные данные Text Analytics в свой поток.":::


## <a name="get-the-plumbing-issues"></a>Получить сантехника вопросы

Свести к минимуму **применить к каждому 3,** нажав на имя. Затем создайте еще одно **apply к каждому** в родительском действии. Выберите текстовое поле и добавьте **Сущности** в качестве вывода для этого действия из окна динамического содержимого. 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/add-apply-action-4.png" alt-text="Добавьте учетные данные Text Analytics в свой поток.":::


Далее поток проверит, содержит ли описание проблемы из строки таблицы Excel слово "сантехника". Если да, то он добавит "сантехника" в столбце IssueType. Если нет, мы вветем "другие".

Внутри **Применения к каждому 4** действию добавьте элемент управления **состоянием.** Он будет называться **Условие 3**. В первом текстовом поле ищите описание **из** файла Excel, используя окно динамического содержимого. Убедитесь, что центральная коробка говорит **содержит**. Затем, в правом текстовом `var_plumbing`поле, найти и выбрать . 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/condition-3-options.png" alt-text="Добавьте учетные данные Text Analytics в свой поток.":::


В условиях **If yes** нажмите **«Добавить действие»** и выберите **строку «Обновление».** Затем введите информацию, как и раньше. В столбце IssueType `var_plumbing`выберите . Это будет применяться "сантехника" этикетки на строку.

В **условии, если нет,** нажмите **Добавить действие**и выберите **обновление строки.** Затем введите информацию, как и раньше. В столбце IssueType `var_other`выберите . Это будет применяться "другой" ярлык к строке.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/plumbing-issue-condition.png" alt-text="Добавьте учетные данные Text Analytics в свой поток.":::

## <a name="test-the-workflow"></a>Проверка рабочего процесса

В правом верхнем углу экрана нажмите **Сохранить,** а затем **тест**. **Выберите, что я выполню триггерное действие.** Нажмите **Сохранить & тест,** **Выполнить поток**, затем **сделано**.

Файл Excel будет обновляться в вашей учетной записи OneDrive. Он будет выглядеть как ниже.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/updated-excel-sheet.png" alt-text="Обновленная таблица Excel.":::

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Изучите другие решения](../text-analytics-user-scenarios.md)
