---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: e969520e226999bc2b104fb4f757619832eaa165
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/05/2020
ms.locfileid: "88944423"
---
## <a name="configure-your-local-environment"></a>Настройка локальной среды

Перед началом работы убедитесь, что у вас есть такие компоненты.

+ Учетная запись Azure с активной подпиской. [Создайте учетную запись](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) бесплатно.

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-java"  
+ [Azure Functions Core Tools](../articles/azure-functions/functions-run-local.md#v2) 2.7.1846 или версии, более поздней, чем 2.
::: zone-end  
::: zone pivot="programming-language-python"
+ Версия Azure Functions Core Tools, соответствующая установленной версии Python:

   | Версия Python | Версия Core Tools |
   | -------------- | ------------------ |
   | Python 3.8     | [Версия 3.x](../articles/azure-functions/functions-run-local.md#v2) |
   | Python 3.6<br/>Python 3.7 | [Версия 2.7.1846 или более поздняя](../articles/azure-functions/functions-run-local.md#v2) |
  
::: zone-end

+ [Azure CLI](/cli/azure/install-azure-cli) 2.4 или более поздней версии. 
::: zone pivot="programming-language-javascript,programming-language-typescript"
+ [Node.js](https://nodejs.org/), активная версия LTS и версия Maintenance LTS (рекомендуются версии 8.11.1 и 10.14.1).
::: zone-end

::: zone pivot="programming-language-python"
+ [Python 3.8 (64-разрядная версия)](https://www.python.org/downloads/release/python-382/), [Python 3.7 (64-разрядная версия)](https://www.python.org/downloads/release/python-375/), [Python 3.6 (64-разрядная версия)](https://www.python.org/downloads/release/python-368/), которые поддерживаются решением "Функции Azure". 
::: zone-end
::: zone pivot="programming-language-powershell"
+ Пакет [SDK для .NET Core 3.1](https://www.microsoft.com/net/download).
::: zone-end
::: zone pivot="programming-language-java"  
+ [Java Developer Kit](https://aka.ms/azure-jdks) версии 8. 

    > [!IMPORTANT]
    > + Поддержка Функций для Java 11 в настоящее время доступна в предварительной версии, а архетип Maven по умолчанию создает развертывание Java 8. Если вы хотите запустить приложение-функцию в среде Java 11, необходимо вручную изменить файл pom.xml, указав в нем значения Java 11. Дополнительные сведения см. в разделе [Версии Java](../articles/azure-functions/functions-reference-java.md#java-versions). 
    > + Переменной среды `JAVA_HOME` необходимо присвоить расположение установки правильной версии JDK, чтобы завершить выполнение заданий этого краткого руководства.

+ [Apache Maven](https://maven.apache.org) 3.0 или более поздней версии.

::: zone-end
