---
title: Синхронизация содержимого из облачной папки
description: Узнайте, как развернуть приложение в службе приложений Azure с помощью синхронизации содержимого из облачной папки, включая OneDrive или Dropbox.
ms.assetid: 88d3a670-303a-4fa2-9de9-715cc904acec
ms.topic: article
ms.date: 12/03/2018
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: c55894bff9501d3ffb9aa843a9eaa240a213180e
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/02/2019
ms.locfileid: "74671729"
---
# <a name="sync-content-from-a-cloud-folder-to-azure-app-service"></a>Синхронизация содержимого из папки в облаке со службами приложений Azure
В этой статье показано, как синхронизировать содержимое [службы приложений Azure](https://go.microsoft.com/fwlink/?LinkId=529714) из Dropbox и OneDrive. 

Развертывание синхронизации содержимого по запросу основано на службе приложений [механизма развертывания Kudu](https://github.com/projectkudu/kudu/wiki). Можно работать с кодом приложения и содержимым указанной папки облака, а затем синхронизировать с App Service одним нажатием кнопки. Синхронизация содержимого использует сервер сборки Kudu. 

## <a name="enable-content-sync-deployment"></a>Включение развертывания синхронизации содержимого

Чтобы включить синхронизацию содержимого, перейдите на страницу приложения службы приложений на [портале Azure](https://portal.azure.com).

В меню слева щелкните **Центр развертывания** > **OneDrive** или **Dropbox** > **Авторизовать**. Следуйте инструкциям авторизации. 

![](media/app-service-deploy-content-sync/choose-source.png)

Необходимо пройти авторизацию только один раз в OneDrive или Dropbox. Если авторизация уже пройдена, просто щелкните **Продолжить**. Авторизованную учетную запись OneDrive или Dropbox можно изменить, щелкнув **Изменить учетную запись**.

![](media/app-service-deploy-content-sync/continue.png)

На странице **Настройка** выберите папку, которую необходимо синхронизировать. Эта папка создается в следующий указанный путь к содержимому в OneDrive или Dropbox. 
   
* **OneDrive**: `Apps\Azure Web Apps`
* **Dropbox**: `Apps\Azure`

По завершении нажмите кнопку **Продолжить**.

На странице **Сводка** проверьте параметры и нажмите **Готово**.

## <a name="synchronize-content"></a>Синхронизация содержимого

Если необходимо синхронизировать содержимое в папке облака с App Service, вернитесь в **Центр развертывания** и нажмите кнопку **Синхронизировать**.

![](media/app-service-deploy-content-sync/synchronize.png)
   
   > [!NOTE]
   > Из-за основных различий в интерфейсах API **OneDrive для бизнеса** пока не поддерживается. 
   > 
   > 

## <a name="disable-content-sync-deployment"></a>Отключение развертывания синхронизации содержимого

Чтобы отключить синхронизацию содержимого, перейдите на страницу приложения службы приложений в [портале Azure](https://portal.azure.com).

В меню слева щелкните **Центр развертывания** > **Отключение**.

![](media/app-service-deploy-content-sync/disable.png)

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Развертывание из локального репозитория Git](deploy-local-git.md)
