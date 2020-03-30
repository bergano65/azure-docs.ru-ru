---
title: Проблемы аутентификации в Azure HDInsight
description: Проблемы аутентификации в Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 11/08/2019
ms.openlocfilehash: 26eec9cdd327ceb51e72deb1d6f40d585ce368fb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75896140"
---
# <a name="authentication-issues-in-azure-hdinsight"></a>Проблемы аутентификации в Azure HDInsight

В этой статье описаны шаги устранения неполадок и возможные решения проблем при взаимодействии с кластерами Azure HDInsight.

На защищенных кластерах, поддерживаемых Azure Data Lake (Gen1 или Gen2), когда пользователи домена вступят в кластерные службы через HDI Gateway (например, войдут в систему портала Apache Ambari), HDI Gateway попытается получить токен OAuth из Active Directory Azure (Azure AD) сначала , а затем получить билет Kerberos от Azure AD DS. Проверка подлинности может выполнить сяопротезирование на любом из этих этапов. Эта статья направлена на отладку некоторых из этих проблем.

Когда проверка подлинности не удается, вы получите запрос на учетные данные. Если вы отмените этот диалог, сообщение об ошибке будет напечатано. Вот некоторые из распространенных сообщений об ошибках:

## <a name="invalid_grant-or-unauthorized_client-50126"></a>invalid_grant или unauthorized_client, 50126

### <a name="issue"></a>Проблема

Войти в неудачи для федеративных пользователей с кодом ошибки 50126 (ввеч удается для пользователей облака). Сообщение об ошибке похоже на:

```
Reason: Bad Request, Detailed Response: {"error":"invalid_grant","error_description":"AADSTS70002: Error validating credentials. AADSTS50126: Invalid username or password\r\nTrace ID: 09cc9b95-4354-46b7-91f1-efd92665ae00\r\n Correlation ID: 4209bedf-f195-4486-b486-95a15b70fbe4\r\nTimestamp: 2019-01-28 17:49:58Z","error_codes":[70002,50126], "timestamp":"2019-01-28 17:49:58Z","trace_id":"09cc9b95-4354-46b7-91f1-efd92665ae00","correlation_id":"4209bedf-f195-4486-b486-95a15b70fbe4"}
```

### <a name="cause"></a>Причина

Код ошибки Azure AD 50126 означает, что `AllowCloudPasswordValidation` политика не была установлена арендатором.

### <a name="resolution"></a>Решение

Администратор компании— арендатор Azure AD должен позволить Azure AD использовать хэши паролей для пользователей, поддерживаемых ADFS.  Примените, `AllowCloudPasswordValidationPolicy` как показано в статье [Использование пакета корпоративной безопасности в HDInsight](../domain-joined/apache-domain-joined-architecture.md).

---

## <a name="invalid_grant-or-unauthorized_client-50034"></a>invalid_grant или unauthorized_client, 50034

### <a name="issue"></a>Проблема

Войти в сбой с кодом ошибки 50034. Сообщение об ошибке похоже на:

```
{"error":"invalid_grant","error_description":"AADSTS50034: The user account Microsoft.AzureAD.Telemetry.Diagnostics.PII does not exist in the 0c349e3f-1ac3-4610-8599-9db831cbaf62 directory. To sign into this application, the account must be added to the directory.\r\nTrace ID: bbb819b2-4c6f-4745-854d-0b72006d6800\r\nCorrelation ID: b009c737-ee52-43b2-83fd-706061a72b41\r\nTimestamp: 2019-04-29 15:52:16Z", "error_codes":[50034],"timestamp":"2019-04-29 15:52:16Z","trace_id":"bbb819b2-4c6f-4745-854d-0b72006d6800", "correlation_id":"b009c737-ee52-43b2-83fd-706061a72b41"}
```

### <a name="cause"></a>Причина

Имя пользователя неправильно (не существует). Пользователь не использует то же имя пользователя, что используется на портале Azure.

### <a name="resolution"></a>Решение

Используйте одно и то же имя пользователя, которое работает на этом портале.

---

## <a name="invalid_grant-or-unauthorized_client-50053"></a>invalid_grant или unauthorized_client, 50053

### <a name="issue"></a>Проблема

Учетная запись пользователя заблокирована, код ошибки 50053. Сообщение об ошибке похоже на:

```
{"error":"unauthorized_client","error_description":"AADSTS50053: You've tried to sign in too many times with an incorrect user ID or password.\r\nTrace ID: 844ac5d8-8160-4dee-90ce-6d8c9443d400\r\nCorrelation ID: 23fe8867-0e8f-4e56-8764-0cdc7c61c325\r\nTimestamp: 2019-06-06 09:47:23Z","error_codes":[50053],"timestamp":"2019-06-06 09:47:23Z","trace_id":"844ac5d8-8160-4dee-90ce-6d8c9443d400","correlation_id":"23fe8867-0e8f-4e56-8764-0cdc7c61c325"}
```

### <a name="cause"></a>Причина

Слишком много подписей в попытках с неправильным паролем.

### <a name="resolution"></a>Решение

Подождите 30 минут или около того, остановите любые приложения, которые могут попытаться проверить подлинность.

---

## <a name="invalid_grant-or-unauthorized_client-50053"></a>invalid_grant или unauthorized_client, 50053

### <a name="issue"></a>Проблема

Пароль истек, код ошибки 50053. Сообщение об ошибке похоже на:

```
{"error":"user_password_expired","error_description":"AADSTS50055: Password is expired.\r\nTrace ID: 241a7a47-e59f-42d8-9263-fbb7c1d51e00\r\nCorrelation ID: c7fe4a42-67e4-4acd-9fb6-f4fb6db76d6a\r\nTimestamp: 2019-06-06 17:29:37Z","error_codes":[50055],"timestamp":"2019-06-06 17:29:37Z","trace_id":"241a7a47-e59f-42d8-9263-fbb7c1d51e00","correlation_id":"c7fe4a42-67e4-4acd-9fb6-f4fb6db76d6a","suberror":"user_password_expired","password_change_url":"https://portal.microsoftonline.com/ChangePassword.aspx"}
```

### <a name="cause"></a>Причина

Срок действия пароля истек.

### <a name="resolution"></a>Решение

Измените пароль на портале Azure (в вашей предварительной системе), а затем подождите 30 минут, пока синхронизация наверстать упущенное.

---

## <a name="interaction_required"></a>interaction_required

### <a name="issue"></a>Проблема

Получение сообщения `interaction_required`об ошибке .

### <a name="cause"></a>Причина

Политика условного доступа или MFA применяется к пользователю. Так как интерактивная проверка подлинности еще не поддерживается, пользователь или кластер необходимо исключить из процедуры MFA или условного доступа. Если вы решите освободить кластер (политика освобождения от IP-адреса), убедитесь, что AD `ServiceEndpoints` включен для этого vnet.

### <a name="resolution"></a>Решение

Используйте политику условного доступа и освобождайте кластеры HDInisght от MFA, как показано в [кластере HdInsight с пакетом корпоративной безопасности, используя службы azure Active Directory Domain Domain.](./apache-domain-joined-configure-using-azure-adds.md)

---

## <a name="sign-in-denied"></a>Войти в отказано

### <a name="issue"></a>Проблема

Вход в систему отказано.

### <a name="cause"></a>Причина

Чтобы добраться до этой стадии, ваша проверка подлинности OAuth не является проблемой, но проверка подлинности Kerberos является. Если этот кластер поддерживается ADLS, OAuth знак в удалось до Kerberos auth попытка. На кластерах WASB, OAuth войти не пытается. Причин сбоя Kerberos может быть много - например, хэши паролей не синхронизированы, учетная запись пользователя заблокирована в Azure AD DS и так далее. Хэши пароля синхронизируются только при изменении пароля. При создании экземпляра Azure AD DS начнется синхронизация паролей, которые изменяются после создания. Он не будет ретроактивно синхронизировать пароли, которые были установлены до его создания.

### <a name="resolution"></a>Решение

Если вы считаете, что пароли не могут быть синхронизированы, попробуйте изменить пароль и подождите несколько минут, чтобы синхронизировать.

Попробуйте SSH в Вас нужно будет попытаться проверить подлинность (kinit) с помощью тех же учетных данных пользователей, от машины, которая соединена с доменом. SSH в голову / край узла с местным пользователем, а затем запустить kinit.

---

## <a name="kinit-fails"></a>kinit терпит неудачу

### <a name="issue"></a>Проблема

Кинит терпит неудачу.

### <a name="cause"></a>Причина

Возможны разные варианты.

### <a name="resolution"></a>Решение

Для kinit для достижения успеха, `sAMAccountName` вы должны знать ваш (это короткое имя счета без области). `sAMAccountName`как правило, приставка учетной `bob@contoso.com`записи (например, Боб в). Для некоторых пользователей, это может быть по-другому. Вам понадобится возможность просматривать / поиск каталога, чтобы узнать ваш `sAMAccountName`.

Способы `sAMAccountName`поиска:

* Если вы можете войти в Ambari с помощью местного амбари админ, посмотрите на список пользователей.

* Если у вас есть [домен, соединенный машиной Windows,](../../active-directory-domain-services/manage-domain.md)вы можете использовать стандартные инструменты Windows AD для просмотра. Для этого требуется рабочая учетная запись в домене.

* Из головного узла можно использовать команды SAMBA для поиска. Для этого требуется действительная сессия Kerberos (успешный кинит). поиск чистой рекламы "(userPrincipalName-bob)"

    Результаты поиска/просмотра должны `sAMAccountName` показать вам атрибут. Кроме того, вы можете посмотреть `badPasswordTime` `userPrincipalName` на другие атрибуты, как, `pwdLastSet`и т.д., чтобы увидеть, если эти свойства соответствуют тому, что вы ожидаете.

---

## <a name="kinit-fails-with-preauthentication-failure"></a>Kinit не удается с сбоем преапонации

### <a name="issue"></a>Проблема

Kinit терпит `Preauthentication` неудачу с отказом.

### <a name="cause"></a>Причина

Неправильное имя пользователя или пароль.

### <a name="resolution"></a>Решение

Проверьте имя пользователя и пароль. Также проверьте другие свойства, описанные выше. Для обеспечения многословной отладки, выполнить `export KRB5_TRACE=/tmp/krb.log` от сессии, прежде чем пытаться kinit.

---

## <a name="job--hdfs-command-fails-due-to-tokennotfoundexception"></a>Задание / команда HDFS выходит из строя из-за TokenNotFoundException

### <a name="issue"></a>Проблема

Задание / команда HDFS `TokenNotFoundException`выходит из строя из-за .

### <a name="cause"></a>Причина

Необходимый токен доступа OAuth не был найден для выполнения работы/команды для достижения успеха. Водитель ADLS / ABFS попытается получить токен доступа OAuth из службы учетных данных, прежде чем делать запросы на хранение. Этот маркер регистрируется при входе на портал Ambari с помощью одного и того же пользователя.

### <a name="resolution"></a>Решение

Убедитесь, что вы успешно вошли в систему портала Ambari один раз через имя пользователя, личность которого используется для выполнения задания.

---

## <a name="error-fetching-access-token"></a>Ошибка извлечения маркера доступа

### <a name="issue"></a>Проблема

Пользователь получает `Error fetching access token`сообщение об ошибке .

### <a name="cause"></a>Причина

Эта ошибка возникает с перерывами, когда пользователи пытаются получить доступ к ADLS Gen2 с помощью ACLs и маркер Kerberos истек.

### <a name="resolution"></a>Решение

* Для Azure Data Lake Storage 1 очиститесь браузера и снова войдите в Ambari.

* Для хранения azure Data Lake `/usr/lib/hdinsight-common/scripts/RegisterKerbWithOauth.sh <upn>` Data Gen2 выполнить для пользователя, который пользователь пытается войти в систему

---

## <a name="next-steps"></a>Дальнейшие действия

Если вы не видите своего варианта проблемы или вам не удается ее устранить, дополнительные сведения можно получить, посетив один из следующих каналов.

* Получите ответы от экспертов Azure через [поддержку сообщества Azure.](https://azure.microsoft.com/support/community/)

* Связаться [@AzureSupport](https://twitter.com/azuresupport) с - официальная учетная запись Microsoft Azure для улучшения обслуживания клиентов. Подключение сообщества Azure к нужным ресурсам: ответы, поддержка и эксперты.

* Если вам нужна дополнительная помощь, вы можете отправить запрос на поддержку с [портала Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Выберите **поддержку** из бара меню или откройте концентратор **поддержки Справка и.** Для получения более подробной информации просмотрите [Как создать запрос поддержки Azure.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) Доступ к управлению подпиской и поддержке выставления счетов включен в подписку Microsoft Azure, а техническая поддержка обеспечивается через один из [планов поддержки Azure.](https://azure.microsoft.com/support/plans/)
