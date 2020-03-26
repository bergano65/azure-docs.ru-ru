---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 01/12/2020
ms.author: glenga
ms.openlocfilehash: 256510f855256e648ae9203f46eb9f66c9ffaed6
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/26/2020
ms.locfileid: "77029003"
---
## <a name="publish-the-project-to-azure"></a>Публикация проекта в Azure

В этом разделе показано создание приложения-функции и сопутствующих ресурсов в подписке Azure и последующее развертывание кода. 

1. Щелкните значок Azure на панели действий, а затем в области **Azure: Функции** выберите кнопку **Deploy to function app...** (Развертывание в приложение-функцию).

    ![Публикация проекта в Azure](media/functions-publish-project-vscode/function-app-publish-project.png)

1. Введите следующие сведения по соответствующим запросам:

    + **Выбрать подписку**. Выберите подписку, которую нужно использовать. Если у вас только одна подписка, вы не увидите этот параметр.

    + **Select Function App in Azure** (Выбор приложения-функции в Azure). Выберите `+ Create new Function App` (не `Advanced`). Эта статья не поддерживает [процесс расширенной публикации](../articles/azure-functions/functions-develop-vs-code.md#enable-publishing-with-advanced-create-options). 
    
    >[!IMPORTANT]
    > Публикация в существующее приложение-функцию перезаписывает содержимое этого приложения в Azure. 
    
    + **Enter a globally unique name for the function app** (Ввод глобально уникального имени для приложения-функции). Введите имя, допустимое в пути URL-адреса. Имя, которое вы вводите, проверяется, чтобы убедиться, что оно уникально в функциях Azure. 
    
    ::: zone pivot="programming-language-python"
    + **Select a runtime** (Выбор среды выполнения). Выберите версию Python, которая запускалась локально. Вы можете использовать команду `python --version`, чтобы проверить установленную версию.
    ::: zone-end

    ::: zone pivot="programming-language-javascript,programming-language-typescript"
    + **Select a runtime** (Выбор среды выполнения). Выберите версию Node.js, которая запускалась локально. Вы можете использовать команду `node --version`, чтобы проверить установленную версию.
    ::: zone-end

    + **Select a location for new resources** (Выбор расположения для новых ресурсов).  Для повышения производительности выберите [регион](https://azure.microsoft.com/regions/) рядом с вами. 
    
1.  После завершения в Azure в вашей подписке создаются следующие ресурсы:

    + **[Группа ресурсов](../articles/azure-resource-manager/management/overview.md)** . Содержит все созданные ресурсы Azure. Имя основывается на имени приложения-функции.
    + **[Учетная запись хранения](../articles//storage/common/storage-introduction.md#types-of-storage-accounts)** . Стандартная учетная запись хранения создается с уникальным именем, основанным на имени приложения-функции.
    + **[План размещения](../articles/azure-functions/functions-scale.md)** . План потребления создается в регионе "Западная часть США" для размещения бессерверного приложения-функции.
    + **Приложение-функция**. Проект развертывается и выполняется в этом новом приложении-функции.
    + **Application Insights**. Экземпляр, который подключен к приложению-функции, создается на основе имени функции.

    После создания приложения-функции и применения пакета развертывания отобразится уведомление. 
    
1. Выберите **View Output** (Просмотреть выходные данные) в уведомлении, чтобы просмотреть результаты создания и развертывания ресурсов Azure. Если вы пропустили уведомление, щелкните значок колокольчика в правом нижнем углу, чтобы снова просмотреть его.

    ![Создание уведомления о завершении](media/functions-publish-project-vscode/function-create-notifications.png)
