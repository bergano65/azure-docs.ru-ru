---
title: Развертывание службы управления сертификатами хранилища OPC в Azure | Документация Майкрософт
description: Развертывание службы управления сертификатами хранилища OPC с нуля.
author: mregen
ms.author: mregen
ms.date: 08/16/2019
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 347e7c2aa2ff4fb4f188847b81d03006c1909166
ms.sourcegitcommit: 4b8a69b920ade815d095236c16175124a6a34996
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/23/2019
ms.locfileid: "69997642"
---
# <a name="build-and-deploy-the-opc-vault-certificate-management-service"></a>Создание и развертывание службы управления сертификатами хранилища OPC

В этой статье объясняется, как развернуть службу управления сертификатами хранилища OPC в Azure.

> [!NOTE]
> Дополнительные сведения о развертывании и инструкциях см. в [репозитории хранилища GitHub OPC](https://github.com/Azure/azure-iiot-opc-vault-service).

## <a name="prerequisites"></a>Предварительные требования

### <a name="install-required-software"></a>Установка необходимого программного обеспечения

В настоящее время операция сборки и развертывания ограничена Windows.
Примеры написаны для C# .NET Standard, которые необходимы для создания службы и примеров для развертывания.
Все средства, необходимые для .NET Standard, входят в состав средств .Net Core. Сведения о том, что вам нужно, см. [здесь](https://docs.microsoft.com/dotnet/articles/core/getting-started) .

1. [Установите .NET Core 2.1 +][dotnet-install].
2. [Установка DOCKER][docker-url] (необязательно, только если требуется локальная сборка DOCKER).
4. Установите [средства командной строки Azure для PowerShell][powershell-install].
5. Подпишитесь на [подписку Azure][azure-free].

### <a name="clone-the-repository"></a>Клонирование репозитория

Если вы еще не сделали этого, клонировать этот репозиторий GitHub.  Откройте командную строку или терминал и выполните следующую команду:

```bash
git clone https://github.com/Azure/azure-iiot-opc-vault-service
cd azure-iiot-opc-vault-service 
```

или клонировать репозиторий непосредственно в Visual Studio 2017.

### <a name="build-and-deploy-the-azure-service-on-windows"></a>Создание и развертывание службы Azure в Windows

Сценарий PowerShell предоставляет простой способ развертывания микрослужбы хранилища OPC и приложения.<br>

1. Откройте окно PowerShell в корне репозитория. 
3. Переход к папке Deploy`cd deploy`
3. Выберите имя `myResourceGroup` , которое вряд ли приведет к конфликту с другими развернутыми веб-страницами. [Ниже](#website-name-already-in-use) приведены сведения о выборе имен веб-страниц в зависимости от имени группы ресурсов.
5. Запуск развертывания с `.\deploy.ps1` целью интерактивной установки<br>
или введите полную командную строку:  
`.\deploy.ps1  -subscriptionName "MySubscriptionName" -resourceGroupLocation "East US" -tenantId "myTenantId" -resourceGroupName "myResourceGroup"`
7. Если планируется разработка с использованием этого развертывания, добавьте `-development 1` , чтобы включить пользовательский интерфейс Swagger и развернуть отладочные сборки.
6. Следуйте инструкциям в сценарии, чтобы войти в подписку и предоставить дополнительные сведения.
9. После успешной операции сборки и развертывания должно отобразиться следующее сообщение:
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

В случае возникновения проблем выполните приведенные [ниже](#troubleshooting-deployment-failures)действия.

8. Откройте свой любимый браузер и откройте страницу приложения:`https://myResourceGroup.azurewebsites.net`
8. Присвойте веб-приложению и микрослужбе хранилища OPC несколько минут, чтобы развернуть после развертывания. Домашняя страница веб-сайта может зависнуть при первом использовании до минуты, пока вы не получите первые ответы.
11. Чтобы взглянуть на открытый API Swagger, сделайте следующее:`https://myResourceGroup-service.azurewebsites.net`
13. Запуск локального сервера GDS с помощью DotNet Start `.\myResourceGroup-gds.cmd` или с запуском `.\myResourceGroup-dockergds.cmd`DOCKER.

В качестве полях можно повторно развернуть сборку с теми же параметрами. Имейте в виду, что такая операция обновляет секреты всех приложений и может сбросить некоторые параметры в Azure Active Directory регистрации приложений (Azure AD).

Кроме того, можно повторно развернуть только двоичные файлы веб-приложения. С параметром `-onlyBuild 1` новые ZIP-пакеты службы и приложение развертываются в веб – приложениях.

После успешного развертывания вы можете приступить к использованию служб: [Управление службой управления сертификатами хранилища OPC](howto-opc-vault-manage.md)

## <a name="delete-the-certificate-management-service-from-the-subscription"></a>Удаление службы управления сертификатами из подписки

1. Войдите в портал Azure: `https://portal.azure.com`.
2. Перейдите к группе ресурсов, в которой была развернута служба.
3. Выберите `Delete resource group` и подтвердите.
4. Через некоторое время все развернутые компоненты службы будут удалены.
5. Теперь перейдите по `Azure Active Directory/App registrations`адресу.
6. Для каждой развернутой группы ресурсов должны быть указаны три регистрации со следующими именами: `resourcegroup-client`, `resourcegroup-module`, `resourcegroup-service`.
Каждая регистрация должна быть удалена отдельно.
7. Теперь все развернутые компоненты будут удалены.

## <a name="troubleshooting-deployment-failures"></a>Устранение неполадок при развертывании

### <a name="resource-group-name"></a>Имя группы ресурсов

Убедитесь, что используется короткое и простое имя группы ресурсов.  Имя также используется для присвоения имен ресурсам и префиксу URL-адреса службы и, таким образом, должно соответствовать требованиям к именованию ресурсов.  

### <a name="website-name-already-in-use"></a>Имя веб-сайта уже используется

Возможно, имя веб-сайта уже используется.  Если вы выпустили эту ошибку, необходимо использовать другое имя группы ресурсов. В скрипте развертывания используются следующие имена узлов: https://resourcegroupname.azurewebsites.net и. https://resourgroupname-service.azurewebsites.net
Другие имена служб создаются с помощью сочетания хэшей коротких имен и вряд ли конфликтуют с другими службами.

### <a name="azure-active-directory-azure-ad-registration"></a>Регистрация Azure Active Directory (Azure AD) 

Сценарий развертывания пытается зарегистрировать три приложения Azure AD в Azure Active Directory.  
В зависимости от ваших разрешений в выбранном клиенте Azure AD эта операция может завершиться ошибкой.   Существуют два варианта:

1. Если вы выбрали клиент Azure AD из списка клиентов, перезапустите сценарий и выберите другой из списка.
2. Кроме того, можно развернуть частный клиент Azure AD в другой подписке, перезапустить сценарий и выбрать его для использования.

## <a name="deployment-script-options"></a>Параметры скрипта развертывания

Скрипт принимает следующие параметры:


```
-resourceGroupName
```

Может быть именем существующей или новой группы ресурсов.

```
-subscriptionId
```


(Необязательно) идентификатор подписки, в которой будут развернуты ресурсы.

```
-subscriptionName
```


Или же имя подписки.

```
-resourceGroupLocation
```


Необязательно, расположение группы ресурсов. Если указано, попытается создать новую группу ресурсов в этом расположении.


```
-tenantId
```


Клиент Azure AD для использования. 

```
-development 0|1
```

Необязательно, чтобы выполнить развертывание для разработки. Используйте отладочную сборку и задайте среду ASP.Net для разработки. Создайте ". publishsettings" для импорта в Visual Studio 2017, чтобы разрешить ему развертывание приложения и службы напрямую.

```
-onlyBuild 0|1
```

Необязательно, для перестроения и повторного развертывания только веб-приложений и перестроения контейнеров DOCKER.

[azure-free]:https://azure.microsoft.com/free/
[powershell-install]:https://azure.microsoft.com/downloads/#powershell
[docker-url]: https://www.docker.com/
[dotnet-install]: https://www.microsoft.com/net/learn/get-started

## <a name="next-steps"></a>Следующие шаги

Теперь, когда вы узнали, как развернуть хранилище OPC с нуля, предлагаем следующий шаг:

> [!div class="nextstepaction"]
> [Управление хранилищем OPC](howto-opc-vault-manage.md)
