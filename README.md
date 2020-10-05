# Kotlin-koans
Solution proposal for  Kotlin koans
## Introduction
* [Simple Functions](#simple-functions) 
* [Named arguments](#named-arguments) 
* [Default arguments](#default-arguments)  
* [Lambdas](#lambdas)
* [Strings](#strings)
* [Data Classes](#data-classes)
* [Nullable types](#nullable-types)
* [Smart casts](#smart-casts)
* [Extension functions](#extension-functions)
* [Object expressions](#object-expressions)
* [SAM conversions](#sam-conversions)
* [Extension functions on collections](#extension-functions-on-collections)
## Conventions
* [Comparison](#comparison)
* [In range](#in-range)
* [Range to](#range-to)
* [For loop](#for-loop)
* [Operators overloading](#operators-overloading)
* [Destructuring declarations](#destructuring-declarations)
* [Invoke](#invoke)
## Collections

* [Introduction](#introduction)
* [ Filter; map](#filter-map)
* [All, Any and other predicates](#all-any-and-other-predicates)
* [FlatMap](#flatmap)
* [Max; min](#max-min)
* [Sort](#sort)
* [Sum](#sum)
* [Group By](#group-by)
* [Partition](#partition)
* [Fold](#fold)
* [Compound tasks](#compound-tasks)
* [Get used to new style](#get-used-to-new-style)
## Properties
* [Properties](#properties)
* [Lazy property](#lazy-property)
* [Delegates example](#delegates-example)
* [Delegates](#delegates)
## Builders
* [Extension function literals](#extension-function-literals)
* [String and map builders](#string-and-map-builders)
* [The function apply](#the-function-apply)
* [Builders: how it works](#builders-how-it-works)
## Generics
* [Generic functions](#generic-functions)









## Simple Functions
Take a look at function syntax and make the function start return the string "OK".
In the tasks the function TODO() is used that throws an exception. Your job during the koans will be to replace this function invocation with a meaningful code according to the problem.
 - #### Solution
```Kotlin
fun start(): String = "OK"
``` 
## Named arguments

Default and named arguments help to minimize the number of overloads and improve the readability of the function invocation. The library function joinToString is declared with default values for parameters:
```Kotlin
fun joinToString(
    separator: String = ", ",
    prefix: String = "",
    postfix: String = "",
    /* ... */
): String
```
It can be called on a collection of Strings. Specifying only two arguments make the function joinOptions() return the list in a JSON format 
```Kotlin 
(e.g., "[a, b, c]")
```
- ### Solution
```Kotlin
fun joinOptions(options: Collection<String>) = 
    options.joinToString(prefix="[",postfix="]")
```
## Default arguments
There are several overloads of 'foo()' in Java:
```Java
public String foo(String name, int number, boolean toUpperCase) {
    return (toUpperCase ? name.toUpperCase() : name) + number;
}
public String foo(String name, int number) {
    return foo(name, number, false);
}
public String foo(String name, boolean toUpperCase) {
    return foo(name, 42, toUpperCase);
}
public String foo(String name) {
    return foo(name, 42);
}
```
All these Java overloads can be replaced with one function in Kotlin. Change the declaration of the function foo in a way that makes the code using foo compile.
Use default and named arguments.    
- #### Solution
```Kotlin
fun foo(name: String, number: Int=42, toUpperCase: Boolean=false) =
        (if (toUpperCase) name.toUpperCase() else name) + number

fun useFoo() = listOf(
        foo("a"),
        foo("b", number = 1),
        foo("c", toUpperCase = true),
        foo(name = "d", number = 2, toUpperCase = true)
)
```
## Lambdas
Kotlin supports a functional style of programming. Read about higher-order functions and function literals (lambdas) in Kotlin.

Pass a lambda to any function to check if the collection contains an even number. The function any gets a predicate as an argument and returns true if there is at least one element satisfying the predicate.
- #### Solution
```Kotlin
fun containsEven(collection: Collection<Int>): Boolean = 
    collection.any { it-> it % 2 == 0
           
    }
```
## Strings
Read about different string literals and string templates in Kotlin.

Raw strings are useful for writing regex patterns, you don't need to escape a backslash by a backslash. Below there is a pattern that matches a date in format 13.06.1992 (two digits, a dot, two digits, a dot, four digits):

`fun getPattern() = """\d{2}\.\d{2}\.\d{4}"""`
Using month variable rewrite this pattern in such a way that it matches the date in format 13 JUN 1992 (two digits, a whitespace, a month abbreviation, a whitespace, four digits).
- #### Solution
```Kotlin
val month = "(JAN|FEB|MAR|APR|MAY|JUN|JUL|AUG|SEP|OCT|NOV|DEC)"

fun getPattern(): String = """\d{2} ${month} \d{4}"""
```
## Data classes
Rewrite the following Java code to Kotlin:
```Java
public class Person {
    private final String name;
    private final int age;
​
    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }
​
    public String getName() {
        return name;
    }
​
    public int getAge() {
        return age;
    }
}
```
Then add a modifier data to the resulting class. This annotation means the compiler will generate a bunch of useful methods in this class: equals/hashCode, toString and some others. The getPeople function should start to compile.

Read about classes, properties and data classes.
- #### Solution
```Kotlin
data class Person(val name:String,val age :Int)

fun getPeople(): List<Person> {
    return listOf(Person("Alice", 29), Person("Bob", 31))
}
```
## Nullable types
Read about null safety and safe calls in Kotlin and rewrite the following Java code using only one if expression:
```Java
public void sendMessageToClient(
    @Nullable Client client,
    @Nullable String message,
    @NotNull Mailer mailer
) {
    if (client == null || message == null) return;
​
    PersonalInfo personalInfo = client.getPersonalInfo();
    if (personalInfo == null) return;
​
    String email = personalInfo.getEmail();
    if (email == null) return;
​
    mailer.sendMessage(email, message);
}
```
- ### Solution
```Kotlin
fun sendMessageToClient(
        client: Client?, message: String?, mailer: Mailer
){
   if (client == null || message == null){
        return
   }
    val personalInfo = client.personalInfo
    val email=personalInfo?.email
    email?.let{it->
        mailer.sendMessage(it,message)
    }
}

class Client (val personalInfo: PersonalInfo?)
class PersonalInfo (val email: String?)
interface Mailer {
    fun sendMessage(email: String, message: String)
}
```
## Smart casts
Rewrite the following Java code using smart casts and when expression:
```Java
public int eval(Expr expr) {
    if (expr instanceof Num) {
        return ((Num) expr).getValue();
    }
    if (expr instanceof Sum) {
        Sum sum = (Sum) expr;
        return eval(sum.getLeft()) + eval(sum.getRight());
    }
    throw new IllegalArgumentException("Unknown expression");
}
```
- ### Solution
```Kotlin
fun eval(expr: Expr): Int =
        when (expr) {
            is Num -> expr.value
            is Sum -> eval(expr.left)+eval( expr.right)
            else -> throw IllegalArgumentException("Unknown expression")
        }

interface Expr
class Num(val value: Int) : Expr
class Sum(val left: Expr, val right: Expr) : Expr
```
## Extension functions
Read about extension functions. Then implement extension functions Int.r() and Pair.r() and make them convert Int and Pair to RationalNumber.
- ### Solution
```Kotlin
fun Int.r(): RationalNumber = RationalNumber(this.and(this),1)
fun Pair<Int, Int>.r(): RationalNumber = RationalNumber(this.first,this.component2())
    


data class RationalNumber(val numerator: Int, val denominator: Int)
```
## Object expressions
Read about object expressions that play the same role in Kotlin as anonymous classes in Java.

Add an object expression that provides a comparator to sort a list in a descending order using java.util.Collections class. In Kotlin you use Kotlin library extensions instead of java.util.Collections, but this example is still a good demonstration of mixing Kotlin and Java code.
- ### Solution
```Kotlin
import java.util.*

fun getList(): List<Int> {
    val arrayList = arrayListOf(1, 5, 2)
    Collections.sort(arrayList,{a:Int,b:Int->b-a}
        
    )
    return arrayList
}
```
## SAM conversions
When an object implements a SAM interface (one with a Single Abstract Method), you can pass a lambda instead. Read more about SAM-conversions.

In the previous example change an object expression to a lambda.
- ### Solution
```Kotlin
import java.util.*

fun getList(): List<Int> {
    val arrayList = arrayListOf(1, 5, 2)
    Collections.sort(arrayList, { x, y -> y-x })
    return arrayList
}
```
## Extension functions on collections
Kotlin code can be easily mixed with Java code. Thus in Kotlin we don't introduce our own collections, but use standard Java ones (slightly improved). Read about read-only and mutable views on Java collections.

In Kotlin standard library there are lots of extension functions that make the work with collections more convenient. Rewrite the previous example once more using an extension function sortedDescending.
- ### Solution
```Kotlin
fun getList(): List<Int> {
    return arrayListOf(1, 5, 2).sortedDescending()
}
```
## Comparison
Read about operator overloading to learn how different conventions for operations `like ==, <, +` work in Kotlin. Add the function compareTo to the class MyDate to make it comparable. After that the code below `date1 < date2` will start to compile.

In Kotlin when you override a member, the modifier override is mandatory.
- ### Solution
```Kotlin
data class MyDate(val year: Int, val month: Int, val dayOfMonth: Int): Comparable<MyDate> {
//fun operator minus(other:MyDate):
    override operator fun compareTo( other:MyDate):Int{
      if(this.year<other.year){
          return -1
      } else if(this.year>other.year){
          return 1
      }
      if(this.month<other.month){
          return -1
      } else if(this.month>other.month){
          return 1
      }
      if(this.dayOfMonth<other.dayOfMonth){
          return -1
      } else if(this.dayOfMonth>other.dayOfMonth){
          return 1
      } else
        return 0
    }
}

fun compare(date1: MyDate, date2: MyDate) = date1 < date2
```
## In range
In Kotlin in checks are translated to the corresponding contains calls:
```Kotlin
val list = listOf("a", "b")
"a" in list  // list.contains("a")
"a" !in list // !list.contains("a")
```
Read about ranges. Add a method fun contains(d: MyDate) to the class DateRange to allow in checks with a range of dates.
- ### Solution
```Kotlin
class DateRange(val start: MyDate, val endInclusive: MyDate){
    fun contains(d: MyDate):Boolean{
        return this.start<d && d<=this.endInclusive
    }
}


fun checkInRange(date: MyDate, first: MyDate, last: MyDate): Boolean {
    return  DateRange(first, last).contains(date)
}

```
## Range to
Implement the function MyDate.rangeTo(). This allows you to create a range of dates using the following syntax:

`MyDate(2015, 5, 11)..MyDate(2015, 5, 12)`
Note that now the class DateRange implements the standard ClosedRange interface and inherits contains method implementation.
- ### Solution
```Kotlin
operator fun MyDate.rangeTo(other: MyDate) = DateRange(this,other)

class DateRange(override val start: MyDate, override val endInclusive: MyDate): ClosedRange<MyDate>

fun checkInRange(date: MyDate, first: MyDate, last: MyDate): Boolean {
    return date in first..last
}
```
## For loop
Kotlin for loop iterates through anything that provides an iterator. Make the` class DateRange implement Iterable<MyDate>`, so that it could be iterated over. You can use the function` MyDate.nextDay()` defined in DateUtil.kt
- ### Solution
```Kotlin
  class DateRange(val start: MyDate, val end: MyDate):Iterable<MyDate>{
  override operator fun iterator(): Iterator<MyDate>{
     // if(this.start>=this.end){
     //     throw IllegalArgumentException("empty range")
      //}
      val list=arrayListOf<MyDate>()
      var day=this.start
     
      do{
          list.add(day)
         day= day.nextDay()
      }while(day<=this.end)
      return list.iterator()
          
      
  }
 
}

fun iterateOverDateRange(firstDate: MyDate, secondDate: MyDate, handler: (MyDate) -> Unit) {
    for (date in firstDate..secondDate) {
          if(firstDate>=secondDate){
              break
          }
        handler(date)
    }
}
```  
## Operators overloading
Implement a kind of date arithmetic. Support adding years, weeks and days to a date. You could be able to write the code like this:` date + YEAR * 2 + WEEK * 3 + DAY * 15.`

At first, add an extension function `'plus()'` to MyDate, taking a TimeInterval as an argument. Use an utility function MyDate.addTimeIntervals() declared in DateUtil.kt

Then, try to support adding several time intervals to a date. You may need an extra class.
- ### Solution
```Kotlin
import TimeInterval.*

data class MyDate(val year: Int, val month: Int, val dayOfMonth: Int)

enum class TimeInterval { DAY, WEEK, YEAR }
fun addTimeIntervals(myDate: MyDate, year:Int = 0, week: Int = 0, day:Int = 0) : MyDate {
    return myDate
            .addTimeIntervals(TimeInterval.YEAR,year)
            .addTimeIntervals(TimeInterval.WEEK,week)
            .addTimeIntervals(TimeInterval.DAY,day)
}
```
## Destructuring declarations
Read about destructuring declarations and make the following code compile by adding one word.
- ### Solution
```Kotlin
data class MyDate(val year: Int, val month: Int, val dayOfMonth: Int)

fun isLeapDay(date: MyDate): Boolean {

    val (year, month, dayOfMonth) = date

    // 29 February of a leap year
    return year % 4 == 0 && month == 2 && dayOfMonth == 29
}
```
## Invoke
Objects with invoke() method can be invoked as a function.

You can add invoke extension for any class, but it's better not to overuse it:
```
fun Int.invoke() { println(this) }
​
1() //huh?..
```
Implement the function Invokable.invoke() so it would count a number of invocations.

- ### Solution
```Kotlin
class Invokable {
    var numberOfInvocations: Int = 0
        private set
    operator fun invoke(): Invokable {
        this.numberOfInvocations =numberOfInvocations +1
        return this
    }
}

fun invokeTwice(invokable: Invokable) = invokable()()
```
## Introduction
This part was inspired by GS Collections Kata.

Default collections in Kotlin are Java collections, but there are lots of useful extension functions for them. For example, operations that transform a collection to another one, starting with `'to': toSet or toList.`

Implement an extension function Shop.getSetOfCustomers(). The class Shop and all related classes can be found at Shop.kt
- ### Solution
```Kotlin
fun Shop.getSetOfCustomers(): Set<Customer> = this.customers.toSet()
```
## Filter; map
Implement extension functions Shop.getCitiesCustomersAreFrom() and Shop.getCustomersFrom() using functions map and filter.
```Kotlin
val numbers = listOf(1, -1, 2)
numbers.filter { it > 0 } == listOf(1, 2)
numbers.map { it * it } == listOf(1, 1, 4)
```
- ### Solution
```Kotlin
// Return the set of cities the customers are from
fun Shop.getCitiesCustomersAreFrom(): Set<City> = this.customers.map{
    it.city
}.toSet()

// Return a list of the customers who live in the given city
fun Shop.getCustomersFrom(city: City): List<Customer> = this.customers.filter{
    it.city==city
}
```
## All, Any and other predicates
Implement all the functions below using all, any, count, find.

val numbers = listOf(-1, 0, 2)
val isZero: (Int) -> Boolean = { it == 0 }
numbers.any(isZero) == true
numbers.all(isZero) == false
numbers.count(isZero) == 1
numbers.find { it > 0 } == 2
- ### Solution
```Kotlin
// Return true if all customers are from the given city
fun Shop.checkAllCustomersAreFrom(city: City): Boolean = this.customers.all{it->
  it.city==city  
}

// Return true if there is at least one customer from the given city
fun Shop.hasCustomerFrom(city: City): Boolean = this.customers.any{it->
  it.city==city  
}

// Return the number of customers from the given city
fun Shop.countCustomersFrom(city: City): Int = this.customers.count{it->
 it.city==city   
}

// Return a customer who lives in the given city, or null if there is none
fun Shop.findAnyCustomerFrom(city: City): Customer? = this.customers.find{it->
   it.city==city 
}
```
## FlatMap
Implement Customer.getOrderedProducts() and Shop.getAllOrderedProducts() using flatMap.
```Kotlin
val result = listOf("abc", "12").flatMap { it.toCharList() }
result == listOf('a', 'b', 'c', '1', '2')
```
- ### Solution
```Kotlin
// Return all products this customer has ordered
val Customer.orderedProducts: Set<Product> get() {
    return this.orders.flatMap{it->
        it.products
    }.toSet()
}

// Return all products that were ordered by at least one customer
val Shop.allOrderedProducts: Set<Product> get() {
    return this.customers.flatMap{it-> it.orderedProducts
           }.toSet()
}
```
## Max; min
Implement Shop.getCustomerWithMaximumNumberOfOrders() and Customer.getMostExpensiveOrderedProduct() using max, min, maxBy, or minBy.
```Kotlin
listOf(1, 42, 4).max() == 42
listOf("a", "ab").minBy { it.length } == "a"
```
- ### Solution
```Kotlin
// Return a customer whose order count is the highest among all customers
fun Shop.getCustomerWithMaximumNumberOfOrders(): Customer? {
    return this.customers.maxByOrNull{it->
        it.orders.size
    }
}

// Return the most expensive product which has been ordered
fun Customer.getMostExpensiveOrderedProduct(): Product?{
    return this.orders.flatMap{it.products}.maxByOrNull{it.price}
}
```
## Sort
Implement Shop.getCustomersSortedByNumberOfOrders() using sorted or sortedBy.
```Kotlin
listOf("bbb", "a", "cc").sorted() == listOf("a", "bbb", "cc")
listOf("bbb", "a", "cc").sortedBy { it.length } == li
```
- ### Solution
```Kotlin
// Return a list of customers, sorted by the ascending number of orders they made
fun Shop.getCustomersSortedByNumberOfOrders(): List<Customer> 
    =  this.customers.sortedBy{it.orders.size}
```
## Sum
Implement Customer.getTotalOrderPrice() using sum or sumBy.
```Kotlin
listOf(1, 5, 3).sum() == 9
listOf("a", "b", "cc").sumBy { it.length() } == 4
If you want to sum the double values, use sumByDouble.
```
- ### Solution
```Kotlin
// Return the sum of prices of all products that a customer has ordered.
// Note: the customer may order the same product for several times.
fun Customer.getTotalOrderPrice(): Double
= this.orders.flatMap{it.products}.sumByDouble{it.price}
```
## Group By
Implement Shop.groupCustomersByCity() using groupBy.
```Kotlin
val result = listOf("a", "b", "ba", "ccc", "ad").groupBy { it.length() }
result == mapOf(1 to listOf("a", "b"), 2 to listOf("
```
- ### Solution
```Kotlin
// Return a map of the customers living in each city
fun Shop.groupCustomersByCity(): Map<City, List<Customer>> 
    { val customers=this.customers
        return customers.groupBy{it.city}
    }
```
## Partition
Implement Shop.getCustomersWithMoreUndeliveredOrdersThanDelivered() using partition.
```Kotlin
val numbers = listOf(1, 3, -4, 2, -11)
val (positive, negative) = numbers.partition { it > 0 }
positive == listOf(1, 3, 2)
negative == listOf(-4, -11)
Note that destructuring declaration syntax is used in this example.
```
- ### Solution
```Kotlin
// Return customers who have more undelivered orders than delivered
fun Shop.getCustomersWithMoreUndeliveredOrdersThanDelivered(): Set<Customer>
    {
      return this.customers.filter{
          val(delivered,undelivered)=it.orders.partition{it.isDelivered}
          delivered.size<undelivered.size
      }.toSet()
    }
```
## Fold
Implement Shop.getSetOfProductsOrderedByEveryCustomer() using fold.
```Kotlin
listOf(1, 2, 3, 4).fold(1, {
    partProduct, element ->
    element * partProduct
}) == 24
```
- ### Solution
```Kotlin
// Return the set of products that were ordered by every customer
fun Shop.getSetOfProductsOrderedByEveryCustomer(): Set<Product> {
    val orders=this.customers.flatMap{it.orders}
    val pducts=orders.flatMap{it.products}.toSet()
   
 return this.customers.fold(pducts){ acc,customer->
 customer.orders.flatMap{it.products}.toSet().intersect(acc)
 }
        
}
```
## Compound tasks
Implement Customer.getMostExpensiveDeliveredProduct() and Shop.getNumberOfTimesProductWasOrdered() using functions from the Kotlin standard library.
- ### Solution
```Kotlin
// Return the most expensive product among all delivered products
// (use the Order.isDelivered flag)
fun Customer.getMostExpensiveDeliveredProduct(): Product? {
    val deliveredOrders=this.orders.filter{it.isDelivered}
     val deliveredProducts=deliveredOrders.flatMap{it.products}
     return deliveredProducts.maxByOrNull{it.price}
}

// Return how many times the given product was ordered.
// Note: a customer may order the same product for several times.
fun Shop.getNumberOfTimesProductWasOrdered(product: Product): Int {
    val orderedProducts=this.customers.flatMap{it.orders}
    return orderedProducts.flatMap{it.products}.count{it==product}
}
```
## Get used to new style
Rewrite the following Java function to Kotlin.
```Java
public Collection<String> doSomethingStrangeWithCollection(
        Collection<String> collection
) {
    Map<Integer, List<String>> groupsByLength = Maps.newHashMap();
    for (String s : collection) {
        List<String> strings = groupsByLength.get(s.length());
        if (strings == null) {
            strings = Lists.newArrayList();
            groupsByLength.put(s.length(), strings);
        }
        strings.add(s);
    }
​
    int maximumSizeOfGroup = 0;
    for (List<String> group : groupsByLength.values()) {
        if (group.size() > maximumSizeOfGroup) {
            maximumSizeOfGroup = group.size();
        }
    }
​
    for (List<String> group : groupsByLength.values()) {
        if (group.size() == maximumSizeOfGroup) {
            return group;
        }
    }
    return null;
}
```
 - ### Solution
 ```Kotlin
 fun doSomethingStrangeWithCollection(collection: Collection<String>): Collection<String>? {

    val groupsByLength = collection. groupBy { s -> s.length }

    val maximumSizeOfGroup = groupsByLength.values.map { group -> group.size }.max()

    return groupsByLength.values.firstOrNull { group -> maximumSizeOfGroup==group.size }
}
 ```
## Properties
Read about properties in Kotlin.

Add a custom setter to PropertyExample.propertyWithCounter so that the counter property is incremented every time propertyWithCounter is assigned to.
- ### Solution
```Kotlin
class PropertyExample() {
    var counter = 0
          set
    var propertyWithCounter: Int? = null
         set(value){
             field=value
             counter++
         }
}
```
## Lazy property
Add a custom getter to make the `'lazy'` val really lazy. It should be initialized by the invocation of `'initializer()'` at the moment of the first access.

You can add as many additional properties as you need.

Do not use delegated properties!
- ### Solution
```Kotlin
class LazyProperty(val initializer: () -> Int) {
     var laz:Int?=null
             get(){
                 if(field==null){
                    field=initializer()
                 }
                 return field
             }
    
      
    val lazy: Int
        get() {
           return laz!!
        }
}
```
## Delegates example
Read about delegated properties and make the property lazy by using delegates.
- ### Solution
```Kotlin
class LazyProperty(val initializer: () -> Int) {
    val lazyValue: Int by lazy{initializer()}
}
```
## Delegates
You may declare your own delegates. Implement the methods of the class `'EffectiveDate'` so it can be delegated to. Store only the time in milliseconds in `'timeInMillis'` property.

Use the extension functions `MyDate.toMillis()` and `Long.toDate()`, defined at `MyDate.kt`
- ### Solution
```Kotlin
import kotlin.properties.ReadWriteProperty
import kotlin.reflect.KProperty

class D {
    var date: MyDate by EffectiveDate()
}

class EffectiveDate<R> : ReadWriteProperty<R, MyDate> {

    var timeInMillis: Long? = null

    override fun getValue(thisRef: R, property: KProperty<*>): MyDate {
        return timeInMillis!!.toDate()
    }

    override fun setValue(thisRef: R, property: KProperty<*>, value: MyDate) {
        if(timeInMillis==null){
            timeInMillis=value.toMillis()
        }
    }
}
```
## Extension function literals
Read about function literals with receiver.

You can declare isEven and isOdd as values, that can be called as extension functions. Complete the declarations below.
- ### Solution
```Kotlin
fun task(): List<Boolean> {
    val isEven: Int.() -> Boolean = {this%2==0}
    val isOdd: Int.() -> Boolean = { this%2==1 }

    return listOf(42.isOdd(), 239.isOdd(), 294823098.isEven())
}
```
## String and map builders
Extension function literals are very useful for creating builders, e.g.:
```Kotlin
fun buildString(build: StringBuilder.() -> Unit): String {
    val stringBuilder = StringBuilder()
    stringBuilder.build()
    return stringBuilder.toString()
}
​
val s = buildString {
    this.append("Numbers: ")
    for (i in 1..3) {
        // 'this' can be omitted
        append(i)
    }
}
​
s == "Numbers: 123"
```
Add and implement the function `'buildMutableMap'` with one parameter (of type extension function) creating a new HashMap, building it and returning it as a result. The usage of this function is shown below.
- ### Solution
```Kotlin
import java.util.HashMap

fun buildMutableMap(hash:HashMap<Int, String>.()-> Unit):Map<Int, String>{
    val hashmap=HashMap<Int, String>().apply{
        hash()
    }
    return hashmap
}

fun usage(): Map<Int, String> {
    return buildMutableMap {
        put(0, "0")
        for (i in 1..10) {
            put(i, "$i")
        }
    }
}
```
## The function apply
The previous examples can be rewritten using the library function apply (see examples below). Write your own implementation of this function named `'myApply'.`
- ### Solution
```Kotlin
fun <T> T.myApply(f: T.() -> Unit): T { 
        
          
    return this.apply{f()}
}

fun createString(): String {
    return StringBuilder().myApply {
        append("Numbers: ")
        for (i in 1..10) {
            append(i)
        }
    }.toString()
}

fun createMap(): Map<Int, String> {
    return hashMapOf<Int, String>().myApply {
        put(0, "0")
        for (i in 1..10) {
            put(i, "$i")
        }
    }
}
```
## Builders: how it works
Look at the questions below and give your answers

1. In the Kotlin code
```javaScript
tr {
    td {
        text("Product")
    }
    td {
        text("Popularity")
    }
}
```
'td' is:

a. special built-in syntactic construct

b. function declaration

c. function invocation

2. In the Kotlin code
```javaScript
tr (color = "yellow") {
    td {
        text("Product")
    }
    td {
        text("Popularity")
    }
}
```
'color' is:

a. new variable declaration

b. argument name

c. argument value

3. The block
```javaScript
{
    text("Product")
}
```
from the previous question is:

a. block inside built-in syntax construction td

b. function literal (or "lambda")

c. something mysterious

4. For the code
```javaScript
tr (color = "yellow") {
    this.td {
        text("Product")
    }
    td {
        text("Popularity")
    }
}
```
which of the following is true:

a. this code doesn't compile

b. this refers to an instance of an outer class

c. this refers to a receiver parameter TR of the function literal:
```javaScript
tr (color = "yellow") {
    this@tr.td {
        text("Product")
    }
}
```
- ### Solution
```Kotlin
import Answer.*

enum class Answer { a, b, c }

val answers = mapOf<Int, Answer?>(
        1 to c, 2 to b, 3 to b, 4 to c
)

```
## Generic functions
Make the following code compile by implementing a partitionTo function that splits a collection into two collections according to the predicate.

There is a partition() function in the standard library that always returns two newly created lists. You should write a function that splits the collection into two collections given as arguments. The signature of the toCollection() function from the standard library may help you.
- ### Solution
```Kotlin
import java.util.*

fun <T,H: MutableCollection<T>> Collection<T>.partitionTo(listHead : H, listBody: H, predicate : (T) -> Boolean ) : Pair<H,H> {
    this.forEach{      
        val isPredicate = predicate.invoke(it)
            if(isPredicate) listHead.add(it)
            else listBody.add(it)
    }
     return Pair(listHead,listBody)
}

fun partitionWordsAndLines() {
    val (words, lines) = listOf("a", "a b", "c", "d e").
            partitionTo(ArrayList<String>(), ArrayList()) { s -> !s.contains(" ") }
    words == listOf("a", "c")
    lines == listOf("a b", "d e")
}

fun partitionLettersAndOtherSymbols() {
    val (letters, other) = setOf('a', '%', 'r', '}').
            partitionTo(HashSet<Char>(), HashSet()) { c -> c in 'a'..'z' || c in 'A'..'Z'}
    letters == setOf('a', 'r')
    other == setOf('%', '}')
}
```
