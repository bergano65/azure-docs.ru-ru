---
title: Примеры Azure CLI. Служба приложений | Документация Майкрософт
description: Примеры Azure CLI. Служба приложений
services: app-service
documentationcenter: app-service
author: syntaxc4
manager: erikre
editor: ggailey777
tags: azure-service-management
ms.assetid: 53e6a15a-370a-48df-8618-c6737e26acec
ms.service: app-service
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: app-service
ms.date: 12/12/2017
ms.author: cfowler
ms.custom: mvc
ms.openlocfilehash: fe5649951b1b19ce52c13648f897f4a83e1f761b
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/19/2018
ms.locfileid: "53628380"
---
# <a name="cli-samples-for-azure-app-service"></a>Примеры интерфейса командной строки для Службы приложений Azure

В следующей таблице содержатся ссылки на скрипты Bash, созданные с помощью Azure CLI.

| | |
|-|-|
|**Создание приложения**||
| [Создание приложения и развертывание файлов с помощью протокола FTP](./scripts/cli-deploy-ftp.md?toc=%2fcli%2fazure%2ftoc.json)| Создает приложение Службы приложений и развертывает в него файл с использованием протокола FTP. |
| [Создание приложения и развертывание кода из GitHub](./scripts/cli-deploy-github.md?toc=%2fcli%2fazure%2ftoc.json)| Создает приложение Службы приложений и развертывает код из общедоступного репозитория GitHub. |
| [Создание приложения с непрерывным развертыванием из GitHub](./scripts/cli-continuous-deployment-github.md?toc=%2fcli%2fazure%2ftoc.json)| Создает приложение Службы приложений с непрерывной публикацией из репозитория GitHub, владельцем которого вы являетесь. |
| [Создание приложения и развертывание кода из локального репозитория Git](./scripts/cli-deploy-local-git.md?toc=%2fcli%2fazure%2ftoc.json) | Создает приложение Службы приложений и настраивает вживление кода из локального репозитория Git. |
| [Создание приложения и развертывание кода в промежуточной среде](./scripts/cli-deploy-staging-environment.md?toc=%2fcli%2fazure%2ftoc.json) | Создает приложение Службы приложений со слотом развертывания для изменений промежуточного кода. |
| [Создание приложения ASP.NET Core в контейнере Docker](./scripts/cli-linux-docker-aspnetcore.md?toc=%2fcli%2fazure%2ftoc.json)| Создает приложение Службы приложений в Linux и загружает образ Docker из Docker Hub. |
|**Настройка приложения**||
| [Сопоставление личного домена с приложением](./scripts/cli-configure-custom-domain.md?toc=%2fcli%2fazure%2ftoc.json)| Создает приложение Службы приложений и сопоставляет c ним имя личного домена. |
| [Привязка SSL-сертификата к приложению](./scripts/cli-configure-ssl-certificate.md?toc=%2fcli%2fazure%2ftoc.json)| Создает приложение Службы приложений и привязывает к нему SSL-сертификат имени личного домена. |
|**Масштабирование приложения**||
| [Масштабирование приложения вручную](./scripts/cli-scale-manual.md?toc=%2fcli%2fazure%2ftoc.json) | Создает приложение Службы приложений и масштабирует его по двум экземплярам. |
| [Глобальное масштабирование приложения с помощью высокодоступной архитектуры](./scripts/cli-scale-high-availability.md?toc=%2fcli%2fazure%2ftoc.json) | Создает два приложения Службы приложений в двух разных географических регионах и делает их доступными через одну конечную точку с помощью диспетчера трафика Azure. |
|**Подключение приложения к ресурсам**||
| [Подключение приложения к Базе данных SQL](./scripts/cli-connect-to-sql.md?toc=%2fcli%2fazure%2ftoc.json)| Создает приложение Службы приложений и базу данных SQL, а затем добавляет строку подключения базы данных к параметрам приложения. |
| [Подключение приложения к учетной записи хранения](./scripts/cli-connect-to-storage.md?toc=%2fcli%2fazure%2ftoc.json)| Создает приложение Службы приложений и учетную запись хранения, а затем добавляет строку подключения хранилища к параметрам приложения. |
| [Подключение приложения к кэшу Azure для Redis](./scripts/cli-connect-to-redis.md?toc=%2fcli%2fazure%2ftoc.json) | Создает приложение Службы приложений и кэш Azure для Redis, а затем добавляет сведения о подключении Redis в параметры приложения. |
| [Подключение приложения к Cosmos DB](./scripts/cli-connect-to-documentdb.md?toc=%2fcli%2fazure%2ftoc.json) | Создает приложение Службы приложений и базу данных Cosmos DB, а затем добавляет сведения о подключении Cosmos DB в параметры приложения. |
|**Резервное копирование и восстановление приложения**||
| [Резервное копирование приложения](./scripts/cli-backup-onetime.md?toc=%2fcli%2fazure%2ftoc.json) | Создает приложение Службы приложений и однократно создает его резервную копию. |
| [Создание резервной копии приложения по расписанию](./scripts/cli-backup-scheduled.md?toc=%2fcli%2fazure%2ftoc.json) | Создает приложение Службы приложений и запланировано создает его резервную копию. |
| [Восстановление приложения из резервной копии](./scripts/cli-backup-restore.md?toc=%2fcli%2fazure%2ftoc.json) | Восстанавливает приложение Службы приложений из резервной копии. |
|**Мониторинг приложения**||
| [Мониторинг приложения с помощью журналов веб-сервера](./scripts/cli-monitor.md?toc=%2fcli%2fazure%2ftoc.json) | Создает приложение Службы приложений, включает ведение журналов и скачивает их на локальный компьютер. |
| | |