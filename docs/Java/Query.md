<a name="Query"></a>

## Query
Класс для вборки документов коллекции.

**Содержание**

* [Query](#Query)
    * [new Query(name)](#Query_new)
    * [.findDocuments(callback)](#Query+findDocuments)
    * [.countDocuments(callback)](#Query+countDocuments)
    * [.updateDocument(update, callback)](#Query+updateDocument)
    * [.removeDocument(callback)](#Query+removeDocument)
    * [.setLimit(limit)](#Query+setLimit)
    * [.setSkip(skip)](#Query+setSkip)
    * [.setPage(page)](#Query+setPage)
    * [.equalTo(field, value)](#Query+equalTo)
    * [.notEqualTo(field, value)](#Query+notEqualTo)
    * [.containedIn(field, values)](#Query+containedIn)
    * [.containsAll(field, values)](#Query+containsAll)
    * [.notContainedIn(field, values)](#Query+notContainedIn)
    * [.greaterThan(field, value)](#Query+greaterThan)
    * [.greaterThenOrEqualTo(field, value)](#Query+greaterThenOrEqualTo)
    * [.lessThan(field,  value)](#Query+lessThan)
    * [.lessThanOrEqualTo(field, value)](#Query+lessThanOrEqualTo)
    * [.exists(field)](#Query+exists)
    * [.doesNotExist(field)](#Query+doesNotExist)
    * [.contains(field, regEx, options)](#Query+contains)
    * [.startsWith(field, regEx, options)](#Query+startsWith)
    * [.endsWith(field, regEx, options)](#Query+endsWith)
    * [.and(field, query)](#Query+and)
    * [.or(field, query)](#Query+or)
    * [.raw(json)](#Query+raw)
    * [.reset()](#Query+reset)
    * [.ascending(field)](#Query+ascending)
    * [.descending(field)](#Query+descending)
    * [.setFieldsForSearch(fields)](#Query+setFieldsForSearch)
    * [.getQueryInfo()](#Query+getQueryInfo)

---------------------------------------------------------------------------------------------
<a name="Query_new"></a>

### new Query()

Initialisation of a collection data query

| Parameter | Type | Properties | Description | Value example |
|-----------|------|------------|-------------|---------------|
|  collection_name  | `String`  |    | Collection name |  "things"  |

**Example** 

```Java
Query query = new Query("name");
```
---------------------------------------------------------------------------------------------

<a name="Query+findDocuments"></a>
### .findDocuments(callback)
Method for requesting a document from a collection. Returns data of the objects that match the sampling criteria. 


| Parameter | Type | Properties | Description | Value example |
|-----------|------|------------|-------------|---------------|
| callback | `CallbackFindDocument` |    Mandatory | Callback for the request being executed.|    |


!!! note "Примечание"
    Если вы хотите чтобы вместе с id найденных документов возвращались их поля, задайте название необходимых полей с помощью функции `setFieldsForSearch(fields)`

!!! note "Note"
    If no criteria are set, the first 50 objects of the collection are returned by default.

**Example** 

```Java
Query query = new Query(“mycollection”)
                .equalTo("number3", 10)
                .exists("number2");

query.findDocuments(new CallbackFindDocument() {
            @Override
            public void onDocumentFound(List<DocumentInfo> documentInfos) {
                //found. See document list what match query
            }

            @Override
            public void onDocumentNotFound(String errorCode, String errorMessage) {
                //no documents what match query
            }
        });
```
---------------------------------------------------------------------------------------------

<a name="Query+countDocuments"></a>
### .countDocuments(callback)

Method for counting objects that meet the query conditions.

| Parameter | Type | Properties | Description | Value example |
|-----------|------|------------|-------------|---------------|
| callback | `CallbackCountDocument ` |                                            Mandatory | Callback for the request being executed.|    |


**Example** 

```Java
Query query = new Query(“mycollection”);
        query.greaterThan("rating", 10);

        query.countDocuments(new CallbackCountDocument() {
            @Override
            public void onDocumentsCounted(ResponseCount responseCount) {
                //see responseCount.getResult() to find how many documents was found.
            }

            @Override
            public void onCountFailed(String errorCode, String errorMessage) {
                //error during count
            }
        });
```
---------------------------------------------------------------------------------------------

<a name="Query+updateDocument"></a>
### .updateDocument(update, callback)

Method for updating the requested objects.

| Parameter | Type | Properties | Description | Value example |
|-----------|------|------------|-------------|---------------|
| update | `Update` | Mandatory | Update object  |   |
| callback | `CallbackUpdateDocument` | Mandatory |  Callback for the request being executed.  |  |

!!! note "Примечание"
    Данный метод обновляет не более 1000 документов

**Example** 

```Java
 Query query = new Query(“mycollection”);
        query.equalTo("number3", 10);

        Update update = new Update()
                .set("number2", 199)
                .set("numberField", 111)
                .addToSet("array1", 900);

        query.updateDocument(update, new CallbackUpdateDocument() {
            @Override
            public void onUpdateSucceed(ResponseUpdate responseUpdate) {
                //documents updated successful
            }

            @Override
            public void onUpdateFailed(String errorCode, String errorMessage) {
                //error during update
            }
        });
```
---------------------------------------------------------------------------------------------

<a name="Query+removeDocument"></a>
### .removeDocument(callback)

Method for removing the requested documents.

| Parameter | Type | Properties | Description | Value example |
|-----------|------|------------|-------------|---------------|
| callback | `CallbackRemoveDocument` | Mandatory |  Callback for the request being executed.  |  |

!!! note "Примечание"
    Данный метод удаляет не более 1000 документов

**Example** 

```Java
Query query = new Query(“mycollection”);
        query.equalTo("_id", "aJfkipJags");

        query.removeDocument(new CallbackRemoveDocument() {
            @Override
            public void onRemoveSucceed(ResponseRemove responseRemove) {
                //succeed. See responseRemove to findout how many documents was removed
            //and get list of removed documents
            }

            @Override
            public void onRemoveFailed(String errorCode, String errorMessage) {
                //error during remove operation
            }
        });

```
---------------------------------------------------------------------------------------------

<a name="Query+setLimit"></a>
### .setLimit(limit)

Method for specifying a limit for the number of sampling, updating or removing documents.

| Parameter | Type | Properties | Description | Value example |
|-----------|------|------------|-------------|---------------|
| limit | `Integer` |   Mandatory | Limit | 15  |

!!! note "Note"
    Limit defaults to 50, but anything from 1 to 1000 is a valid limit.

**Example** 

```Java
Query query = new Query(“mycollection”);
query.setLimit(15);
//query.findDocuments(…);
```
---------------------------------------------------------------------------------------------

<a name="Query+setSkip"></a>
### .setSkip(skip)

Метод для пропуска части объектов перед возвратом результата совершенной выборки

| Parameter | Type | Properties | Description | Value example |
|-----------|------|------------|-------------|---------------|
| skip | `Integer` | Mandatory | Количество пропускаемых документов | 100  |

**Example** 

```Java
Query query = new Query(“mycollection”);
query.setSkip(12);
//query.findDocuments(…);
```
---------------------------------------------------------------------------------------------

<a name="Query+setPage"></a>
### .setPage(page)

Метод для постраничного вывода результатов выборки

| Parameter | Type | Properties | Description | Value example |
|-----------|------|------------|-------------|---------------|
| page | `Integer` |  Mandatory | Номер страницы | 2  |

**Example** 

```Java
Query query = new Query(“mycollection”);
//query.setLimit(15);
query.setPage(1);
//query.findDocuments(…);
```
---------------------------------------------------------------------------------------------

<a name="Query+equalTo"></a>
### .equalTo(field, value)

Метод для добавления условия выборки: только документы для которых значение поля равно значению в условии выборки.


| Parameter | Type | Properties | Description | Value example |
|-----------|------|------------|-------------|---------------|
| field | `String` | Mandatory | Имя поля, которому задается условие | "orderNumber"   |
| value | `Object` | Mandatory |  Значение для сравнения                   |  22 |

**Example** 

```Java
Query query = new Query(“mycollection”);
query.equalTo(“orderNumber”, 22);
//query.findDocuments(…);
```

!!! note "Примечание"
    Если вы пытаетесь задать данное условие для поля id документа, помните, что, поскольку id – первичный ключ для документа, вам нужно добавить “_” перед именем поля.

```Java
Query query = new Query(“mycollection”);
query.equalTo(“_id”, “dasds12dskm”);
//query.findDocuments(…);
```

---------------------------------------------------------------------------------------------

<a name="Query+notEqualTo"></a>
### .notEqualTo(field, value)

Метод для добавления условия выборки: только документы для которых значение поля не равно значению в условии выборки.

| Parameter | Type | Properties | Description | Value example |
|-----------|------|------------|-------------|---------------|
| field | `String` |                                            Mandatory | Имя поля, которому задается условие | "orderNumber"   |
| value | `Object` | Mandatory | Значение для сравнения                   |  22 |


**Example** 

```Java
Query query = new Query(“mycollection”);
query.notEqualTo(“orderNumber”, 22);
//query.findDocuments(…);
```

!!! note "Примечание"
    Если вы пытаетесь задать данное условие для поля id документа, помните, что, поскольку id – первичный ключ для документа, вам нужно добавить “_” перед именем поля.

```Java
Query query = new Query(“mycollection”);
query.equalTo(“_id”, “dasds12dskm”);
//query.findDocuments(…);
```

---------------------------------------------------------------------------------------------

<a name="Query+containedIn"></a>
### .containedIn(field, values)

Метод для добавления условия выборки: только документы у которых значение поля совпадает с одним из значений заданного массива.


| Parameter | Type | Properties | Description | Value example |
|-----------|------|------------|-------------|---------------|
| field | `String` |                                            Mandatory | Имя поля, которому задается условие | "orderNumbers"   |
| value | `List<тип поля>` | Mandatory |  Массив элементов                  |  см.пример ниже |

**Example** 

```Java
List<Object> numbers = new ArrayList<>();
        numbers.add(1);
        numbers.add(5);
        numbers.add(10);
        numbers.add(15);

Query query = new Query(“mycollection”).containedIn("number3", numbers);
//query.findDocuments(…);
```
---------------------------------------------------------------------------------------------

<a name="Query+containsAll"></a>
### .containsAll(field, values)

Метод для добавления условия выборки:только документы у которых поле (типа массив) содержит все элементы заданного массива

| Parameter | Type | Properties | Description | Value example |
|-----------|------|------------|-------------|---------------|
| field | `String` |                                            Mandatory | Имя поля, которому задается условие | "orderNumbers"   |
| value | `List<тип поля>` | Mandatory |  Массив элементов                  |  см.пример ниже |

**Example** 

```Java
List<Object> containsAllNumbers = new ArrayList<>();
        containsAllNumbers.add(1);
        containsAllNumbers.add(2);
        containsAllNumbers.add(3);
        containsAllNumbers.add(900);
Query query = new Query(“mycollection”).containsAll("array1", containsAllNumbers);
//query.findDocuments(…);
```
---------------------------------------------------------------------------------------------

<a name="Query+notContainedIn"></a>
### .notContainedIn(field, values)
Метод для добавления условия выборки: только документы у которых

* значение поля не совпадает ни с одним из значений заданного массива.
* значение поля не задано (not exist).


| Parameter | Type | Properties | Description | Value example |
|-----------|------|------------|-------------|---------------|
| field | `String` |                                            Mandatory | Имя поля, которому задается условие | "orderNumbers"   |
| value | `List<тип поля>` | Mandatory |  Массив элементов                  |  см.пример ниже |


**Example** 

```Java
List<Object> notContainsInList = new ArrayList<>();
        notContainsInList.add(1);
        notContainsInList.add(111);
        notContainsInList.add(11);
        notContainsInList.add(50);
Query query = new Query(“mycollection”).notContainedIn("orderNumbers", notContainsInList)
//query.findDocuments(…);

```
---------------------------------------------------------------------------------------------

<a name="Query+greaterThan"></a>
### .greaterThan(field, value)
Метод для добавления условия выборки: только документы у которых значение поля больше указанного в value значения

| Parameter | Type | Properties | Description | Value example |
|-----------|------|------------|-------------|---------------|
| field | `String` |                                            Mandatory | Имя поля, которому задается условие | "fieldname"   |
| value | `Integer / Double / Date` | Mandatory |  Значение для сравнения                  |  22 |

**Example** 

```Java
Query query = new Query(“mycollection”).greaterThan("number", 22)
//query.findDocuments(…);
```
---------------------------------------------------------------------------------------------

<a name="Query+greaterThenOrEqualTo"></a>
### .greaterThenOrEqualTo(field, value)
Метод для добавления условия выборки: только документы у которых значение поля больше или равно указанного в value значения

| Parameter | Type | Properties | Description | Value example |
|-----------|------|------------|-------------|---------------|
| field | `String` |                                            Mandatory | Имя поля, которому задается условие | "fieldname"   |
| value | `Integer / Double / Date` | Mandatory |  Значение для сравнения                  |  22 |

**Example** 

```Java
Query query = new Query(“mycollection”).greaterThenOrEqualTo ("number", 22)
//query.findDocuments(…);
```
---------------------------------------------------------------------------------------------

<a name="Query+lessThan"></a>
### .lessThan(field, value)
Метод для добавления условия выборки: только документы у которых значение поля меньше указанного в value значения

| Parameter | Type | Properties | Description | Value example |
|-----------|------|------------|-------------|---------------|
| field | `String` |                                            Mandatory | Имя поля, которому задается условие | "fieldname"   |
| value | `Integer / Double / Date` | Mandatory |  Значение для сравнения                  |  22 |

**Example** 

```Java
Query query = new Query(“mycollection”). lessThan("number", 22)
//query.findDocuments(…);

```
---------------------------------------------------------------------------------------------

<a name="Query+lessThanOrEqualTo"></a>
### .lessThanOrEqualTo(field, value)
Метод для добавления условия выборки: только документы у которых значение поля меньше или равно указанного в value значения

| Parameter | Type | Properties | Description | Value example |
|-----------|------|------------|-------------|---------------|
| field | `String` |                                            Mandatory | Имя поля, которому задается условие | "fieldname"   |
| value | `Integer / Double / Date` | Mandatory |  Значение для сравнения                  |  22 |

**Example** 

```Java
Query query = new Query(“mycollection”).lessThanOrEqualTo ("number", 22)
//query.findDocuments(…);
```
---------------------------------------------------------------------------------------------

<a name="Query+exists"></a>
### .exists(field)
Метод для добавления условия выборки: только документы у которых задано значение поля, т.е поле существует и не является пустым.

| Parameter | Type | Properties | Description | Value example |
|-----------|------|------------|-------------|---------------|
| field | `String` |                                            Mandatory | Имя поля, которому задается условие | "fieldname"   |

**Example** 

```Java
Query query = new Query(“mycollection”).exists("phoneNumber")
//query.findDocuments(…);
```
---------------------------------------------------------------------------------------------

<a name="Query+doesNotExist"></a>
### .doesNotExist(field)
Метод для добавления условия выборки: только документы у которых не задано значение поля, т.е поле не существует или является пустым.

| Parameter | Type | Properties | Description | Value example |
|-----------|------|------------|-------------|---------------|
| field | `String` |                                            Mandatory | Имя поля, которому задается условие | "fieldname"   |

**Example** 

```Java
Query query = new Query(“mycollection”).doesNotExist("phoneNumber")
//query.findDocuments(…);
```
---------------------------------------------------------------------------------------------

<a name="Query+contains"></a>
### .contains(field, regEx, options)
Метод для добавления условия выборки: только документы у которых значение поля соответствуют заданному регулярному выражению.


| Parameter | Type | Properties | Description | Value example |
|-----------|------|------------|-------------|---------------|
| field | `String` |                                            Mandatory | Имя поля, которому задается условие | "fieldname"   |
| regEx | `String` |                                            Mandatory | Регулярное выражение | “aB”   |
| options | `RegexOptions` |                                            НеMandatory | Опции регулярного выражения | см.пример ниже  |

**Example** 

```Java
 RegexOptions regexOptions = new RegexOptions();
 regexOptions.setRegexCaseInsenssitive();

 Query query = new Query(“mycollection”).contains("exampleField", "BC", regexOptions)
 //query.findDocuments(…);         
```
---------------------------------------------------------------------------------------------

<a name="Query+startsWith"></a>
### .startsWith(field, regEx, options)
Метод для добавления условия выборки: только документы у которых значение поля начинается с заданной строки

| Parameter | Type | Properties | Description | Value example |
|-----------|------|------------|-------------|---------------|
| field | `String` |                                            Mandatory | Имя поля, которому задается условие | "fieldname"   |
| regEx | `String` |                                            Mandatory | Строка с которой должно начинаться значение поля | “aB”   |
| options | `RegexOptions` |                                            НеMandatory | Опции регулярного выражения | см.пример ниже  |

!!! note "Примечание"
    Параметр options позволяет, к примеру, искать все документы начинающиеся с “aB” не зависимо от регистра, т.е искать документы, начинающиеся на ab, aB, Ab, AB.

**Example** 

```Java
 RegexOptions regexOptions = new RegexOptions();
 regexOptions.setRegexCaseInsenssitive();

 Query query = new Query(“mycollection”).startsWith ("exampleField", "a", regexOptions)
 //query.findDocuments(…);

```
---------------------------------------------------------------------------------------------

<a name="Query+endsWith"></a>
### .endsWith(field, regEx, options)
Метод для добавления условия выборки: только документы у которых значение поля заканчивается с заданной строки


| Parameter | Type | Properties | Description | Value example |
|-----------|------|------------|-------------|---------------|
| field | `String` |  Mandatory | Имя поля, которому задается условие | "fieldname"   |
| regEx | `String` |  Mandatory | Строка, на которую должно заканчиваться значение поля | “aaB”   |
| options | `RegexOptions` |                                            НеMandatory | Опции регулярного выражения | см.пример ниже  |

!!! note "Примечание"
    Параметр options позволяет, к примеру, искать все документы начинающиеся с “aB” не зависимо от регистра, т.е искать документы, начинающиеся на ab, aB, Ab, AB.

**Example** 

```Java
 RegexOptions regexOptions = new RegexOptions();
 regexOptions.setRegexCaseInsenssitive();

 Query query = new Query(“mycollection”).startsWith ("exampleField", "a", regexOptions)
 //query.findDocuments(…);
```
---------------------------------------------------------------------------------------------

<a name="Query+and"></a>
### .and(field, query)
Метод логического умножения нескольких условий выборки по одному полю

| Parameter | Type | Properties | Description | Value example |
|-----------|------|------------|-------------|---------------|
| field | `String` | Mandatory | Имя поля, которому задается условие | "fieldname"   |
| query | `Query` | Mandatory |  Объект класса Query, содержащий данные для выборки                   |  см.пример ниже |

**Example** 

```Java
Query query1 = new Query(COLLECTION_NAME).greaterThan("raiting", 50);
Query query2 = new Query(COLLECTION_NAME).lessThan("raiting", 100);

query1.and("number3", query2);
//query1.findDocuments(…);
```
---------------------------------------------------------------------------------------------

<a name="Query+or"></a>
### .or(field, query)
Метод логического сложения нескольких условий выборки по одному полю

| Parameter | Type | Properties | Description | Value example |
|-----------|------|------------|-------------|---------------|
| field | `String` | Mandatory | Имя поля, которому задается условие | "fieldname"   |
| query | `Query` | Mandatory |  Объект класса Query, содержащий данные для выборки                   |  см.пример ниже |

**Example** 

```Java
Query query1 = new Query(“mycollection”).greaterThan("raiting", 50);
Query query2 = new Query(“mycollection”).equalTo("status", 0);

query1.or("number3", query2);
//query1.findDocuments(…);
```
---------------------------------------------------------------------------------------------

<a name="Query+raw"></a>
### .raw(json)
Метод для задания условий выборки в формате json.

| Parameter | Type | Properties | Description | Value example |
|-----------|------|------------|-------------|---------------|
| json | `String` |  Mandatory | Условия выборки в формате json | "{\"_id\": {\"$eq\": \"W9vrMS9SuW\"}}"  |

**Example** 

```Java
  Query query = new Query(“mycollection”);
  query.raw("{\"_id\": {\"$eq\": \"W9vrMS9SuW\"}}");
  //query.findDocuments(…)
```
---------------------------------------------------------------------------------------------

<a name="Query+reset"></a>
### .reset()
Метод для очистки условия выборки.

**Example** 

```Java
Query query = new Query(“mycollection”);
query.equalTo(“_id”, “dsads123sd”);
query.reset();
query.equalTo(“_id”, “ds54522sd”);
//query.findDocuments(…)
```
---------------------------------------------------------------------------------------------

<a name="Query+ascending"></a>
### .ascending(field)
Метод для сортировки данных указанного поля в порядке возрастания перед совершением выборки.

| Parameter | Type | Properties | Description | Value example |
|-----------|------|------------|-------------|---------------|
| field | `String` | Mandatory | Имя поля, которому задается условие | "fieldname"   |

**Example** 

```Java
Query query = new Query("ordersCollection");
query.ascending("itemId");
//query.findDocuments(...)
```
---------------------------------------------------------------------------------------------

<a name="Query+descending"></a>
### .descending(field)
Метод для сортировки данных указанного поля в порядке убывания перед совершением выборки.

| Parameter | Type | Properties | Description | Value example |
|-----------|------|------------|-------------|---------------|
| field | `String` | Mandatory | Имя поля, которому задается условие | "fieldname"   |

**Example** 

```Java
Query query = new Query("ordersCollection");
query.descending("itemId");
//query.findDocuments(...)
```
---------------------------------------------------------------------------------------------

<a name="Query+setFieldsForSearch"></a>
### .setFieldsForSearch(fields)
Метод для задания списка возвращаемых полей.

| Parameter | Type | Properties | Description | Value example |
|-----------|------|------------|-------------|---------------|
| fields | `List<String>` | Mandatory | Имена полей | см.пример ниже   |

**Example** 

```Java
List<Strings> fieldNames = new ArrayList<>();
fieldNames.add(“orderId”);
fieldNames.add(“buyerName”);
fieldNames.add(“phoneNumber”);

Query query = new Query(“mycollection”).setFieldsForSearch(fieldNames);
//query.findDocuments(…);
```
---------------------------------------------------------------------------------------------

<a name="Query+getQueryInfo"></a>
### .getQueryInfo()
Метод для получения информации о условиях выборки

**Example** 

```Java
Query query = new Query(“mycollection”);
query.equalTo(“_id”, “dsads123sd”);

QueryInfo queryInfo = query.getQueryInfo();
```