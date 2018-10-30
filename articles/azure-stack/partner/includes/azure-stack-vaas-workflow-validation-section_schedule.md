---
author: mattbriggs
ms.service: azure-stack
ms.topic: include
ms.date: 10/19/2018
ms.author: mabrigg
ms.openlocfilehash: d23ba90f7cc0c5a03db2ef3eed4a662abe7ff27e
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/23/2018
ms.locfileid: "49650559"
---
В рабочих процессах проверки при **планировании** теста используются общие параметры уровня рабочего процесса, которые вы указали во время его создания (см. раздел [Workflow common parameters for Azure Stack Validation as a Service](../azure-stack-vaas-parameters.md) (Общие параметры рабочих процессов для проверки как услуги в Azure Stack)). Если какие-либо из значений параметров станут недействительными, необходимо указать их повторно, как описано в разделе об [изменении параметров рабочего процесса](../azure-stack-vaas-monitor-test.md#change-workflow-parameters).

> [!NOTE]
> При планировании проверочного теста через существующий экземпляр на портале будет создан новый экземпляр вместо старого. Журналы для старого экземпляра будут сохранены, но недоступны на портале.  
После успешного завершения теста действие **Расписание** становится недоступным.

1. [!INCLUDE [azure-stack-vaas-workflow-step_select-agent](azure-stack-vaas-workflow-step_select-agent.md)]

1. В контекстном меню выберите **Расписание**, чтобы открыть командную строку для планирования выполнения тестового экземпляра.

1. Проверьте параметры теста, а затем выберите **Отправить**, чтобы запланировать выполнение теста.