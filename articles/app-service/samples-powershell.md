---
title: Примеры Azure PowerShell. Служба приложений | Документация Майкрософт
description: Примеры Azure PowerShell для службы приложений.
services: app-service
documentationcenter: app-service
author: syntaxc4
manager: erikre
editor: ggailey777
tags: azure-service-management
ms.assetid: b48d1137-8c04-46e0-b430-101e07d7e470
ms.service: app-service
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: app-service
ms.date: 03/08/2017
ms.author: cfowler
ms.custom: mvc
ms.openlocfilehash: 322820976f7f693ff09c071fc28f1ef3d1d472cf
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70074078"
---
# <a name="powershell-samples-for-azure-app-service"></a>Примеры PowerShell для Службы приложений Azure

В следующей таблице приведены ссылки на сценарии PowerShell, созданные с помощью Azure PowerShell.

| | |
|-|-|
|**Создание приложения**||
| [Создание приложения с развертыванием из GitHub](./scripts/powershell-deploy-github.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Создает приложение Службы приложений, которое вытягивает код из GitHub. |
| [Создание приложения с непрерывным развертыванием из GitHub](./scripts/powershell-continuous-deployment-github.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Создает приложение Службы приложений, которое непрерывно развертывает код из GitHub. |
| [Создание приложения и развертывание кода с помощью протокола FTP](./scripts/powershell-deploy-ftp.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Создает приложение Службы приложений и передает файлы из локального каталога с помощью протокола FTP. |
| [Создание приложения и развертывание кода из локального репозитория Git](./scripts/powershell-deploy-local-git.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Создает приложение Службы приложений и настраивает вживление кода из локального репозитория Git. |
| [Создание приложения и развертывание кода в промежуточной среде](./scripts/powershell-deploy-staging-environment.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Создает приложение Службы приложений со слотом развертывания для изменений промежуточного кода. |
|**Настройка приложения**||
| [Сопоставление личного домена с приложением](./scripts/powershell-configure-custom-domain.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Создает приложение Службы приложений и сопоставляет c ним имя личного домена. |
| [Привязка SSL-сертификата к приложению](./scripts/powershell-configure-ssl-certificate.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Создает приложение Службы приложений и привязывает к нему SSL-сертификат имени личного домена. |
|**Масштабирование приложения**||
| [Масштабирование приложения вручную](./scripts/powershell-scale-manual.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Создает приложение Службы приложений и масштабирует его по двум экземплярам. |
| [Глобальное масштабирование приложения с помощью высокодоступной архитектуры](./scripts/powershell-scale-high-availability.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Создает два приложения Службы приложений в двух разных географических регионах и делает их доступными через одну конечную точку с помощью диспетчера трафика Azure. |
|**Подключение приложения к ресурсам**||
| [Подключение приложения к Базе данных SQL](./scripts/powershell-connect-to-sql.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Создает приложение Службы приложений и базу данных SQL, а затем добавляет строку подключения базы данных к параметрам приложения. |
| [Подключение приложения к учетной записи хранения](./scripts/powershell-connect-to-storage.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Создает приложение Службы приложений и учетную запись хранения, а затем добавляет строку подключения хранилища к параметрам приложения. |
|**Резервное копирование и восстановление приложения**||
| [Резервное копирование приложения](./scripts/powershell-backup-onetime.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Создает приложение Службы приложений и однократно создает его резервную копию. |
| [Создание резервной копии приложения по расписанию](./scripts/powershell-backup-scheduled.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Создает приложение Службы приложений и запланировано создает его резервную копию. |
| [Удаление резервной копии приложения](./scripts/powershell-backup-delete.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Удаление существующей резервной копии приложения. |
| [Восстановление приложения из резервной копии](./scripts/powershell-backup-restore.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Восстановление приложения из ранее созданной резервной копии. |
| [Восстановление резервной копии между подписками](./scripts/powershell-backup-restore-diff-sub.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Восстановление веб-приложения из резервной копии в других подписках. |
|**Мониторинг приложения**||
| [Мониторинг приложения с помощью журналов веб-сервера](./scripts/powershell-monitor.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Создает приложение Службы приложений, включает ведение журналов и скачивает их на локальный компьютер. |
| | |
