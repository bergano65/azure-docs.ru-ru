---
title: Проблемы проверки подлинности в Azure HDInsight
description: Проблемы проверки подлинности в Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 11/08/2019
ms.openlocfilehash: 17bc9f1ea93b0afa4f53443a53d294acb9e94b2e
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/10/2019
ms.locfileid: "73901403"
---
# <a name="authentication-issues-in-azure-hdinsight"></a>Проблемы проверки подлинности в Azure HDInsight

В этой статье описываются действия по устранению неполадок и возможные способы решения проблем при взаимодействии с кластерами Azure HDInsight.

В защищенных кластерах, которые поддерживаются Azure Data Lake (GEN1 или Gen2), когда пользователи домена входят в службы кластеров через шлюз HDI (например, при входе на портал Apache Ambari), шлюз HDI сначала попытается получить маркер OAuth из Azure Active Directory (Azure AD). , а затем получите билет Kerberos из Azure AD DS. На одном из этих этапов может произойти сбой проверки подлинности. Эта статья предназначена для отладки некоторых из этих проблем.

При сбое проверки подлинности вам будет предложено ввести учетные данные. Если отменить это диалоговое окно, будет выведено сообщение об ошибке. Ниже приведены некоторые распространенные сообщения об ошибках.

## <a name="invalid_grant-or-unauthorized_client-50126"></a>invalid_grant или unauthorized_client, 50126

### <a name="issue"></a>Проблема

Сбой входа для федеративных пользователей с кодом ошибки 50126 (вход выполнен успешно для пользователей облака). Сообщение об ошибке выглядит следующим образом:

```
Reason: Bad Request, Detailed Response: {"error":"invalid_grant","error_description":"AADSTS70002: Error validating credentials. AADSTS50126: Invalid username or password\r\nTrace ID: 09cc9b95-4354-46b7-91f1-efd92665ae00\r\n Correlation ID: 4209bedf-f195-4486-b486-95a15b70fbe4\r\nTimestamp: 2019-01-28 17:49:58Z","error_codes":[70002,50126], "timestamp":"2019-01-28 17:49:58Z","trace_id":"09cc9b95-4354-46b7-91f1-efd92665ae00","correlation_id":"4209bedf-f195-4486-b486-95a15b70fbe4"}
```

### <a name="cause"></a>Причина:

Код ошибки Azure AD 50126 означает, что клиент не установил политику `AllowCloudPasswordValidation`.

### <a name="resolution"></a>Способы устранения:

Администратор компании клиента Azure AD должен разрешить Azure AD использовать хэши паролей для пользователей, которые поддерживают ADFS.  Примените `AllowCloudPasswordValidationPolicy`, как показано в статье [использование Корпоративный пакет безопасности в HDInsight](../domain-joined/apache-domain-joined-architecture.md).

---

## <a name="invalid_grant-or-unauthorized_client-50034"></a>invalid_grant или unauthorized_client, 50034

### <a name="issue"></a>Проблема

Сбой входа с кодом ошибки 50034. Сообщение об ошибке выглядит следующим образом:

```
{"error":"invalid_grant","error_description":"AADSTS50034: The user account Microsoft.AzureAD.Telemetry.Diagnostics.PII does not exist in the 0c349e3f-1ac3-4610-8599-9db831cbaf62 directory. To sign into this application, the account must be added to the directory.\r\nTrace ID: bbb819b2-4c6f-4745-854d-0b72006d6800\r\nCorrelation ID: b009c737-ee52-43b2-83fd-706061a72b41\r\nTimestamp: 2019-04-29 15:52:16Z", "error_codes":[50034],"timestamp":"2019-04-29 15:52:16Z","trace_id":"bbb819b2-4c6f-4745-854d-0b72006d6800", "correlation_id":"b009c737-ee52-43b2-83fd-706061a72b41"}
```

### <a name="cause"></a>Причина:

Неверное имя пользователя (не существует). Пользователь не использует то же имя пользователя, которое используется в портал Azure.

### <a name="resolution"></a>Способы устранения:

Используйте то же имя пользователя, которое работает на этом портале.

---

## <a name="invalid_grant-or-unauthorized_client-50053"></a>invalid_grant или unauthorized_client, 50053

### <a name="issue"></a>Проблема

Учетная запись пользователя заблокирована, код ошибки 50053. Сообщение об ошибке выглядит следующим образом:

```
{"error":"unauthorized_client","error_description":"AADSTS50053: You've tried to sign in too many times with an incorrect user ID or password.\r\nTrace ID: 844ac5d8-8160-4dee-90ce-6d8c9443d400\r\nCorrelation ID: 23fe8867-0e8f-4e56-8764-0cdc7c61c325\r\nTimestamp: 2019-06-06 09:47:23Z","error_codes":[50053],"timestamp":"2019-06-06 09:47:23Z","trace_id":"844ac5d8-8160-4dee-90ce-6d8c9443d400","correlation_id":"23fe8867-0e8f-4e56-8764-0cdc7c61c325"}
```

### <a name="cause"></a>Причина:

Слишком много попыток входа с неверным паролем.

### <a name="resolution"></a>Способы устранения:

Подождите 30 минут или закройте все приложения, которые могут попытаться пройти проверку подлинности.

---

## <a name="invalid_grant-or-unauthorized_client-50053"></a>invalid_grant или unauthorized_client, 50053

### <a name="issue"></a>Проблема

Срок действия пароля истек, код ошибки 50053. Сообщение об ошибке выглядит следующим образом:

```
{"error":"user_password_expired","error_description":"AADSTS50055: Password is expired.\r\nTrace ID: 241a7a47-e59f-42d8-9263-fbb7c1d51e00\r\nCorrelation ID: c7fe4a42-67e4-4acd-9fb6-f4fb6db76d6a\r\nTimestamp: 2019-06-06 17:29:37Z","error_codes":[50055],"timestamp":"2019-06-06 17:29:37Z","trace_id":"241a7a47-e59f-42d8-9263-fbb7c1d51e00","correlation_id":"c7fe4a42-67e4-4acd-9fb6-f4fb6db76d6a","suberror":"user_password_expired","password_change_url":"https://portal.microsoftonline.com/ChangePassword.aspx"}
```

### <a name="cause"></a>Причина:

Срок действия пароля истек.

### <a name="resolution"></a>Способы устранения:

Измените пароль в портал Azure (в локальной системе), а затем подождите 30 минут, пока синхронизация будет продолжена.

---

## <a name="interaction_required"></a>interaction_required

### <a name="issue"></a>Проблема

Получение сообщения об ошибке `interaction_required`.

### <a name="cause"></a>Причина:

Политика условного доступа или MFA применяется к пользователю. Так как интерактивная проверка подлинности еще не поддерживается, пользователь или кластер необходимо исключить из процедуры MFA или условного доступа. Если вы решили исключить кластер (политика исключения на основе IP-адреса), убедитесь, что `ServiceEndpoints` Active Directory включены для этой виртуальной сети.

### <a name="resolution"></a>Способы устранения:

Используйте политику условного доступа и исключайте кластеры Хдинисгхт из MFA, как показано в подокне [Настройка кластера HDInsight с корпоративный пакет безопасности с помощью доменных служб Azure Active Directory](./apache-domain-joined-configure-using-azure-adds.md).

---

## <a name="sign-in-denied"></a>Вход запрещен

### <a name="issue"></a>Проблема

Вход запрещен.

### <a name="cause"></a>Причина:

Чтобы перейти к этому этапу, проверка подлинности OAuth не является проблемой, но используется проверка подлинности Kerberos. Если этот кластер поддерживается ADLS, то вход в OAuth выполнен до попытки проверки подлинности Kerberos. В кластерах WASB попытки входа OAuth не предпринимались. Для хэшей паролей с ошибками Kerberos может быть не синхронизирована, учетная запись пользователя заблокирована в Azure AD DS и т. д. Хэши паролей синхронизируются, только когда пользователь изменяет пароль. При создании экземпляра AD DS Azure начнется синхронизация паролей, измененных после создания. Он не задним числом синхронизацию паролей, которые были заданы до его порождения.

### <a name="resolution"></a>Способы устранения:

Если вы считаете, что пароли могут не синхронизироваться, попробуйте изменить пароль и подождите несколько минут, чтобы синхронизироваться.

Попробуйте выполнить проверку подлинности по протоколу SSH. попытайтесь выполнить аутентификацию (kinit) с использованием тех же учетных данных пользователя на компьютере, присоединенном к домену. Подключитесь к головному или пограничному узлу по протоколу SSH с помощью локального пользователя, а затем запустите kinit.

---

## <a name="kinit-fails"></a>Сбой kinit

### <a name="issue"></a>Проблема

Сбой kinit.

### <a name="cause"></a>Причина:

Различать.

### <a name="resolution"></a>Способы устранения:

Чтобы kinit, необходимо знать `sAMAccountName` (это короткое имя учетной записи без области). `sAMAccountName` — это, как правило, префикс учетной записи (например, Боб в `bob@contoso.com`). Для некоторых пользователей это может отличаться. Для изучения `sAMAccountName`вам потребуется возможность просмотра и поиска в каталоге.

Способы поиска `sAMAccountName`:

* Если вы можете войти в Ambari с помощью локального администратора Ambari, просмотрите список пользователей.

* При наличии компьютера, [присоединенного к домену](../../active-directory-domain-services/manage-domain.md), можно использовать стандартные средства Windows AD для просмотра. Для этого требуется Рабочая учетная запись в домене.

* С головного узла можно использовать команды SAMBA для поиска. Для этого требуется допустимый сеанс Kerberos (успешный kinit). Поиск баннеров в Интернете "(userPrincipalName = Bob *)"

    Результаты поиска и обзора должны показывать атрибут `sAMAccountName`. Кроме того, можно просмотреть другие атрибуты, такие как `pwdLastSet`, `badPasswordTime``userPrincipalName` и т. д., чтобы узнать, соответствуют ли эти свойства вашим ожиданиям.

---

## <a name="kinit-fails-with-preauthentication-failure"></a>Сбой kinit с ошибкой предварительной проверки подлинности

### <a name="issue"></a>Проблема

Kinit завершается с ошибкой `Preauthentication`.

### <a name="cause"></a>Причина:

Неправильное имя пользователя или пароль.

### <a name="resolution"></a>Способы устранения:

Проверьте имя пользователя и пароль. Также проверьте другие свойства, описанные выше. Чтобы включить подробную отладку, запустите `export KRB5_TRACE=/tmp/krb.log` из сеанса, прежде чем пытаться kinit.

---

## <a name="job--hdfs-command-fails-due-to-tokennotfoundexception"></a>Сбой команды задания или HDFS из-за Токеннотфаундексцептион

### <a name="issue"></a>Проблема

Сбой команды Job/HDFS из-за `TokenNotFoundException`.

### <a name="cause"></a>Причина:

Необходимый маркер доступа OAuth не найден для завершения задания или команды. Драйвер ADLS/АБФС попытается получить маркер доступа OAuth из службы учетных данных перед выполнением запросов к хранилищу. Этот маркер регистрируется при входе на портал Ambari с помощью того же пользователя.

### <a name="resolution"></a>Способы устранения:

Убедитесь, что вы успешно выполнили вход на портал Ambari через имя пользователя, удостоверение которого используется для запуска задания.

---

## <a name="error-fetching-access-token"></a>Ошибка при выборке маркера доступа

### <a name="issue"></a>Проблема

Пользователь получает сообщение об ошибке `Error fetching access token`.

### <a name="cause"></a>Причина:

Эта ошибка возникает периодически, когда пользователи пытаются получить доступ к ADLS 2-го поколения с помощью списков ACL, и срок действия маркера Kerberos истек.

### <a name="resolution"></a>Способы устранения:

* Для Azure Data Lake Storage 1-го поколения очистите кэш браузера и снова войдите в Ambari.

* Для Azure Data Lake Storage 2-го поколения запустите `/usr/lib/hdinsight-common/scripts/RegisterKerbWithOauth.sh <upn>` для пользователя, от имени которого пользователь пытается выполнить вход.

---

## <a name="next-steps"></a>Дополнительная информация

Если вы не видите своего варианта проблемы или вам не удается ее устранить, дополнительные сведения можно получить, посетив один из следующих каналов.

* Получите ответы от экспертов Azure через [службу поддержки сообщества Azure](https://azure.microsoft.com/support/community/).

* Подключайтесь с [@AzureSupport](https://twitter.com/azuresupport) — официальная учетная запись Microsoft Azure для улучшения качества взаимодействия с клиентами. Подключение сообщества Azure к нужным ресурсам: ответы, поддержка и эксперты.

* Если вам нужна дополнительная помощь, можно отправить запрос в службу поддержки из [портал Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Выберите пункт **Поддержка** в строке меню или откройте центр **справки и поддержки** . Для получения более подробных сведений см. статью [о создании запроса на поддержку Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Доступ к поддержке в рамках управления подпиской и выставления счетов включен в вашу подписку Microsoft Azure, а техническая поддержка предоставляется через тот или иной [план поддержки Azure](https://azure.microsoft.com/support/plans/).
