---
title: Включить и работать с диагностическими журналами Azure Bastion
description: В этой статье узнайте, как включить и работать с диагностическими журналами Azure Bastion.
services: bastion
author: charwen
ms.service: bastion
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: charwen
ms.openlocfilehash: 97f0cdb1e93ef2ad06d2daa04b2f4893fd5dfac2
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80619281"
---
# <a name="enable-and-work-with-bastion-diagnostic-logs"></a>Включить и работать с диагностическими журналами Бастиона

По мере подключения пользователей к рабочим нагрузкам с помощью Azure Bastion, Bastion может регистрировать диагностику удаленных сеансов. Затем можно использовать диагностику для просмотра, к каким рабочим нагрузкам подключены пользователи, в какое время, откуда и на другой такой релевантной информации о ходе регистрации. Для использования диагностики необходимо включить журналы диагностики на Azure Bastion. Эта статья помогает включить журналы диагностики, а затем просмотреть журналы.

## <a name="enable-the-diagnostics-log"></a><a name="enable"></a>Включить журнал диагностики

1. На [портале Azure](https://portal.azure.com)перейдите на ресурс Azure Bastion и выберите **параметры диагностики** со страницы Azure Bastion.

   ![настройки диагностики](./media/diagnostic-logs/1diagnostics-settings.png)
2. Выберите **параметры диагностики,** затем выберите **диагностическую настройку «Добавить»** для добавления пункта назначения для журналов.

   ![добавить диагностическую настройку](./media/diagnostic-logs/2add-diagnostic-setting.png)
3. На странице **настроек Диагностики** выберите тип учетной записи хранения, который будет использоваться для хранения журналов диагностики.

   ![выбрать место хранения](./media/diagnostic-logs/3add-storage-account.png)
4. Когда вы заполните настройки, он будет выглядеть так же, как этот пример:

   ![примеры настроек](./media/diagnostic-logs/4example-settings.png)

## <a name="view-diagnostics-log"></a><a name="view"></a>Просмотр журнала диагностики

Чтобы получить доступ к журналам диагностики, можно напрямую использовать указанную учетную запись хранилища, включая настройки диагностики.

1. Перейдите на ресурс учетной записи хранилища, а затем в **контейнеры.** Вы видите, что в контейнере с каплой капли учетной записи хранилища можно увидеть **информацию-журналы-бастионы.**

   ![настройки диагностики](./media/diagnostic-logs/1-navigate-to-logs.png)
2. При переходе внутрь контейнера, вы видите различные папки в вашем блоге. Эти папки указывают на иерархию ресурсов для ресурса Azure Bastion.

   ![добавить диагностическую настройку](./media/diagnostic-logs/2-resource-h.png)
3. Перейдите к полной иерархии ресурса Azure Bastion, к диагностике которого вы хотите получить доступ/просмотр. В «y» , «мз», «d» , «h» и «m» указывают год, месяц, день, час и минуту соответственно для диагностических журналов.

   ![выбрать место хранения](./media/diagnostic-logs/3-resource-location.png)
4. Найдите файл json, созданный Azure Bastion, содержащий данные журнала диагностики за период навигации.

5. Скачать файл json из контейнера для хранения капли. Приведенная ниже запись примера из файла json:

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

## <a name="next-steps"></a>Следующие шаги

Читайте [Бастион часто задаваемые вопросы](bastion-faq.md).
