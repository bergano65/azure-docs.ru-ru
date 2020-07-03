---
title: Преобразование данных JSON с помощью преобразований жидкостей
description: Создание преобразований или сопоставлений для сложных преобразований JSON с помощью Logic Apps и шаблона Liquid
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 04/01/2020
ms.openlocfilehash: d2598dfe9d7972dcb764abf4a1239613a1e8417a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80879179"
---
# <a name="perform-advanced-json-transformations-with-liquid-templates-in-azure-logic-apps"></a>Выполнение сложных преобразований JSON с помощью шаблонов Liquid в Azure Logic Apps

С помощью встроенных действий обработки данных, таких как **Создание** или **Синтаксический анализ JSON**, в приложениях логики можно выполнять основные преобразования JSON. С помощью [Liquid](https://shopify.github.io/liquid/), который является языком шаблонов с открытым исходным кодом, позволяющим создавать гибкие веб-приложения, можно создавать шаблоны или преобразования для выполнения расширенных преобразований JSON. Шаблон ликвидности определяет способ преобразования выходных данных JSON и поддерживает более сложные преобразования JSON, такие как итерации, потоки управления, переменные и т. д.

Прежде чем можно будет выполнить преобразование «ликвидность» в приложении логики, сначала необходимо определить сопоставление JSON с шаблоном жидкости и сохранить его в учетной записи интеграции. Из этой статьи можно узнать, как создать и использовать шаблон или сопоставление Liquid.

## <a name="prerequisites"></a>Предварительные требования

* Подписка Azure. Если у вас нет ее, вы можете [зарегистрироваться для получения бесплатной учетной записи Azure](https://azure.microsoft.com/free/).

* Основные сведения о [создании приложений логики](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Простая [учетная запись интеграции](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)

* Основные сведения о [языке шаблона жидкости](https://shopify.github.io/liquid/)

## <a name="create-liquid-template-or-map-for-your-integration-account"></a>Создание шаблона или сопоставления Liquid в учетной записи интеграции

1. Для данного примера создайте образец шаблона Liquid, описанный на этом шаге. В шаблоне ликвидности можно использовать [фильтры жидкостей](https://shopify.github.io/liquid/basics/introduction/#filters), которые используют соглашения об именовании [DotLiquid](https://github.com/dotliquid/dotliquid) и C#.

   > [!NOTE]
   > Убедитесь, что имена фильтров используют *регистр предложений* в шаблоне. В противном случае фильтры не будут работать. Кроме того, карты имеют [ограничения на размер файлов](../logic-apps/logic-apps-limits-and-config.md#artifact-capacity-limits).

   ```json
   {%- assign deviceList = content.devices | Split: ', ' -%}
   
   {
      "fullName": "{{content.firstName | Append: ' ' | Append: content.lastName}}",
      "firstNameUpperCase": "{{content.firstName | Upcase}}",
      "phoneAreaCode": "{{content.phone | Slice: 1, 3}}",
      "devices" : [
         {%- for device in deviceList -%}
            {%- if forloop.Last == true -%}
            "{{device}}"
            {%- else -%}
            "{{device}}",
            {%- endif -%}
        {%- endfor -%}
        ]
   }
   ```

1. В [портал Azure](https://portal.azure.com)в поле поиска Azure введите `integration accounts`и выберите **учетные записи интеграции**.

   ![Найти "учетные записи интеграции"](./media/logic-apps-enterprise-integration-liquid-transform/find-integration-accounts.png)

1. Найдите и выберите учетную запись интеграции.

   ![Выбор учетной записи интеграции](./media/logic-apps-enterprise-integration-liquid-transform/select-integration-account.png)

1. В области **Обзор** в разделе **компоненты**выберите **Maps**.

    ![Выберите плитку "карты"](./media/logic-apps-enterprise-integration-liquid-transform/select-maps-tile.png)

1. На панели " **карты** " выберите **Добавить** и укажите следующие сведения для карты:

   | Свойство | Значение | Описание | 
   |----------|-------|-------------|
   | **имя**; | `JsonToJsonTemplate` | Это имя сопоставления, в нашем примере это "JsontoJsonTemplate" | 
   | **Тип сопоставления** | **liquid** | Обозначает тип сопоставления. Для преобразования JSON в JSON следует выбрать **Liquid**. | 
   | **Таблица** | `SimpleJsonToJsonTemplate.liquid` | Это существующий файл шаблона или сопоставления Liquid, который будет использован для преобразования. В нашем примере это SimpleJsonToJsonTemplate.liquid. Чтобы найти этот файл, используйте средство выбора файлов. Ограничения размера карт см. в разделе [ограничения и конфигурация](../logic-apps/logic-apps-limits-and-config.md#artifact-capacity-limits). |
   ||| 

   ![Добавить шаблон ликвидности](./media/logic-apps-enterprise-integration-liquid-transform/add-liquid-template.png)
    
## <a name="add-the-liquid-action-for-json-transformation"></a>Добавление действия Liquid для преобразования JSON

1. Чтобы [создать пустое приложение логики](../logic-apps/quickstart-create-first-logic-app-workflow.md) на портале Azure, сделайте следующее:

1. В конструкторе приложений логики добавьте [триггер запроса](../connectors/connectors-native-reqres.md#add-request) в приложение логики.

1. В разделе триггера выберите **Добавить шаг**. В поле поиска введите `liquid` фильтр и выберите это действие: **преобразовать JSON в JSON-жидкость**

   ![Поиск и выбор действия Liquid](./media/logic-apps-enterprise-integration-liquid-transform/search-action-liquid.png)

1. Откройте список **карт** и выберите шаблон ликвидности, который в этом примере — «JsonToJsonTemplate».

   ![Выбор сопоставления](./media/logic-apps-enterprise-integration-liquid-transform/select-map.png)

   Если список сопоставлений пуст, скорее всего, приложение логики не связано с учетной записью интеграции. 
   Чтобы настроить связь с учетной записью интеграции, в которой хранится шаблон или сопоставление Liquid, выполните следующие действия.

   1. В меню приложения логики выберите **Параметры рабочего процесса**.

   1. В списке **выберите учетную запись интеграции** выберите учетную запись интеграции и нажмите кнопку **сохранить**.

      ![Привязка приложения логики к учетной записи интеграции](./media/logic-apps-enterprise-integration-liquid-transform/link-integration-account.png)

1. Теперь добавьте свойство **Content** в это действие. Откройте список **Добавить новый параметр** и выберите **содержимое**.

   ![Добавление свойства "Content" в действие](./media/logic-apps-enterprise-integration-liquid-transform/add-content-property-to-action.png)

1. Чтобы задать значение свойства **содержимого** , щелкните внутри поля **содержимого** , чтобы появился список динамического содержимого. Выберите маркер **тела** , который представляет выходные данные основного содержимого триггера.

   ![Выберите маркер "Body" для значения свойства "Content"](./media/logic-apps-enterprise-integration-liquid-transform/select-body.png)

   В итоге действие должно выглядеть приблизительно так, как показано в примере ниже:

   ![Действие "преобразование JSON в JSON" завершено](./media/logic-apps-enterprise-integration-liquid-transform/finished-transform-action.png)

## <a name="test-your-logic-app"></a>Тестирование приложения логики

Передайте в приложение логики входные данные JSON через [Postman](https://www.getpostman.com/postman) или аналогичное средство. Из приложения логики вы получите преобразованные выходные данные JSON примерно такого вида:
  
![Пример выходных данных](./media/logic-apps-enterprise-integration-liquid-transform/example-output-jsontojson.png)

## <a name="more-liquid-action-examples"></a>Дополнительные примеры действия Liquid
Liquid не ограничивается преобразованием формата JSON. Ниже указаны другие действия преобразования, для которых используется Liquid:

* Преобразование JSON в текст.
  
  Шаблон Liquid, используемый в этом примере:
   
   ``` json
   {{content.firstName | Append: ' ' | Append: content.lastName}}
   ```
   Вот пример входных и выходных данных.
  
   ![Пример выходных данных преобразования JSON в текст](./media/logic-apps-enterprise-integration-liquid-transform/example-output-jsontotext.png)

* Преобразование XML в JSON.
  
  Шаблон Liquid, используемый в этом примере:
   
   ``` json
   [{% JSONArrayFor item in content -%}
        {{item}}
    {% endJSONArrayFor -%}]
   ```
   Вот пример входных и выходных данных.

   ![Пример выходных данных преобразования XML в JSON](./media/logic-apps-enterprise-integration-liquid-transform/example-output-xmltojson.png)

* Преобразование XML в текст.
  
  Шаблон Liquid, используемый в этом примере:

   ``` json
   {{content.firstName | Append: ' ' | Append: content.lastName}}
   ```

   Вот пример входных и выходных данных.

   ![Пример выходных данных преобразования XML в текст](./media/logic-apps-enterprise-integration-liquid-transform/example-output-xmltotext.png)

## <a name="next-steps"></a>Дальнейшие шаги

* [Дополнительные сведения о Пакете интеграции Enterprise](../logic-apps/logic-apps-enterprise-integration-overview.md "Дополнительные сведения о Пакет интеграции Enterprise")  
* [Дополнительные сведения о сопоставлениях](../logic-apps/logic-apps-enterprise-integration-maps.md "Сведения о картах интеграции Enterprise")  

