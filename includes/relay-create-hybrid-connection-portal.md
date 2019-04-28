---
author: clemensv
ms.service: service-bus-relay
ms.topic: include
ms.date: 11/09/2018
ms.author: clemensv
ms.openlocfilehash: ceb2626a43ed44338bb0faad475ae2333af2de9e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60553913"
---
Убедитесь, что [создано пространство имен ретранслятора][namespace-how-to].

1. Войдите на [портале Azure](https://portal.azure.com).
2. В меню слева выберите **Все ресурсы**.
3. Выберите пространство имен, в котором нужно создать гибридное подключение. В нашем примере это **mynewns**.  
4. В разделе **Relay namespace** (Пространство имен ретранслятора) выберите **Гибридные подключения**.

    ![Создание гибридного подключения](./media/relay-create-hybrid-connection-portal/create-hc-1.png)

5. В окне обзора пространств имен выберите **+ Гибридное подключение**.
   
    ![Выбор гибридного подключения](./media/relay-create-hybrid-connection-portal/create-hc-2.png)
6. В разделе **Создание гибридного подключения** введите имя гибридного подключения. Не изменяйте остальные значения по умолчанию.
   
    ![Нажмите кнопку "Создать"](./media/relay-create-hybrid-connection-portal/create-hc-3.png)
7. Нажмите кнопку **Создать**.

[namespace-how-to]: ../articles/service-bus-relay/relay-create-namespace-portal.md 