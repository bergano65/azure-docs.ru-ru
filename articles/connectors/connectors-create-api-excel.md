---
title: Подключение к Excel Online — Azure Logic Apps
description: Сведения об управлении данными с помощью интерфейсов REST API Excel Online и Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
manager: carmonm
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 08/23/2018
tags: connectors
ms.openlocfilehash: 5d0d276096441c780dee4f8b1e95442a1d7e7b25
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/27/2019
ms.locfileid: "70050917"
---
# <a name="manage-excel-online-data-with-azure-logic-apps"></a>Управление данными Excel Online с помощью Azure Logic Apps

С помощью Azure Logic Apps и соединителя Excel Online можно создать автоматизированные задачи и рабочие процессы, основанные на данных в Excel Online для бизнеса или OneDrive. Этот соединитель выполняет действия, которые помогут вам работать с данными и управлять электронными таблицами, например:

* создавать листы и таблицы;
* получать листы, таблицы и строки, а также управлять ими;
* добавлять отдельные строки и ключевые столбцы.

Затем выходные данные этих действий можно использовать с действиями для других служб. Например, если есть действие, которое создает листы каждую неделю, можно использовать другое действие, которое отправляет подтверждение по электронной почте с помощью соединителя Office 365 Outlook.

Если вы не знакомы с приложениями логики, ознакомьтесь со статьей [Что такое Azure Logic Apps](../logic-apps/logic-apps-overview.md).

> [!NOTE]
> Соединители [Excel для бизнеса Online](/connectors/excelonlinebusiness/) и [Excel Online для OneDrive](/connectors/excelonline/) работают с Azure Logic Apps и отличаются от [соединителя Excel для PowerApps](/connectors/excel/).

## <a name="prerequisites"></a>Предварительные требования

* Подписка Azure. Если у вас еще нет подписки Azure, [зарегистрируйтесь для получения бесплатной учетной записи Azure](https://azure.microsoft.com/free/).

* [Учетная запись Office 365](https://www.office.com/) для рабочей или пользовательской учетной записи Майкрософт.

  Данные Excel могут существовать в качестве файла с разделителями-запятыми (CSV) в папке хранилища, например в OneDrive. 
  Этот же CSV-файл можно использовать с [соединителем неструктурированного файла](../logic-apps/logic-apps-enterprise-integration-flatfile.md).

* Базовые знания [создания приложений логики](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* Приложение логики, из которого необходимо получить доступ к данным Excel Online. Этот соединитель предоставляет только действия, поэтому для запуска приложения логики выберите отдельный триггер, например **Периодичность**.

## <a name="add-excel-action"></a>Добавление действия с Excel

1. Откройте приложение логики в конструкторе приложений логики на [портале Azure](https://portal.azure.com), если оно еще не открыто.

1. В разделе триггера выберите **Добавить шаг**.

1. В поле поиска введите слово excel в качестве фильтра. В списке действий выберите любое необходимое действие.

1. При появлении запроса войдите в учетную запись Office 365.

   Ваши учетные данные авторизуют приложение логики, чтобы оно могло создать подключение к Excel Online и получить доступ к данным.

1. Предоставьте необходимые сведения для выбранного действия и продолжайте создавать рабочий процесс приложения логики.

## <a name="connector-reference"></a>Справочник по соединителям

Для получения технических сведений, таких как триггеры, действия и ограничения, как описано в файлах OpenAPI (ранее Swagger) соединителя, ознакомьтесь со следующими страницами Справочника по соединителям:

* [Excel Online для бизнеса](/connectors/excelonlinebusiness/)
* [Excel Online для OneDrive](/connectors/excelonline/)

## <a name="next-steps"></a>Следующие шаги

* См. дополнительные сведения о других [соединителях Logic Apps](../connectors/apis-list.md).
