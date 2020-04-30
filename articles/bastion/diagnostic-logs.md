---
title: Включение и работа с журналами ресурсов Azure бастиона
description: В этой статье вы узнаете, как включить и использовать журналы диагностики бастиона для Azure.
services: bastion
author: charwen
ms.service: bastion
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: charwen
ms.openlocfilehash: 4b71dba02a311b8fa6a16645364f90d7632693a2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82131279"
---
# <a name="enable-and-work-with-bastion-resource-logs"></a>Включение и работа с журналами ресурсов бастиона

Когда пользователи подключаются к рабочим нагрузкам с помощью Azure бастиона, бастиона может вести журнал диагностики удаленных сеансов. После этого можно использовать диагностику для просмотра пользователей, подключенных к рабочей нагрузке, в то время, откуда и другие сведения, связанные с ведением журнала. Чтобы использовать диагностику, необходимо включить журналы диагностики в Azure бастиона. Эта статья поможет вам включить журналы диагностики, а затем просмотреть журналы.

## <a name="enable-the-resource-log"></a><a name="enable"></a>Включение журнала ресурсов

1. В [портал Azure](https://portal.azure.com)перейдите к ресурсу Azure бастиона и выберите **параметры диагностики** на странице бастиона Azure.

   ![параметры диагностики](./media/diagnostic-logs/1diagnostics-settings.png)
2. Выберите **параметры диагностики**, а затем выберите **+ Добавить параметр диагностики** , чтобы добавить назначение для журналов.

   ![добавить параметр диагностики](./media/diagnostic-logs/2add-diagnostic-setting.png)
3. На странице **параметры диагностики** выберите тип учетной записи хранения, которая будет использоваться для хранения журналов диагностики.

   ![Выбор места хранения](./media/diagnostic-logs/3add-storage-account.png)
4. После завершения параметров он будет выглядеть примерно так:

   ![Примеры параметров](./media/diagnostic-logs/4example-settings.png)

## <a name="view-diagnostics-log"></a><a name="view"></a>Просмотр журнала диагностики

Чтобы получить доступ к журналам диагностики, можно напрямую использовать учетную запись хранения, указанную при включении параметров диагностики.

1. Перейдите к ресурсу учетной записи хранения, а затем к **контейнерам**. Вы увидите большой двоичный объект **Insights-Logs-бастионаудитлогс** , созданный в контейнере BLOB-объектов учетной записи хранения.

   ![параметры диагностики](./media/diagnostic-logs/1-navigate-to-logs.png)
2. При переходе внутри контейнера вы увидите в блоге различные папки. Эти папки указывают на иерархию ресурсов для ресурса Azure бастиона.

   ![добавить параметр диагностики](./media/diagnostic-logs/2-resource-h.png)
3. Перейдите к полной иерархии ресурса Azure бастиона, для которого требуется доступ к журналам диагностики. Значения "y =", "ч =", "d =", "h =" и "m =" указывают год, месяц, день, час и минуту соответственно для журналов ресурсов.

   ![Выбор места хранения](./media/diagnostic-logs/3-resource-location.png)
4. Найдите JSON-файл, созданный Azure бастиона, содержащий данные журнала диагностики за период времени, по которому осуществляется переход.

5. Скачайте JSON-файл из контейнера BLOB-объектов хранилища. Ниже приведен пример записи из JSON-файла для справки:

   ```json
   { 
   "time":"2019-10-03T16:03:34.776Z",
   "resourceId":"/SUBSCRIPTIONS/<subscripionID>/RESOURCEGROUPS/MYBASTION/PROVIDERS/MICROSOFT.NETWORK/BASTIONHOSTS/MYBASTION-BASTION",
   "operationName":"Microsoft.Network/BastionHost/connect",
   "category":"BastionAuditLogs",
   "level":"Informational",
   "location":"eastus",
   "properties":{ 
      "userName":"<username>",
      "userAgent":"Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/77.0.3865.90 Safari/537.36",
      "clientIpAddress":"131.107.159.86",
      "clientPort":24039,
      "protocol":"ssh",
      "targetResourceId":"/SUBSCRIPTIONS/<subscripionID>/RESOURCEGROUPS/MYBASTION/PROVIDERS/MICROSOFT.COMPUTE/VIRTUALMACHINES/LINUX-KEY",
      "subscriptionId":"<subscripionID>",
      "message":"Successfully Connected.",
      "resourceType":"VM",
      "targetVMIPAddress":"172.16.1.5",
      "tunnelId":"<tunnelID>"
   },
   "FluentdIngestTimestamp":"2019-10-03T16:03:34.0000000Z",
   "Region":"eastus",
   "CustomerSubscriptionId":"<subscripionID>"
   }
   ```

## <a name="next-steps"></a>Дальнейшие шаги

Ознакомьтесь с [часто задаваемыми вопросами о бастиона](bastion-faq.md).
