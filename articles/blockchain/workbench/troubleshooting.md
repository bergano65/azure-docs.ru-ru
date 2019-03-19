---
title: Устранение неполадок в Azure Blockchain Workbench
description: Как устранение неполадок в приложении Azure Blockchain Workbench.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 01/14/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: b55c84773d99c325689fbc5182e75c7cb108d00a
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57890021"
---
# <a name="azure-blockchain-workbench-troubleshooting"></a>Устранение неполадок в Azure Blockchain Workbench

Мы создали скрипт PowerShell для отладки при разработке и технической поддержки. Этот скрипт формирует сводные данные и собирает подробные журналы для устранения неполадок. Собираются журналы следующих служб:

* сеть Blockchain, например Ethereum;
* микрослужбы Blockchain Workbench;
* Application Insights
* Мониторинг Azure (журналы Azure Monitor)

Эти сведения помогут вам определиться с дальнейшими действиями и выяснить основную причину возникших проблем.

## <a name="troubleshooting-script"></a>Скрипт для устранения неполадок

Скрипт PowerShell для устранения неполадок доступен в репозитории GitHub. [Скачайте ZIP-файл](https://github.com/Azure-Samples/blockchain/archive/master.zip) или клонируйте пример с GitHub.

```
git clone https://github.com/Azure-Samples/blockchain.git
```

## <a name="run-the-script"></a>Запуск сценария
[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

Запустите скрипт `collectBlockchainWorkbenchTroubleshooting.ps1`, чтобы собрать журналы и создать ZIP-файл, содержащий папку со сведениями для устранения неполадок. Например: 

``` powershell
collectBlockchainWorkbenchTroubleshooting.ps1 -SubscriptionID "<subscription_id>" -ResourceGroupName "workbench-resource-group-name"
```
Этот скрипт принимает следующие параметры.

| Параметр  | ОПИСАНИЕ | Обязательно для заполнения |
|---------|---------|----|
| SubscriptionID | Идентификатор подписки, в которой создаются или используются ресурсы. | Yes |
| ResourceGroupName | Имя группы ресурсов Azure, в которой развернуто приложение Blockchain Workbench. | Yes |
| OutputDirectory | Путь для создания ZIP-файла с выходными данными. Если это значение не указано, по умолчанию используется текущий каталог. | Нет  |
| LookbackHours | Интервал времени (в часах), используемый при извлечении данных телеметрии. Значение по умолчанию — 24 часа. Максимальное значение — 90 часов. | Нет  |
| OmsSubscriptionId | Идентификатор подписки, где Azure Monitor регистрирует развертывается. Передайте этот параметр только в том случае, если журналы Azure Monitor для сети блокчейнов развертывается за пределами группы ресурсов Blockchain Workbench.| Нет  |
| OmsResourceGroup |Развертывается в группе ресурсов, где журналы Azure Monitor. Передайте этот параметр только в том случае, если журналы Azure Monitor для сети блокчейнов развертывается за пределами группы ресурсов Blockchain Workbench.| Нет  |
| OmsWorkspaceName | Имя рабочей области Log Analytics. Передайте этот параметр, только если журналы Azure Monitor для сети блокчейнов развертывается за пределами группы ресурсов Blockchain Workbench | Нет  |

## <a name="what-is-collected"></a>Какие данные собираются?

Результирующий ZIP-файл содержит выходные данные в следующей структуре папок:

| Папка или файл | ОПИСАНИЕ  |
|---------|---------|
| \Summary.txt | Общие сведения о системе |
| \Metrics\blockchain | Метрики сети блокчейн |
| \Metrics\Workbench | Метрики Workbench |
| \Details\Blockchain | Подробные журналы сети блокчейн |
| \Details\Workbench | Подробные журналы Workbench |

Файл общих сведений содержит моментальный снимок общего состояния и работоспособности приложения. В эту сводку входят рекомендуемые действия, самые частые ошибки и метаданные о запущенных службах.

Папка **Metrics** содержит метрики различных компонентов системы по времени. Например, выходной файл `\Details\Workbench\apiMetrics.txt` содержит сводку различных кодов отклика, а также время отклика за весь период сбора. Папка **Details** содержит подробные журналы со сведениями об устранении определенных проблем с программой Workbench базовой сети блокчейн. Например, файл `\Details\Workbench\Exceptions.csv` содержит список последних исключений, произошедших в системе. Эти сведения полезны для устранения ошибок, связанных со смарт-контрактами или взаимодействием с блокчейном. 

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Azure Blockchain Workbench Application Insights troubleshooting guide](https://aka.ms/workbenchtroubleshooting) (Руководство по устранению неполадок с Application Insights в Azure Blockchain Workbench)
