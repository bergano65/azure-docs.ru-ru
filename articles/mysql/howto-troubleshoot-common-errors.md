---
title: Устранение распространенных ошибок — База данных Azure для MySQL
description: Сведения о том, как устранять распространенные ошибки миграции, которые происходят у начинающих пользователей Базы данных Azure для MySQL
author: savjani
ms.service: mysql
ms.author: pariks
ms.custom: mvc
ms.topic: overview
ms.date: 8/20/2020
ms.openlocfilehash: ebe9f936e3d0dfafec23842fcdbfd225995d546b
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/05/2020
ms.locfileid: "88719787"
---
# <a name="common-errors"></a>Распространенные ошибки

База данных Azure для MySQL — это полностью управляемая служба на базе версии MySQL для сообщества. Функциональные возможности MySQL в среде управляемой службы могут отличаться от возможностей MySQL в вашей собственной среде. Из этой статьи вы узнаете о некоторых распространенных ошибках, с которыми могут столкнуться пользователи при миграции в службу "База данных Azure для MySQL" или осуществлении в ней разработки.

## <a name="errors-due-to-lack-of-super-privilege-and-dba-role"></a>Ошибки из-за отсутствия разрешения SUPER и роли администратора баз данных

Служба не поддерживает разрешение SUPER и роль администратора баз данных. В результате вы можете столкнуться с некоторыми распространенными ошибками, перечисленными ниже.

#### <a name="error-1419-you-do-not-have-the-super-privilege-and-binary-logging-is-enabled-you-might-want-to-use-the-less-safe-log_bin_trust_function_creators-variable"></a>ОШИБКА 1419. У вас нет разрешения SUPER, и включено ведение двоичного журнала (вы *можете* использовать менее безопасную переменную log_bin_trust_function_creators)

Такая ошибка может произойти при создании функции или триггера, как показано ниже, или при импорте схемы. Инструкции DDL, например CREATE FUNCTION или CREATE TRIGGER, записываются в двоичный журнал, поэтому их может выполнять вторичная реплика. У потока реплики SQL есть полные права доступа, которые можно использовать для повышения привилегий. Для защиты от этой опасности ядро MySQL помимо обычного обязательного разрешения CREATE ROUTINE требует для серверов с включенным ведением журнала в двоичном формате наличия разрешения SUPER. 

```sql
CREATE FUNCTION f1(i INT)
RETURNS INT
DETERMINISTIC
READS SQL DATA
BEGIN
  RETURN i;
END;
```

**Решение**.  Чтобы устранить эту ошибку, на портале в колонке [параметров сервера](howto-server-parameters.md) установите для log_bin_trust_function_creators значение 1. Запустите выполнение инструкций DDL или импортируйте схему, чтобы создать нужные объекты, и восстановите предыдущее значение параметра log_bin_trust_function_creators после создания.

#### <a name="error-1227-42000-at-line-101-access-denied-you-need-at-least-one-of-the-super-privileges-for-this-operation-operation-failed-with-exitcode-1"></a>ОШИБКА 1227 (42000) в строке 101. Доступ запрещен — для выполнения этой операции необходимо иметь (по крайней мере одно) разрешение SUPER; операция завершилась ошибкой с кодом выхода 1

Описанная выше ошибка может произойти при импорте файла дампа или создании процедуры, содержащей предложения [DEFINER](https://dev.mysql.com/doc/refman/5.7/en/create-procedure.html). 

**Решение**.  Чтобы устранить эту ошибку, пользователь с правами администратора может предоставить права на создание или выполнение процедур с помощью команды GRANT, как показано в следующих примерах.

```sql
GRANT CREATE ROUTINE ON mydb.* TO 'someuser'@'somehost';
GRANT EXECUTE ON PROCEDURE mydb.myproc TO 'someuser'@'somehost';
```
В качестве альтернативы можно заменить значения предложения DEFINER именем администратора, выполняющего процесс импорта, как показано ниже.

```sql
DELIMITER;;
/*!50003 CREATE*/ /*!50017 DEFINER=`root`@`127.0.0.1`*/ /*!50003
DELIMITER;;

/* Modified to */

DELIMITER ;;
/*!50003 CREATE*/ /*!50017 DEFINER=`AdminUserName`@`ServerName`*/ /*!50003
DELIMITER ;
```

## <a name="next-steps"></a>Следующие шаги
Если вы не нашли ответ, который искали, воспользуйтесь следующими рекомендациями.
- Опубликуйте свой вопрос на [странице вопросов и ответов на сайте Microsoft Q&A](https://docs.microsoft.com/answers/topics/azure-database-mysql.html) или [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-database-mysql).
- Отправьте сообщение электронной почты команде разработчиков службы "База данных Azure для MySQL" [по этому адресу (@Ask)](mailto:AskAzureDBforMySQL@service.microsoft.com). Учтите, что это не электронный адрес службы технической поддержки.
- Обратитесь в Службу поддержки Azure, [отправив запрос с портала Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Чтобы устранить проблему, связанную с учетной записью, отправьте [запрос в службу поддержки](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) на портале Azure.
- Чтобы отправить отзыв или отправить запрос на новые возможности, создайте запись через [UserVoice](https://feedback.azure.com/forums/597982-azure-database-for-mysql).
