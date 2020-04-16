---
title: Устранение проблем Azure Synapse Studio (предварительный просмотр) подключения PowerShell
description: Устранение проблем в подключении Студии Synapse с помощью PowerShell
author: julieMSFT
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: bbc985407a6cb56f4f1b539f514ab092b5f7d0de
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431479"
---
# <a name="diagnose-azure-synapse-studio-preview-connectivity-issues-with-powershell-script"></a>Диагностика проблем подключения Azure Synapse Studio (предварительный просмотр) со скриптом PowerShell

Студия Azure Synapse (предварительный просмотр) зависит от набора конечных точек Web API, которые должным образом работают. Это руководство поможет вам определить причины проблем с подключением, когда вы:
- настройка локальной сети (например, сети за корпоративным брандмауэром) для доступа к студии Azure Synapse.
- возникли проблемы с подключением с помощью Студии Azure Synapse.

## <a name="prerequisite"></a>Предварительные требования

* PowerShell 5.0 или выше версия на Windows, или
* PowerShell Core 6.0 или более высокая версия на Windows или Linux.

## <a name="troubleshooting-steps"></a>Действия по устранению неполадок

Нажмите правое нажатие на следующую ссылку и нажмите кнопку "Сохранить цель как":

- [Тест-AzureSynapse.ps1](https://go.microsoft.com/fwlink/?linkid=2119734)

Кроме того, вы можете открыть ссылку напрямую и сохранить открытый файл скрипта. Не сохраняйте адрес ссылки выше, так как она может измениться в будущем.

В файл еисследователя, право йгнул на загруженный файл сценария, и нажмите кнопку "Бегите с PowerShell".

![Выполнить загруженный файл скрипта с PowerShell](media/troubleshooting-synapse-studio-powershell/run-with-powershell.png)

При запросе введите имя рабочего пространства Azure Synapse, которое в настоящее время испытывает проблему или которое требуется протестировать на подключение, и нажмите введите.

![Введите имя рабочего пространства](media/troubleshooting-synapse-studio-powershell/enter-workspace-name.png)

Диагностическая сессия будет начата. Дождитесь завершения проверки.

![Подождите, пока диагностика завершится](media/troubleshooting-synapse-studio-powershell/wait-for-diagnosis.png)

В конце концов, будет показано резюме диагноза. Если ваш компьютер не может подключиться к одной или нескольких конечным точкам, он покажет некоторые предложения в разделе "Резюме".

![Обзор диагностического резюме](media/troubleshooting-synapse-studio-powershell/diagnosis-summary.png)

Кроме того, файл диагностического журнала для этой сессии будет сгенерирован в той же папке, что и скрипт устранения неполадок. Его местоположение показано в разделе "Общие советы" ().`D:\TestAzureSynapse_2020....log` При необходимости вы можете отправить этот файл в службу технической поддержки.

Если вы являетесь сетевым администратором и настраиваете конфигурацию брандмауэра для Azure Synapse Studio, технические детали, приведенные над разделом "Резюме", могут помочь.

* Все тестовые элементы (запросы), отмеченные "Пройдено", означают, что они прошли тесты подключения, независимо от кода состояния HTTP.
 Для неудавшихся запросов причина отображается `NamedResolutionFailure` `ConnectFailure`желтым цветом, например, или . Эти причины могут помочь вам выяснить, существуют ли неправильные настройки с вашей сетевой средой.


## <a name="next-steps"></a>Дальнейшие действия
Если предыдущие шаги не помогают решить [проблему, создайте билет поддержки.](../../sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket.md)
