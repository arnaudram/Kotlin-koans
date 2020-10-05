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
