---
title: Устранение неполадок в Azure Blockchain Workbench
description: How to troubleshoot an Azure Blockchain Workbench Preview application.
ms.date: 10/14/2019
ms.topic: article
ms.reviewer: brendal
ms.openlocfilehash: ef4bce4dfba77aafa9b86c6877c153534b54636e
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74324301"
---
# <a name="azure-blockchain-workbench-preview-troubleshooting"></a>Azure Blockchain Workbench Preview troubleshooting

Мы создали скрипт PowerShell для отладки при разработке и технической поддержки. Этот скрипт формирует сводные данные и собирает подробные журналы для устранения неполадок. Собираются журналы следующих служб:

* сеть Blockchain, например Ethereum;
* микрослужбы Blockchain Workbench;
* Application Insights
* Azure Monitoring (Azure Monitor logs)

Эти сведения помогут вам определиться с дальнейшими действиями и выяснить основную причину возникших проблем.

[!INCLUDE [Preview note](./includes/preview.md)]

## <a name="troubleshooting-script"></a>Скрипт для устранения неполадок

Скрипт PowerShell для устранения неполадок доступен в репозитории GitHub. [Скачайте ZIP-файл](https://github.com/Azure-Samples/blockchain/archive/master.zip) или клонируйте пример с GitHub.

```
git clone https://github.com/Azure-Samples/blockchain.git
```

## <a name="run-the-script"></a>Запуск сценария
[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

Запустите скрипт `collectBlockchainWorkbenchTroubleshooting.ps1`, чтобы собрать журналы и создать ZIP-файл, содержащий папку со сведениями для устранения неполадок. Пример.

``` powershell
collectBlockchainWorkbenchTroubleshooting.ps1 -SubscriptionID "<subscription_id>" -ResourceGroupName "workbench-resource-group-name"
```
Этот скрипт принимает следующие параметры.

| Параметр  | Описание | Обязательно для заполнения |
|---------|---------|----|
| SubscriptionID | Идентификатор подписки, в которой создаются или используются ресурсы. | ДА |
| ResourceGroupName | Имя группы ресурсов Azure, в которой развернуто приложение Blockchain Workbench. | ДА |
| OutputDirectory | Путь для создания ZIP-файла с выходными данными. Если это значение не указано, по умолчанию используется текущий каталог. | Нет |
| LookbackHours | Интервал времени (в часах), используемый при извлечении данных телеметрии. Значение по умолчанию — 24 часа. Максимальное значение — 90 часов. | Нет |
| OmsSubscriptionId | The subscription ID where Azure Monitor logs is deployed. Only pass this parameter if the Azure Monitor logs for the blockchain network is deployed outside of Blockchain Workbench's resource group.| Нет |
| OmsResourceGroup |The resource group where Azure Monitor logs is deployed. Only pass this parameter if the Azure Monitor logs for the blockchain network is deployed outside of Blockchain Workbench's resource group.| Нет |
| OmsWorkspaceName | Имя рабочей области Log Analytics. Only pass this parameter if the Azure Monitor logs for the blockchain network is deployed outside of Blockchain Workbench's resource group | Нет |

## <a name="what-is-collected"></a>Какие данные собираются?

Результирующий ZIP-файл содержит выходные данные в следующей структуре папок:

| Папка или файл | Описание  |
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
