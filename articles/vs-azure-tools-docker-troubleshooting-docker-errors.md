---
title: Устранение неполадок клиента Docker в Windows с помощью Visual Studio | Документация Майкрософт
description: Устранение неполадок, которые возникают при использовании Visual Studio для создания и развертывания веб-приложений в Docker в Windows с помощью Visual Studio 2017.
services: azure-container-service
documentationcenter: na
author: devinb
manager: douge
editor: ''
ms.assetid: 346f70b9-7b52-4688-a8e8-8f53869618d3
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 10/13/2017
ms.author: devinb
ms.openlocfilehash: cd88dec2ad79ad9f4b4c004866060be86b777cd9
ms.sourcegitcommit: 3a02e0e8759ab3835d7c58479a05d7907a719d9c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/13/2018
ms.locfileid: "49311190"
---
# <a name="troubleshoot-visual-studio-2017-development-with-docker"></a>Устранение неполадок при разработке с помощью Docker в Visual Studio 2017

Работая с решением "Средства Visual Studio для Docker", вы можете столкнуться с некоторыми проблемами при создании или отладке приложения. Далее приведены некоторые распространенные действия по устранению неполадок.

## <a name="volume-sharing-is-not-enabled-enable-volume-sharing-in-the-docker-ce-for-windows-settings--linux-containers-only"></a>Совместное использование тома не включено. Включите совместное использование тома в параметрах Docker CE для Windows (только для контейнеров Linux)

Действия для устранения этой проблемы.

1. Щелкните правой кнопкой мыши **Docker for Windows** (Docker для Windows) в области уведомлений, а затем выберите **Параметры**.
1. Выберите **Shared Drives** (Общие диски) и включите общий доступ для использования системного диска вместе с диском, на котором находится проект.

> [!NOTE]
> Если файлы отображаются как совместно используемые, вам все равно нужно щелкнуть ссылку Reset credentials... (Сбросить учетные данные...) в нижней части диалогового окна, чтобы снова включить общий доступ к тому. Для продолжения работы после сброса учетных данных, возможно, потребуется перезапустить Visual Studio.

![общие диски](./media/vs-azure-tools-docker-troubleshooting-docker-errors/shareddrives.png)

## <a name="mounts-denied"></a>Отказ в подключении

При использовании Docker для macOS может появиться ошибка со ссылкой на папку /usr/local/share/dotnet/sdk/NuGetFallbackFolder. Добавьте папку на вкладку общего доступа к файлам в Docker.

## <a name="unable-to-start-debugging"></a>Невозможно начать отладку

Одна из причин может быть связана с наличием устаревших компонентов отладки в папке профиля пользователя. Чтобы последние компоненты отладки скачались при следующем сеансе отладки, выполните следующие команды для удаления папок:

- del %userprofile%\vsdbg;
- del %userprofile%\onecoremsvsmon.

## <a name="errors-specific-to-networking-when-debugging-your-application"></a>Ошибки, характерные для сетей при отладке приложения

Попробуйте выполнить сценарий, который можно скачать из репозитория GitHub для [очистки сетей узлов контейнера](https://github.com/MicrosoftDocs/Virtualization-Documentation/tree/master/windows-server-container-tools/CleanupContainerHostNetworking), который обновит связанные с сетью компоненты на вашем хост-компьютере.


## <a name="microsoftdockertools-github-repo"></a>Репозиторий GitHub Microsoft/DockerTools

При возникновении других проблем дополнительные сведения см. в репозитории [Microsoft/DockerTools](https://github.com/microsoft/dockertools/issues).