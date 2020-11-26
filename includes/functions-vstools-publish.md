---
title: включить файл
description: включить файл
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/30/2020
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: ef617b9ed6c35d33350b173efe6189b5b79b6ae6
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/25/2020
ms.locfileid: "96008246"
---
1. Щелкните правой кнопкой мыши проект в **обозревателе решений** и выберите пункт **Опубликовать**.

1. На вкладке **Целевой объект** выберите **Azure**. :::image type="content" source="media/functions-vstools-publish/functions-visual-studio-publish-profile-step-1.png" alt-text="Выбор Azure в качестве целевого объекта":::

1. На вкладке **Указанный целевой объект** выберите **Приложение-функция Azure (Windows)** .

    :::image type="content" source="media/functions-vstools-publish/functions-visual-studio-publish-profile-step-2.png" alt-text="Выбор приложения-функции Azure":::

1. На вкладке **Экземпляр Функций** выберите **Создать функцию Azure…** и используйте значения, указанные в следующей таблице.

    | Параметр      | Значение  | Описание                                |
    | ------------ |  ------- | -------------------------------------------------- |
    | **имя**; | Глобально уникальное имя | Имя, которое однозначно идентифицирует новое приложение-функцию. Используйте это имя или введите новое. Допустимые символы: `a-z`, `0-9` и `-`. |
    | **Подписка** | Ваша подписка | Подписка Azure, которую нужно использовать. Используйте эту подписку или выберите новую из раскрывающегося списка. |
    | **[Группа ресурсов](../articles/azure-resource-manager/management/overview.md)** | Имя группы ресурсов |  Группа ресурсов, в которой создается приложение-функция. Выберите существующую группу ресурсов из раскрывающегося списка или нажмите **Создать**, чтобы создать новую.|
    | **[Тип плана](../articles/azure-functions/functions-scale.md)** | Потребление | При публикации проекта в приложении-функции, которое работает в [плане потребления](../articles/azure-functions/functions-scale.md#consumption-plan), вы платите только за выполнение приложения-функции. Другие планы размещения связаны с дополнительными расходами. |
    | **Расположение** | Расположение Службы приложений. | Выберите **расположение** в ближайшем к вам [регионе](https://azure.microsoft.com/regions/) или регионе, ближайшем к другим службам, к которым обращаются ваши функции. |
    | **[Служба хранилища Azure](../articles/storage/common/storage-account-create.md)** | Учетная запись хранения общего назначения | Учетная запись хранения Azure — обязательный ресурс для среды выполнения Функций. Выберите **Создать**, чтобы настроить учетную запись хранения общего назначения. Можно также использовать существующую учетную запись при условии, что она соответствует [требованиям учетной записи хранения](../articles/azure-functions/functions-scale.md#storage-account-requirements).  |

    ![Диалоговое окно "Создание службы приложений"](./media/functions-vstools-publish/functions-visual-studio-publish.png)

1. Нажмите кнопку **Создать**, чтобы создать приложение-функцию и связанные с ним ресурсы в Azure. 
1. На вкладке **Экземпляр Функций** установите флажок **Run from package file** (Запустить из файла пакета). Приложение-функция развертывается с помощью [Zip Deploy](../articles/azure-functions/functions-deployment-technologies.md#zip-deploy) с включенным режимом [Run-From-Package](../articles/azure-functions/run-functions-from-deployment-package.md) (Выполнение из пакета). Это рекомендуемый метод развертывания для проекта функций, так как он обеспечивает лучшую производительность. 

    :::image type="content" source="media/functions-vstools-publish/functions-visual-studio-publish-profile-step-4.png" alt-text="Завершение создания профиля":::

1. Нажмите кнопку **Готово** и на странице "Публикация" выберите **Опубликовать**, чтобы развернуть пакет с файлами проекта в новом приложении-функции в Azure. 

    Когда развертывание будет завершено, корневой URL-адрес приложения-функции в Azure отобразится на вкладке **Публикация**. 
    
1.  На вкладке "Публикация" выберите **Управление в Cloud Explorer**. В результате в Cloud Explorer будет открыт новый ресурс функции-приложения Azure. 
    
    :::image type="content" source="media/functions-vstools-publish/functions-visual-studio-publish-complete.png" alt-text="Сообщение об успешной публикации":::
    
    В Cloud Explorer вы можете с помощью Visual Studio просматривать содержимое сайта, запускать и останавливать работу приложения-функции, а также переходить непосредственно к ресурсам функций-приложений в Azure и на портале Azure. 
