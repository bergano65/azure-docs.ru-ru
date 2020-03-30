---
title: Устранение неполадок в облачных услугах Azure AD Connect
description: В этой статье описывается, как устранить неполадки, которые могут возникнуть с агентом подготовки облака.
author: billmath
ms.author: billmath
manager: daveba
ms.date: 12/02/2019
ms.topic: article
ms.prod: windows-server-threshold
ms.technology: identity-adfs
ms.openlocfilehash: e41be4b76245f2567015eb0ede317830120ee61a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75549491"
---
# <a name="cloud-provisioning-troubleshooting"></a>Устранение неполадок в облачном подготовке

Облачное подготовка затрагивает множество различных вещей и имеет множество различных зависимостей. Такой широкий охват может породить различные проблемы. Эта статья поможет вам устранить эти проблемы. Он вводит типичные области для вас, чтобы сосредоточиться на, как собрать дополнительную информацию, и различные методы, которые можно использовать для отслеживания проблем.


## <a name="common-troubleshooting-areas"></a>Общие зоны устранения неполадок

|name|Описание|
|-----|-----|
|[Проблемы с агентом](#agent-problems)|Убедитесь, что агент был установлен правильно и что он общается с Active Directory Azure (Azure AD).|
|[Проблемы синхронизации объектов](#object-synchronization-problems)|Используйте журналы подготовки для устранения проблем синхронизации объектов.|
|[Обеспечение карантинных проблем](#provisioning-quarantined-problems)|Понять проблемы карантина и как их решить.|


## <a name="agent-problems"></a>Проблемы с агентом
Некоторые из первых вещей, которые вы хотите проверить с агентом являются:

-  Устанавливается ли он?
-  Агент работает локально?
-  Агент в портале?
-  Является ли агент помечен как здоровый?

Эти элементы могут быть проверены на портале Azure и на локальном сервере, который управляет агентом.

### <a name="azure-portal-agent-verification"></a>Проверка агента на портале Azure

Чтобы убедиться, что агент виден Azure и является работоспособным, выполните следующие действия.

1. Войдите на портал Azure.
1. Слева выберите **Azure Active Directory** > **Azure AD Connect.** В центре выберите **Управление подготовкой (предварительный просмотр).**
1. На экране **обеспечения (предварительного просмотра) Azure AD** выберите **Обзор всех агентов.**

   ![Просмотрите все агенты](media/how-to-install/install7.png)</br>
 
1. На экране **on-premises, подающих агенты,** вы видите установленных агентов. Убедитесь, что агент, о котором идет речь, есть и помечен *здоровым.*

   ![Экран агентов по предварительному обеспечению](media/how-to-install/install8.png)</br>

### <a name="verify-the-port"></a>Проверить порт

Чтобы убедиться, что Azure слушает в порту 443 и что ваш агент может общаться с ним, используйте следующий инструмент:

https://aadap-portcheck.connectorporttest.msappproxy.net/ 

Этот тест проверяет, что агенты могут общаться с Azure через порт 443. Откройте браузер и перейдите на предыдущий URL с сервера, на котором установлен агент.

![Проверка доступности порта](media/how-to-install/verify2.png)

### <a name="on-the-local-server"></a>На локальном сервере

Чтобы убедиться, что агент работает, выполните следующие действия.

1. На сервере с установленным агентом, открыть **услуги** либо навигации к нему или, перейдя к **запуску** > **Run** > **Services.msc**.
1. В **рамках служб**убедитесь, что Microsoft **Azure AD Connect Connect-Агента Updater** и **Microsoft Azure AD Connect Представитель Представитель Службы** Информации Агент есть, и их статус *работает.*

   ![Экран служб](media/how-to-troubleshoot/troubleshoot1.png)

### <a name="common-agent-installation-problems"></a>Общие проблемы установки агента

В следующих разделах описаны некоторые общие проблемы установки агента и типичные разрешения.

#### <a name="agent-failed-to-start"></a>Агент не смог начать

Вы можете получить сообщение об ошибке, в которое говорится:

**Служба 'Microsoft Azure AD Connect Service. Убедитесь, что у вас достаточно привилегий для запуска системных служб.** 

Эта проблема, как правило, вызвана групповой политикой, которая препятствовала применению разрешений к локальной учетной записи NT Service, созданной установщиком (NT SERVICE-AADConnectServiceService. Эти разрешения необходимы для запуска службы.

Чтобы решить эту проблему, выполните следующие шаги.

1. Войти на сервер с учетной записью администратора.
1. Открытые **службы,** либо перенаваясь на него, либо отправляясь в **Start** > **Run** > **Services.msc.**
1. В **рамках служб**— дважды щелкните агента Microsoft **Azure AD Connect.**
1. На вкладке **"Войти в** систему" измените **эту учетную запись** на объявление домена. Затем перезапустите службу. 

   ![Войти на вкладку](media/how-to-troubleshoot/troubleshoot3.png)

#### <a name="agent-times-out-or-certificate-is-invalid"></a>Время агента или сертификат недействителен

Вы можете получить следующее сообщение об ошибке при попытке зарегистрировать агента.

![Сообщение об ошибке тайм-аута](media/how-to-troubleshoot/troubleshoot4.png)

Эта проблема обычно вызвана тем, что агент не может подключиться к службе гибридной идентификации, и требует настройки прокси HTTP. Чтобы решить эту проблему, наконзначаем исходящий прокси. 

Агент подготовки поддерживает использование исходящего прокси. Вы можете настроить его, редактируя файл доверенности агента *C: «Файлы программы»Microsoft Azure AD Connect Представитель AADConnectProvisioningAgent.exe.config*. Добавьте в него следующие строки, к концу файла непосредственно перед тегом закрытия. `</configuration>`
Замените `[proxy-server]` переменные `[proxy-port]` и с вашим именем сервера прокси и значениями порта.

```xml
    <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
            <proxy
                usesystemdefault="true"
                proxyaddress="http://[proxy-server]:[proxy-port]"
                bypassonlocal="true"
            />
        </defaultProxy>
    </system.net>
```

#### <a name="agent-registration-fails-with-security-error"></a>Регистрация агента не удается с ошибкой безопасности

Сообщение об ошибке может получить при установке агента подготовки облака.

Эта проблема обычно вызвана тем, что агент не может выполнить скрипты регистрации PowerShell из-за локальных политик выполнения PowerShell.

Чтобы решить эту проблему, измените политики выполнения PowerShell на сервере. Необходимо настроить политики машины и пользователя как *неопределенные* или *удаленные.* Если они установлены как *неограниченные,* вы увидите эту ошибку. Для получения дополнительной [информации](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_execution_policies?view=powershell-6)см. 

### <a name="log-files"></a>Файлы журналов

По умолчанию агент излучает минимальные сообщения об ошибках и стек информации о следах. Вы можете найти эти журналы следов в папке *C: «ProgramData»Microsoft»Azure AD Connect*

Чтобы собрать дополнительные сведения о проблемах, связанных с устранением неполадок, выполните следующие действия.

1. Остановите службу **Microsoft Azure AD Подключение Агента**подключения.
1. Создайте копию исходного файла конфигурации: *C:'Программные файлы »Microsoft Azure AD AD Connect Представитель ПодключенияAgent.exe.config*.
1. Замените `<system.diagnostics>` существующий раздел следующим, и все сообщения трассировки будут переходить в файл *ProvAgentTrace.log*.

   ```xml
     <system.diagnostics>
         <sources>
         <source name="AAD Connect Provisioning Agent">
             <listeners>
             <add name="console"/>
             <add name="etw"/>
             <add name="textWriterListener"/>
             </listeners>
         </source>
         </sources>
         <sharedListeners>
         <add name="console" type="System.Diagnostics.ConsoleTraceListener" initializeData="false"/>
         <add name="etw" type="System.Diagnostics.EventLogTraceListener" initializeData="Azure AD Connect Provisioning Agent">
             <filter type="System.Diagnostics.EventTypeFilter" initializeData="All"/>
         </add>
         <add name="textWriterListener" type="System.Diagnostics.TextWriterTraceListener" initializeData="C:/ProgramData/Microsoft/Azure AD Connect Provisioning Agent/Trace/ProvAgentTrace.log"/>
         </sharedListeners>
     </system.diagnostics>
    
   ```
1. Запустите службу **Microsoft Azure AD Подключите агента**подключения.
1. Используйте следующую команду для выполнения проблем файла и отладки. 
    ```
    Get-Content “C:/ProgramData/Microsoft/Azure AD Connect Provisioning Agent/Trace/ProvAgentTrace.log” -Wait
    ```
## <a name="object-synchronization-problems"></a>Проблемы синхронизации объектов

Следующий раздел содержит информацию о синхронизации объектов устранения неполадок.

### <a name="provisioning-logs"></a>Подготовка журналов

На портале Azure журналы подготовки могут использоваться для отслеживания и устранения проблем синхронизации объектов. Для просмотра журналов выберите **журналы.**

![Кнопка "Журналы"](media/how-to-troubleshoot/log1.png)

Создание журналов обеспечивает большой объем информации о состоянии объектов, синхронизированных между вашей предварительной средой Active Directory и Azure.

![Экран для обеспечения журналов](media/how-to-troubleshoot/log2.png)

Вы можете использовать выпадающие ящики в верхней части страницы для фильтрации представления до нуля на конкретных проблемах, таких как даты. Дважды щелкните отдельное событие, чтобы увидеть дополнительную информацию.

![Подготовка регистров выпадающих данных окно](media/how-to-troubleshoot/log3.png)

Эта информация содержит подробные шаги и место возникновения проблемы синхронизации. Таким образом, вы можете определить точное место проблемы.


## <a name="provisioning-quarantined-problems"></a>Обеспечение карантинных проблем

Облачное обеспечение отслеживает работоспособность конфигурации и помещает нездоровые объекты в карантинное состояние. Если большинство или все вызовы, сделанные против целевой системы, постоянно выводятся из-за ошибки, например, недействительных учетных данных админ-адектора, задание подготовки помечается как в карантине.

![Состояние карантина](media/how-to-troubleshoot/quarantine1.png)

Выбрав статус, можно увидеть дополнительную информацию о карантине. Вы также можете получить код ошибки и сообщение.

![Информация о статусе карантина](media/how-to-troubleshoot/quarantine2.png)

### <a name="resolve-a-quarantine"></a>Разрешить карантин

- Используйте портал Azure для перезагрузки задания подготовки. На странице конфигурации агента выберите **подготовку restart.**

  ![Перезапуск подготовки](media/how-to-troubleshoot/quarantine3.png)

- Используйте Microsoft Graph для [перезапуска задания подготовки.](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-restart?view=graph-rest-beta&tabs=http) Вы будете иметь полный контроль над тем, что вы перезагрузить. Вы можете выбрать:
  - Escrows, чтобы перезапустить счетчик эскроу, который начисляется к карантину статуса.
  - карантин, чтобы удалить приложение из карантина.
  - Водяные знаки. 
  
  Используйте следующий запрос:
 
  `POST /servicePrincipals/{id}/synchronization/jobs/{jobId}/restart`

## <a name="next-steps"></a>Дальнейшие действия 

- [Что собой представляет подготовка?](what-is-provisioning.md)
- [Что такое подготовка облака Azure AD Connect?](what-is-cloud-provisioning.md)



