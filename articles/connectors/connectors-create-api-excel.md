---
title: Управление данными, листами и таблицами в Excel Online
description: Управление данными в листах и таблицах в Excel Online для бизнеса или Excel Online для OneDrive с помощью приложений Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 08/23/2018
tags: connectors
ms.openlocfilehash: b1df1219590aa94331a4404b1315eb7231c59699
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75445884"
---
# <a name="manage-excel-online-data-with-azure-logic-apps"></a>Управление данными Excel Online с помощью Azure Logic Apps

С [помощью приложений Azure Logic Apps](../logic-apps/logic-apps-overview.md) и разъема Excel Online for [Business](/connectors/excelonlinebusiness/) или Excel Online [для oneDrive](/connectors/excelonline/) разъема можно создавать автоматизированные задачи и рабочие процессы на основе данных в Excel Online for Business или OneDrive. Этот соединитель выполняет действия, которые помогут вам работать с данными и управлять электронными таблицами, например:

* создавать листы и таблицы;
* получать листы, таблицы и строки, а также управлять ими;
* добавлять отдельные строки и ключевые столбцы.

Затем выходные данные этих действий можно использовать с действиями для других служб. Например, если есть действие, которое создает листы каждую неделю, можно использовать другое действие, которое отправляет подтверждение по электронной почте с помощью соединителя Office 365 Outlook.

Если вы новичок в логических приложениях, просмотрите [Что такое приложения логики Azure?](../logic-apps/logic-apps-overview.md)

> [!NOTE]
> Соединители [Excel для бизнеса Online](/connectors/excelonlinebusiness/) и [Excel Online для OneDrive](/connectors/excelonline/) работают с Azure Logic Apps и отличаются от [соединителя Excel для PowerApps](/connectors/excel/).

## <a name="prerequisites"></a>Предварительные требования

* Подписка Azure. Если у вас еще нет подписки Azure, [зарегистрируйтесь для получения бесплатной учетной записи Azure](https://azure.microsoft.com/free/).

* [Учетная запись Office 365](https://www.office.com/) для рабочей или пользовательской учетной записи Майкрософт.

  Данные Excel могут существовать в качестве файла с разделителями-запятыми (CSV) в папке хранилища, например в OneDrive. 
  Этот же CSV-файл можно использовать с [соединителем неструктурированного файла](../logic-apps/logic-apps-enterprise-integration-flatfile.md).

* Основные знания о [том, как создавать логические приложения](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Приложение логики, из которого необходимо получить доступ к данным Excel Online. Этот соединитель предоставляет только действия, поэтому для запуска приложения логики выберите отдельный триггер, например **Периодичность**.

## <a name="add-excel-action"></a>Добавление действия с Excel

1. Откройте приложение логики в конструкторе приложений логики на [портале Azure](https://portal.azure.com), если оно еще не открыто.

1. В разделе триггера выберите **Добавить шаг**.

1. В поле поиска введите слово excel в качестве фильтра. В списке действий выберите любое необходимое действие.

   > [!NOTE]
   > Дизайнер приложения Логика не может загружать таблицы, которые имеют 100 или более столбцов. Если это возможно, уменьшите количество столбцов в выбранной таблице, чтобы дизайнер мог загрузить таблицу.

1. По запросу вопийте в свой аккаунт Office 365.

   Ваши учетные данные авторизуют приложение логики, чтобы оно могло создать подключение к Excel Online и получить доступ к данным.

1. Предоставьте необходимые сведения для выбранного действия и продолжайте создавать рабочий процесс приложения логики.

## <a name="connector-reference"></a>Справочник по соединителям

Для технических деталей, таких как триггеры, действия и ограничения, описанные в файлах OpenAPI разъема (ранее Swagger), см.

* [Excel Online для бизнеса](/connectors/excelonlinebusiness/)
* [Excel Online для OneDrive](/connectors/excelonline/)

## <a name="next-steps"></a>Дальнейшие действия

* См. дополнительные сведения о других [соединителях Logic Apps](../connectors/apis-list.md).
