---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: a84f0a92703d1b626710cfc4dcfa2820bc58bda6
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673198"
---
## <a name="configure-your-local-environment"></a>Настройка локальной среды

Перед началом работы убедитесь, что у вас есть такие компоненты.

+ Учетная запись Azure с активной подпиской. [Создайте учетную запись](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) бесплатно.

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell"  
+ [Azure Functions Core Tools](../articles/azure-functions/functions-run-local.md#v2) 2.7.1846 или версии, более поздней, чем 2.
::: zone-end  
::: zone pivot="programming-language-python"
+ Для Python 3.6 и 3.7 требуется [Azure Functions Core Tools](../articles/azure-functions/functions-run-local.md#v2) 2.7.1846 или версии, более поздней, чем 2.x. Для Python 3.8 требуется Core Tools [версии 3.x](../articles/azure-functions/functions-run-local.md#v2).
::: zone-end

+ Для его работы требуется [Azure CLI](/cli/azure/install-azure-cli) 2.0.76 или более поздней версии. 
::: zone pivot="programming-language-javascript,programming-language-typescript"
+ [Node.js](https://nodejs.org/), активная версия LTS и версия Maintenance LTS (рекомендуются версии 8.11.1 и 10.14.1).
::: zone-end

::: zone pivot="programming-language-python"
+ [Python 3.8 (64-разрядная версия)](https://www.python.org/downloads/release/python-382/), [Python 3.7 (64-разрядная версия)](https://www.python.org/downloads/release/python-375/), [Python 3.6 (64-разрядная версия)](https://www.python.org/downloads/release/python-368/), которые поддерживаются решением "Функции Azure". 
::: zone-end
::: zone pivot="programming-language-powershell"
+ [PowerShell Core](/powershell/scripting/install/installing-powershell-core-on-windows)

+ [Пакет SDK для .NET Core 2.2 и более поздних версий](https://www.microsoft.com/net/download).
::: zone-end
::: zone pivot="programming-language-java"  
+ [Java Developer Kit](https://aka.ms/azure-jdks) версии 8.

+ [Apache Maven](https://maven.apache.org) 3.0 или более поздней версии.

> [!IMPORTANT]
> Переменной среде JAVA_HOME необходимо присвоить расположение установки JDK, чтобы завершить выполнение заданий этого краткого руководства.
::: zone-end