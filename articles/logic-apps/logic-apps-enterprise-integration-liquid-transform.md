---
title: Преобразование данных JSON с помощью жидкостных преобразований
description: Создание преобразований или сопоставлений для сложных преобразований JSON с помощью Logic Apps и шаблона Liquid
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 04/01/2020
ms.openlocfilehash: 0ab9297e772a3b75a077da1c2ae74e5058b2731f
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80657339"
---
# <a name="perform-advanced-json-transformations-with-liquid-templates-in-azure-logic-apps"></a>Выполнение сложных преобразований JSON с помощью шаблонов Liquid в Azure Logic Apps

С помощью встроенных действий обработки данных, таких как **Создание** или **Синтаксический анализ JSON**, в приложениях логики можно выполнять основные преобразования JSON. С помощью [Liquid](https://shopify.github.io/liquid/), который является языком шаблонов с открытым исходным кодом, позволяющим создавать гибкие веб-приложения, можно создавать шаблоны или преобразования для выполнения расширенных преобразований JSON. Шаблон Liquid определяет способпреобразования вывода JSON и поддерживает более сложные преобразования JSON, такие как итерации, контрольные потоки, переменные и так далее.

Прежде чем вы сможете преобразовать Liquid в приложении логики, необходимо сначала определить JSON для отображения JSON с шаблоном Liquid и хранить эту карту в вашей учетной записи интеграции. Из этой статьи можно узнать, как создать и использовать шаблон или сопоставление Liquid.

## <a name="prerequisites"></a>Предварительные требования

* Подписка Azure. Если у вас нет ее, вы можете [зарегистрироваться для получения бесплатной учетной записи Azure](https://azure.microsoft.com/free/).

* Основные знания о [том, как создавать логические приложения](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Базовая [интеграционная учетная запись](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)

* Основные знания о [языке шаблонов Liquid](https://shopify.github.io/liquid/)

## <a name="create-liquid-template-or-map-for-your-integration-account"></a>Создание шаблона или сопоставления Liquid в учетной записи интеграции

1. Для данного примера создайте образец шаблона Liquid, описанный на этом шаге. В шаблоне Liquid вы можете использовать [фильтры Liquid,](https://shopify.github.io/liquid/basics/introduction/#filters)которые используют конвенции [DotLiquid](https://dotliquidmarkup.org/) и именования C.

   > [!NOTE]
   > Убедитесь, что имена фильтров используют *корпус предложения* в шаблоне. В противном случае фильтры не будут работать. Кроме того, карты имеют [ограничения размера файла.](../logic-apps/logic-apps-limits-and-config.md#artifact-capacity-limits)

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

1. На [портале Azure](https://portal.azure.com)из окна поиска `integration accounts`Azure введите и выберите **учетные записи интеграции.**

   ![Найти "Интеграционные счета"](./media/logic-apps-enterprise-integration-liquid-transform/find-integration-accounts.png)

1. Найдите и выберите учетную запись интеграции.

   ![Выбор учетной записи интеграции](./media/logic-apps-enterprise-integration-liquid-transform/select-integration-account.png)

1. На **панели обзора,** под **компонентами,** выберите **Карты**.

    ![Выберите плитку "Карты"](./media/logic-apps-enterprise-integration-liquid-transform/select-maps-tile.png)

1. На панели **«Карты»** **выберите Добавить** и предоставить следующие сведения для карты:

   | Свойство | Значение | Описание | 
   |----------|-------|-------------|
   | **имя**; | `JsonToJsonTemplate` | Это имя сопоставления, в нашем примере это "JsontoJsonTemplate" | 
   | **Тип сопоставления** | **liquid** | Обозначает тип сопоставления. Для преобразования JSON в JSON следует выбрать **Liquid**. | 
   | **Карта** | `SimpleJsonToJsonTemplate.liquid` | Это существующий файл шаблона или сопоставления Liquid, который будет использован для преобразования. В нашем примере это SimpleJsonToJsonTemplate.liquid. Чтобы найти этот файл, используйте средство выбора файлов. Для ограничений по размеру карты [см.](../logic-apps/logic-apps-limits-and-config.md#artifact-capacity-limits) |
   ||| 

   ![Добавление шаблона Liquid](./media/logic-apps-enterprise-integration-liquid-transform/add-liquid-template.png)
    
## <a name="add-the-liquid-action-for-json-transformation"></a>Добавление действия Liquid для преобразования JSON

1. Чтобы [создать пустое приложение логики](../logic-apps/quickstart-create-first-logic-app-workflow.md) на портале Azure, сделайте следующее:

1. В конструкторе приложений логики добавьте [триггер запроса](../connectors/connectors-native-reqres.md#add-request) в приложение логики.

1. В разделе триггера выберите **Добавить шаг**. В поле поиска `liquid` введите фильтр и выберите это действие: **Преобразование JSON в JSON - Liquid**

   ![Поиск и выбор действия Liquid](./media/logic-apps-enterprise-integration-liquid-transform/search-action-liquid.png)

1. Откройте список **Карт** и выберите шаблон Liquid, который в этом примере является "JsonToJsonTemplate".

   ![Выбор сопоставления](./media/logic-apps-enterprise-integration-liquid-transform/select-map.png)

   Если список сопоставлений пуст, скорее всего, приложение логики не связано с учетной записью интеграции. 
   Чтобы настроить связь с учетной записью интеграции, в которой хранится шаблон или сопоставление Liquid, выполните следующие действия.

   1. В меню приложения логики выберите **Параметры рабочего процесса**.

   1. Из списка **учетной записи Select an Integration** выберите учетную запись интеграции и выберите **Сохранить.**

      ![Привязка приложения логики к учетной записи интеграции](./media/logic-apps-enterprise-integration-liquid-transform/link-integration-account.png)

1. Теперь добавьте свойство **Содержимого** в это действие. Откройте **список новых параметров И** выберите **Содержимое.**

   ![Добавление свойства "Содержимое" в действие](./media/logic-apps-enterprise-integration-liquid-transform/add-content-property-to-action.png)

1. Чтобы установить значение свойства **Содержимого,** щелкните внутри окна **Содержимого,** чтобы создать список динамических содержимого. Выберите маркер **тела,** который представляет вывод содержимого тела из триггера.

   ![Выберите маркер "Тело" для значения свойства "Содержание"](./media/logic-apps-enterprise-integration-liquid-transform/select-body.png)

   В итоге действие должно выглядеть приблизительно так, как показано в примере ниже:

   ![Завершено действие "Преобразование JSON в JSON"](./media/logic-apps-enterprise-integration-liquid-transform/finished-transform-action.png)

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

## <a name="next-steps"></a>Дальнейшие действия

* [Дополнительные сведения о Пакете интеграции Enterprise](../logic-apps/logic-apps-enterprise-integration-overview.md "Узнайте больше о пакете корпоративной интеграции")  
* [Дополнительные сведения о сопоставлениях](../logic-apps/logic-apps-enterprise-integration-maps.md "Узнайте о картах интеграции предприятий")  

