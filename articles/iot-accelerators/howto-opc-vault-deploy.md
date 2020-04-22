---
title: Как развернуть службу управления сертификатами OPC Vault - Azure Документы Майкрософт
description: Как развернуть службу управления сертификатами OPC Vault с нуля.
author: mregen
ms.author: mregen
ms.date: 08/16/2019
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 7ee186684b702a42335c6e1a7832cc5c761a69d0
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81686942"
---
# <a name="build-and-deploy-the-opc-vault-certificate-management-service"></a>Создание и развертывание службы управления сертификатами OPC Vault

В этой статье объясняется, как развернуть службу управления сертификатами OPC Vault в Azure.

> [!NOTE]
> Для получения дополнительной [информации](https://github.com/Azure/azure-iiot-opc-vault-service)см.

## <a name="prerequisites"></a>Предварительные требования

### <a name="install-required-software"></a>Установка необходимого программного обеспечения

В настоящее время работа сборки и развертывания ограничена Windows.
Все образцы написаны для стандарта C'NET, который необходимо для создания службы и образцов для развертывания.
Все инструменты, необходимые для .NET Standard, оснащены инструментами .NET Core. Смотрите [Начало работы с .NET Core](https://docs.microsoft.com/dotnet/articles/core/getting-started).

1. [Установка .NET Core 2.1 .][dotnet-install]
2. [Установите Docker][docker-url] (необязательно, только если требуется локальная сборка Docker).
4. Установите [инструменты командной строки Azure для PowerShell.][powershell-install]
5. Подпишитесь на [подписку Azure][azure-free].

### <a name="clone-the-repository"></a>Клонирование репозитория

Если вы еще этого не сделали, клоните этот репозиторий GitHub. Откройте запрос или терминал команды и запустите следующее:

```bash
git clone https://github.com/Azure/azure-iiot-opc-vault-service
cd azure-iiot-opc-vault-service 
```

Кроме того, вы можете клонировать репо непосредственно в Visual Studio 2017.

### <a name="build-and-deploy-the-azure-service-on-windows"></a>Создание и развертывание службы Azure в Windows

Скрипт PowerShell предоставляет простой способ развертывания микрослужбы OPC Vault и приложения.

1. Откройте окно PowerShell в корне репо. 
3. Перейдите в папку `cd deploy`развертывания.
3. Выбрать имя `myResourceGroup` для этого вряд ли вызовет конфликт с другими развернутыми веб-страницами. Позже в этой статье смотрите раздел «Имя веб-сайта, которое уже используется».
5. Начните развертывание `.\deploy.ps1` с интерактивной установкой или введите полную командную строку:  
`.\deploy.ps1  -subscriptionName "MySubscriptionName" -resourceGroupLocation "East US" -tenantId "myTenantId" -resourceGroupName "myResourceGroup"`
7. Если вы планируете развиваться `-development 1` с этим развертыванием, добавьте, чтобы включить uI Swagger, и развернуть отладки сборки.
6. Следуйте инструкциям в скрипте, чтобы войти в подписку, и предоставить дополнительную информацию.
9. После успешной операции сборки и развертывания следует увидеть следующее сообщение:
   ```
   To access the web client go to:
   https://myResourceGroup.azurewebsites.net

   To access the web service go to:
   https://myResourceGroup-service.azurewebsites.net

   To start the local docker GDS server:
   .\myResourceGroup-dockergds.cmd

   To start the local dotnet GDS server:
   .\myResourceGroup-gds.cmd
   ```

   > [!NOTE]
   > В случае проблем смотрите раздел «Устранение сбоев развертывания» позже в статье.

8. Откройте свой любимый браузер и откройте страницу приложения:`https://myResourceGroup.azurewebsites.net`
8. Дайте веб-приложению и микрослужбе OPC Vault несколько минут, чтобы разогреться после развертывания. Веб-домашняя страница может висеть на первом использовании, до минуты, пока вы не получите первые ответы.
11. Чтобы взглянуть на API Swagger, откройте:`https://myResourceGroup-service.azurewebsites.net`
13. Чтобы запустить локальный сервер GDS `.\myResourceGroup-gds.cmd`с помощью dotnet, запустите . С Docker, `.\myResourceGroup-dockergds.cmd`начните .

Можно передислоцировать сборку с точно такими же настройками. Имейте в виду, что такая операция обновляет все секреты приложений и может сбросить некоторые настройки в регистрации приложений Azure Active Directory (Azure AD).

Кроме того, можно передислоцировать только веб-приложений файлов. С параметром, `-onlyBuild 1`новые пакеты zip службы и app раскрыны к применениям стержня.

После успешного развертывания можно начать использовать службы. Смотрите [Управление службой управления сертификатами OPC Vault.](howto-opc-vault-manage.md)

## <a name="delete-the-services-from-the-subscription"></a>Удаление услуг из подписки

Это делается так.

1. Войдите на [портал Azure](https://portal.azure.com).
2. Перейдите в группу ресурсов, в которой была развернута служба.
3. Выберите **Удалить группу ресурсов** и подтвердите выбор.
4. Через некоторое время все развернутые компоненты службы удаляются.
5. Перейдите к > **регистрации приложений**Active **Directory Azure**.
6. Для каждой развернутой группы ресурсов должно быть указано три регистрации. Регистрации имеют следующие `resourcegroup-client`названия: , `resourcegroup-module`. `resourcegroup-service` Удалите каждую регистрацию отдельно.

Теперь все развернутые компоненты удаляются.

## <a name="troubleshooting-deployment-failures"></a>Устранение сбоев в развертывании

### <a name="resource-group-name"></a>Имя группы ресурсов

Используйте короткое и простое название группы ресурсов. Имя также используется для именовании ресурсов и приставки URL-адреса службы. Таким образом, он должен соответствовать требованиям именования ресурсов.  

### <a name="website-name-already-in-use"></a>Название веб-сайта уже используется

Не исключено, что название сайта уже используется. Вам нужно использовать другое имя группы ресурсов. Имена хоста, использоваемые\/скриптом развертывания:\/https: /resourcegroupname.azurewebsites.net и https: /resourgroupname-service.azurewebsites.net.
Другие названия сервисов построены сочетанием коротких хэшов имен и вряд ли противоречат другим службам.

### <a name="azure-ad-registration"></a>регистрация в Azure AD. 

Скрипт развертывания пытается зарегистрировать три приложения Azure AD в Azure AD. В зависимости от ваших разрешений в выбранном атакжеме Azure AD эта операция может выполнить неудачу. Имеются две возможности:

- Если вы выбрали арендатора Azure AD из списка арендаторов, перезапустите скрипт и выберите другой из списка.
- Кроме того, развернуть частный ad-арендатор Azure AD в другой подписке. Перезапустите сценарий и выберите его.

## <a name="deployment-script-options"></a>Параметры сценария развертывания

Скрипт принимает следующие параметры:


```
-resourceGroupName
```

Это может быть имя существующей или новой группы ресурсов.

```
-subscriptionId
```


Это идентификатор подписки, где будут развернуты ресурсы. Это необязательно.

```
-subscriptionName
```


Кроме того, вы можете использовать имя подписки.

```
-resourceGroupLocation
```


Это место расположения группы ресурсов. Если указано, этот параметр пытается создать новую группу ресурсов в этом месте. Этот параметр также не является обязательным.


```
-tenantId
```


Это для использования арендатора Azure AD. 

```
-development 0|1
```

Это необходимо для развертывания для разработки. Используйте отладку сборки и установите среду ASP.NET для разработки. Создайте `.publishsettings` для импорта в Visual Studio 2017, чтобы позволить ему развернуть приложение и службу напрямую. Этот параметр также не является обязательным.

```
-onlyBuild 0|1
```

Это необходимо для восстановления и передислокации только веб-приложений, а также для восстановления контейнеров Docker. Этот параметр также не является обязательным.

[azure-free]:https://azure.microsoft.com/free/
[powershell-install]:https://azure.microsoft.com/downloads/#powershell
[docker-url]: https://www.docker.com/
[dotnet-install]: https://www.microsoft.com/net/learn/get-started

## <a name="next-steps"></a>Следующие шаги

Теперь, когда вы узнали, как развернуть OPC Vault с нуля, вы можете:

> [!div class="nextstepaction"]
> [Управление OPC Vault](howto-opc-vault-manage.md)
