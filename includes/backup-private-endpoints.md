---
author: dcurwin
ms.service: backup
ms.topic: include
ms.date: 03/12/2020
ms.author: dacurwin
ms.openlocfilehash: a3bae0ce3e6ebcf64936d0ca4af4fb702e5ea404
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79137030"
---
Теперь вы можете использовать [Private Endpoints](https://docs.microsoft.com/azure/private-link/private-endpoint-overview) для резервного копирования данных с серверов внутри виртуальной сети в хранилище служб восстановления. Частная конечная точка использует IP-адресное пространство VNET для вашего хранилища. Сетевой трафик между вашими ресурсами внутри виртуальной сети и хранилищем перемещается по вашей виртуальной сети и частной ссылке в сети Microsoft. Это устраняет воздействие из общественного интернета. Частные конечные точки могут использоваться для резервного копирования и восстановления баз данных S'L и SAP HANA, которые работают внутри ваших VMs Azure. Он также может быть использован для ваших серверов с использованием агента MARS.

Резервное копирование Azure VM не требует подключения к Интернету и поэтому не требует частных конечных точек, чтобы позволить изоляцию сети.

>[!NOTE]
> Эта функция в настоящее время в начале использования. Пожалуйста, заполните azbackupnetsec@microsoft.com этот [опрос](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0H3_nezt2RNkpBCUTbWEapUQk5EQ1QxRzVOWDNDS1Y1Q0xLTkdLQ0U0RC4u) и напишите нам, если вы заинтересованы в использовании частных конечных точек для резервного копирования Azure. Возможность использования этой функции подлежит утверждению службой резервного копирования Azure.
