---
author: baanders
description: файл include для учебников по Azure Digital Twins — предварительные требования для примера проекта
ms.service: digital-twins
ms.topic: include
ms.date: 1/20/2021
ms.author: baanders
ms.openlocfilehash: 5a1baf9631f2d30dd14ff16d2d34beda04605c6c
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/21/2021
ms.locfileid: "98660527"
---
## <a name="prerequisites"></a>Предварительные требования

Если у вас еще нет подписки Azure, **создайте [бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)** , прежде чем начинать работу.

Кроме того, перед началом работы **установите [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) версии 16.5 или более поздней версии** на компьютере разработки. Если у вас установлена более старая версия, можно открыть на компьютере приложение *Visual Studio Installer* и выполнить инструкции по обновлению установки.

Этот учебник создан на основе примера проекта, написанного на C#. Пример находится здесь: [Комплексные примеры для Azure Digital Twins](/samples/azure-samples/digital-twins-samples/digital-twins-samples) **Получите пример проекта** на компьютер. Для этого перейдите по соответствующей ссылке и нажмите кнопку *Browse code* (Просмотреть код) под заголовком. Вы перейдете в репозиторий GitHub для примеров, которые можно скачать как *ZIP*-файлы. Для этого нажмите кнопку *Код* и выберите элемент *Скачать ZIP-файл*.

:::image type="content" source="../articles/digital-twins/media/includes/download-repo-zip.png" alt-text="Представление репозитория примеров Digital Twins в GitHub. Выбрана кнопка &quot;Код&quot;, в результате чего отображается небольшое диалоговое окно с выделенной кнопкой &quot;Скачать ZIP-файл&quot;." lightbox="../articles/digital-twins/media/includes/download-repo-zip.png":::

На ваш компьютер будет скачана *ZIP*-папка с именем **digital-twins-samples-master.zip**. Распакуйте папку и извлеките файлы.

### <a name="prepare-an-azure-digital-twins-instance"></a>Подготовка экземпляра Azure Digital Twins

[!INCLUDE [Azure Digital Twins: instance prereq](digital-twins-prereq-instance.md)]

[!INCLUDE [Azure Digital Twins: local credentials prereq (outer)](digital-twins-local-credentials-outer.md)]
