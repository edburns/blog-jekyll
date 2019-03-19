---
layout: post
title:  "Raw Notes: Revisiting Effective Java in 2019"
date:   2019-03-19 18:45 +0100
comments: true
---

* Credentials

* [Plugged his book](http://developers.redhat.com/promotions/migrating-to-microservice-databases)

* [Has a three hour version of the talk](https://www.youtube.com/watch?v=ANZXvXVa1Lg).

* Lambdas

* Streams

* Optionals

* Default methods in interfaces

* try with resources

* factory methods

* Minimize mutability

* Comparable(14)

* Live coding

   * Minimize mutability.  Value object class.  Why?  Easier to reason
     about.  Reduces the chance that the system gets into an
     inconsistent state.
     
      * Make as many of things `final` as possible
      
      * No ctor, use factory method: `of`.  Easier for refactoring.
        Allows you to change the cardinality, maybe re-use objects with
        an object pool.  Flyweight class.  `BigDecimal.valueOf()`
        implementation is an example.
      
      * COMMENT: what about making the zero arg ctor private?
      
      * He used Google Guava, but noted that they have violated backward
        compatibility in the past.
        
      * Advised to implement `equals` and `hashCode`. Use the `Objects`
        class in the JDK.  I thas an `equals` method.  Handles all that
        `null` checking.  But beware of the performance penalty of
        autoboxing.  For hashcode, there is `Objects.hash`.  This is
        even worse for performance.  If you use the generator for
        `equals` and `hashcode` make sure to keep the impls up to date
        as the class evolved.
      
         * for hashcode, because it is immutable, you can do it in the
           ctor. If you use an object as a key to a hashmap, it must be
           immutable.  If you can't do that, you can make a
           `toHashKey()` method.
           
       * `toString()`.  It's for developers.  Guava
         `MoreObjects.toStringHelper()`.  Not for "business strings".
         For busness strings, use `formatTo` with `Formatter` and
         `Formattable`.
         
       * `Comparable`.  `Comparator` interface.
       
   * Functional Interfaces.  `@FunctionalInterface`.  Constrains your
     code to conform to functional interfaces.  Favor *Strategy* over
     *Template Method*.
     
      * You should not need to create a new functional interface.  There
        are 41 already in the JDK.  It's likely it will be in there
        already.  Learn these six well: `UnaryOperator, BinaryOperator,
        Predicate, Function, Supplier, Consumer`.
        
   * Method References.  Five different types
   
      * static, ie `Integer::parseInt()`
      
      * bounded and unbounded method reference.

   * Enums
   
      * Can use lambdas or function references in enum initializer.
