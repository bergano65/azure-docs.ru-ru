---
title: Подключить к Box
description: Создавайте файлы и управляйте ими с помощью интерфейсов REST API Box и Azure Logic Apps.
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 11/07/2016
tags: connectors
ms.openlocfilehash: 38e061e918d445de07961af1789891f44c59090a
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74789864"
---
# <a name="create-and-manage-files-in-box-with-azure-logic-apps"></a>Создание файлов в Box и управление ими с помощью Azure Logic Apps

В этой статье показано, как вы можете создавать файлы в Box и управлять ими изнутри приложения логики с помощью соединителя Box. Таким образом можно создавать приложения логики, которые автоматизируют задачи и рабочие процессы по управлению файлами, а также позволяют:

* формировать бизнес-процессы на основе данных, получаемых из Box;

* запускать автоматизированные задачи и рабочие процессы при создании или обновлении файлов;

* Выполнение действия, которое копирует файл или удаляет файл.

  Когда эти действия получают ответ, они делают выходные данные доступными для использования другими действиями. 
  Например, при изменении файла в Box можно отправить измененный файл по электронной почте с помощью Office 365.

## <a name="prerequisites"></a>Технические условия

* Учетная запись [Box](https://www.box.com/home).

* Подписка Azure. Если у вас еще нет подписки Azure, [зарегистрируйтесь для получения бесплатной учетной записи Azure](https://azure.microsoft.com/free/). 

* Приложение логики, из которого необходимо получить доступ к учетной записи Box. Чтобы запустить приложение логики с помощью триггера Box, требуется [пустое приложение логики](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* Базовые знания о [создании приложений логики](../logic-apps/quickstart-create-first-logic-app-workflow.md).
Если вы не знакомы с приложениями логики, ознакомьтесь со статьей [Что такое Azure Logic Apps](../logic-apps/logic-apps-overview.md).

## <a name="connector-reference"></a>Справочник по соединителям

Для получения технических сведений, таких как триггеры, действия и ограничения, как описано в файле OpenAPI (ранее Swagger) соединителя, см. [страницу справочника по соединителю](/connectors/box/).

## <a name="next-steps"></a>Дальнейшие действия

* См. дополнительные сведения о других [соединителях Logic Apps](../connectors/apis-list.md).