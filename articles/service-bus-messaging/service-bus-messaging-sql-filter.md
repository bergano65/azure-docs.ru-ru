---
title: Справочник по синтаксису SQLFilter для служебной шины Azure | Документация Майкрософт
description: В этой статье содержатся сведения о грамматике SQLFilter. SqlFilter поддерживает подмножество стандарта SQL-92.
ms.topic: article
ms.date: 11/17/2020
ms.openlocfilehash: 7f3c744b691e678ef18c8fa721ccfaecaee9c1e2
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/19/2020
ms.locfileid: "94888476"
---
# <a name="sqlfilter-syntax"></a>Синтаксис SQLFilter

Объект *SqlFilter* является экземпляром [класса SqlFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter)и представляет критерий фильтра на основе языка SQL, который вычисляется для [`BrokeredMessage`](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) . SqlFilter поддерживает подмножество стандарта SQL-92.  
  
 В этом разделе приведены сведения о грамматике SqlFilter.  
  
```  
<predicate ::=  
      { NOT <predicate> }  
      | <predicate> AND <predicate>  
      | <predicate> OR <predicate>  
      | <expression> { = | <> | != | > | >= | < | <= } <expression>  
      | <property> IS [NOT] NULL  
      | <expression> [NOT] IN ( <expression> [, ...n] )  
      | <expression> [NOT] LIKE <pattern> [ESCAPE <escape_char>]  
      | EXISTS ( <property> )  
      | ( <predicate> )  
  
```  
  
```  
<expression> ::=  
      <constant>   
      | <function>  
      | <property>  
      | <expression> { + | - | * | / | % } <expression>  
      | { + | - } <expression>  
      | ( <expression> )  
  
```  
  
```  
<property> :=   
       [<scope> .] <property_name>  
  
```  
  
## <a name="arguments"></a>Аргументы  
  
-   `<scope>` — необязательная строка, указывающая область `<property_name>`. Допустимые значения: `sys` или `user`. `sys`Значение указывает область системы, где `<property_name>` — это имя общедоступного свойства [класса BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage). `user` Указывает область пользователя, где `<property_name>` является ключом словаря [класса BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) . `user` SCOPE является областью по умолчанию, если она `<scope>` не указана.  
  
## <a name="remarks"></a>Комментарии

Попытка доступа к несуществующему системному свойству является ошибкой, в то время как попытка доступа к несуществующему свойству пользователя не является ошибкой. Вместо этого несуществующее свойство пользователя вычисляется внутри системы как неизвестное значение. Неизвестное значение обрабатывается особым образом во время вычисления оператора.  
  
## <a name="property_name"></a>property_name  
  
```  
<property_name> ::=  
     <identifier>  
     | <delimited_identifier>  
  
<identifier> ::=  
     <regular_identifier> | <quoted_identifier> | <delimited_identifier>  
  
```  
  
### <a name="arguments"></a>Аргументы  

 `<regular_identifier>` — строка, представленная следующим регулярным выражением:  
  
```  
[[:IsLetter:]][_[:IsLetter:][:IsDigit:]]*  
```  
  
Это любая строка, которая начинается с буквы, после которой идет один или несколько символов подчеркивания, букв или цифр.  
  
`[:IsLetter:]` — любой символ Юникода, который относится к категории букв Юникода. `System.Char.IsLetter(c)` возвращает значение `true`, если `c` является буквой Юникода.  
  
`[:IsDigit:]` — любой символ Юникода, который относится к категории десятичных цифр. `System.Char.IsDigit(c)` возвращает значение `true`, если `c` является цифрой Юникода.  
  
`<regular_identifier>`Ключевое слово не может быть зарезервированным.  
  
`<delimited_identifier>` — любая строка, заключенная в квадратные скобки ([]). Закрывающая квадратная скобка представляется в виде двух закрывающих квадратных скобок. Ниже приведены примеры `<delimited_identifier>`.  
  
```  
[Property With Space]  
[HR-EmployeeID]  
  
```  
  
`<quoted_identifier>` — любая строка, заключенная в двойные кавычки. Двойные кавычки в идентификаторе представляются в виде двух пар двойных кавычек. Не рекомендуется использовать заключенные в кавычки идентификаторы, так как их легко можно путать с строковой константой. По возможности используйте идентификаторы с разделителем. Ниже приведен пример `<quoted_identifier>` .  
  
```  
"Contoso & Northwind"  
```  
  
## <a name="pattern"></a>pattern  
  
```  
<pattern> ::=  
      <expression>  
```  
  
### <a name="remarks"></a>Комментарии
  
Свойство `<pattern>` должно быть выражением, которое будет вычисляться как строка. Он используется как шаблон для оператора LIKE.      Оно может содержать следующие подстановочные знаки:  
  
-   `%` — любая строка без символов или с несколькими символами.  
  
-   `_` — любой один символ.  
  
## <a name="escape_char"></a>escape_char  
  
```  
<escape_char> ::=  
      <expression>  
```  
  
### <a name="remarks"></a>Комментарии  

Свойство `<escape_char>` должно быть выражением, которое будет вычисляться в качестве строки с 1 символом. Он используется в качестве escape-символа для оператора LIKE.  
  
 Например, `property LIKE 'ABC\%' ESCAPE '\'` соответствует `ABC%`, а не строке, начинающейся с `ABC`.  
  
## <a name="constant"></a>constant  
  
```  
<constant> ::=  
      <integer_constant> | <decimal_constant> | <approximate_number_constant> | <boolean_constant> | NULL  
```  
  
### <a name="arguments"></a>Аргументы  
  
-   `<integer_constant>` Строка чисел, которые не заключены в кавычки и не содержат десятичные разделители. Значения хранятся в виде `System.Int64` внутри системы и попадают в тот же диапазон.  
  
     Ниже приведены примеры длинных констант.  
  
    ```  
    1894  
    2  
    ```  
  
-   `<decimal_constant>` Строка чисел, которые не заключаются в кавычки и содержат десятичную запятую. Значения хранятся в виде `System.Double` внутри системы и попадают в тот же диапазон и точность.  
  
     В следующей версии это число может храниться в другом типе данных для поддержки точной семантики чисел, поэтому не следует полагаться на тот факт, что базовым типом данных `System.Double` является `<decimal_constant>` .  
  
     Ниже приведены примеры десятичных констант.  
  
    ```  
    1894.1204  
    2.0  
    ```  
  
-   `<approximate_number_constant>` — число, записанное в экспоненциальном представлении чисел. Значения хранятся в виде `System.Double` внутри системы и попадают в тот же диапазон и точность. Ниже приведены примеры констант с приближенными числами.  
  
    ```  
    101.5E5  
    0.5E-2  
    ```  
  
## <a name="boolean_constant"></a>boolean_constant  
  
```  
<boolean_constant> :=  
      TRUE | FALSE  
```  
  
### <a name="remarks"></a>Комментарии  

Логические константы представлены в виде ключевого слова **TRUE** или **FALSE**. Значения хранятся в виде `System.Boolean`.  
  
## <a name="string_constant"></a>string_constant  
  
```  
<string_constant>  
```  
  
### <a name="remarks"></a>Комментарии  

Строковые константы заключаются в одинарные кавычки и включают любые допустимые символы Юникода. Одинарная кавычка, внедренная в строковую константу, представляется в виде двух одинарных кавычек.  
  
## <a name="function"></a>function  
  
```  
<function> :=  
      newid() |  
      property(name) | p(name)  
```  
  
### <a name="remarks"></a>Комментарии
  
`newid()`Функция возвращает объект, `System.Guid` сформированный `System.Guid.NewGuid()` методом.  
  
Функция `property(name)` возвращает значение свойства, на которое указывает `name`. В качестве значения `name` может использоваться любое допустимое выражение, возвращающее строковое значение.  
  
## <a name="considerations"></a>Рекомендации
  
Рассмотрим следующую семантику [SqlFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter):  
  
-   В именах свойств не учитывается регистр.  
  
-   Во всех сценариях операторы следуют семантике неявного преобразования C#.  
  
-   Системные свойства являются общедоступными свойствами, используемыми в экземплярах [BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage).  
  
    Рассмотрим следующую семантику `IS [NOT] NULL`:  
  
    -   `property IS NULL` возвращает значение `true`, если свойство не существует или его значение равно `null`.  
  
### <a name="property-evaluation-semantics"></a>Семантика оценки свойств  
  
- При попытке вычислить несуществующее системное свойство будет вызвано исключение [FilterException](/dotnet/api/microsoft.servicebus.messaging.filterexception).  
  
- Несуществующее свойство внутренне оценивается как **неизвестное**.  
  
  Вычисление неизвестного свойства в арифметических операторах:  
  
- Для бинарных операторов, если левая или правая часть операндов вычисляется как **неизвестная**, результат будет **неизвестным**.  
  
- Если операнд вычисляется как **неизвестный**, то результат тоже **неизвестный** (для унарных операторов).  
  
  Неизвестный результат вычисления в двоичных операторах сравнения:  
  
- Если левая или правая часть операндов вычисляется как **неизвестная**, то результат будет **неизвестным**.  
  
  Неизвестный результат вычисления в `[NOT] LIKE`:  
  
- Если какой-либо операнд вычисляется как **неизвестный**, результат будет **неизвестным**.  
  
  Неизвестный результат вычисления в `[NOT] IN`:  
  
- Если левый операнд вычисляется как **неизвестный**, результат будет **неизвестным**.  
  
  Неизвестный результат вычисления в операторе **AND**:  
  
```  
+---+---+---+---+  
|AND| T | F | U |  
+---+---+---+---+  
| T | T | F | U |  
+---+---+---+---+  
| F | F | F | F |  
+---+---+---+---+  
| U | U | F | U |  
+---+---+---+---+  
```  
  
 Неизвестный результат вычисления в операторе **OR**:  
  
```  
+---+---+---+---+  
|OR | T | F | U |  
+---+---+---+---+  
| T | T | T | T |  
+---+---+---+---+  
| F | T | F | U |  
+---+---+---+---+  
| U | T | U | U |  
+---+---+---+---+  
```  
  
### <a name="operator-binding-semantics"></a>Семантика привязки операторов
  
-   Операторы сравнения, такие как `>`, `>=`, `<`, `<=`, `!=`, и `=` следуют той же семантике, что и операторы C#: связываются повышения типов данных и неявные преобразования.  
  
-   Арифметические операторы, такие как `+`, `-`, `*`, `/` и `%` следуют той же семантике, что и операторы C#: связываются повышения типов данных и неявные преобразования.


## <a name="examples"></a>Примеры

### <a name="set-rule-action-for-a-sql-filter"></a>Задание действия правила для фильтра SQL

```csharp
// instantiate the ManagementClient
this.mgmtClient = new ManagementClient(connectionString);

// create the SQL filter
var sqlFilter = new SqlFilter("source = @stringParam");

// assign value for the parameter
sqlFilter.Parameters.Add("@stringParam", "orders");

// instantiate the Rule = Filter + Action
var filterActionRule = new RuleDescription
{
    Name = "filterActionRule",
    Filter = sqlFilter,
    Action = new SqlRuleAction("SET source='routedOrders'")
};

// create the rule on Service Bus
await this.mgmtClient.CreateRuleAsync(topicName, subscriptionName, filterActionRule);
```

### <a name="sql-filter-on-a-system-property"></a>Фильтр SQL для системного свойства

```csharp
sys.Label LIKE '%bus%'`
```

### <a name="using-or"></a>Использование или 

```csharp
 sys.Label LIKE '%bus%'` OR `user.tag IN ('queue', 'topic', 'subscription')
```

### <a name="using-in-and-not-in"></a>Использование IN и NOT IN

```csharp
StoreId IN('Store1', 'Store2', 'Store3')"

sys.To IN ('Store5','Store6','Store7') OR StoreId = 'Store8'

sys.To NOT IN ('Store1','Store2','Store3','Store4','Store5','Store6','Store7','Store8') OR StoreId NOT IN ('Store1','Store2','Store3','Store4','Store5','Store6','Store7','Store8')
```

Пример для C# см. [в разделе Пример фильтров разделов на сайте GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Azure.Messaging.ServiceBus/BasicSendReceiveTutorialwithFilters).


## <a name="next-steps"></a>Дальнейшие действия

- [Класс SQLFilter (.NET Framework)](/dotnet/api/microsoft.servicebus.messaging.sqlfilter)
- [Класс SQLFilter (.NET Standard)](/dotnet/api/microsoft.azure.servicebus.sqlfilter)
- [SQLRuleAction class](/dotnet/api/microsoft.servicebus.messaging.sqlruleaction) (Класс SQLRuleAction)
