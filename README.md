# ScalaX [![Build Status](https://travis-ci.org/t3hnar/scalax.svg)](https://travis-ci.org/t3hnar/scalax) [![Coverage Status](https://coveralls.io/repos/t3hnar/scalax/badge.svg)](https://coveralls.io/r/t3hnar/scalax)

Extension for [Scala](http://www.scala-lang.org) library

## Examples

### StringOption

```scala
import com.github.t3hnar.scalax.StringOption

StringOption(null) // None
StringOption("") // None
StringOption("\t\n\r ") // None
StringOption("nonempty") // Some("nonempty")
StringOption("\t\nnonempty\r ") // Some("nonempty")
```

### RichString

```scala
import com.github.t3hnar.scalax.RichString

"123".toByteOpt // Some(123)
"1-2-3".toByteOpt // None

"123".toIntOpt // Some(123)
"1-2-3".toIntOpt // None

"123".toLongOpt // Some(123)
"1-2-3".toLongOpt // None

"123".toFloatOpt // Some(123.0)
"1-2-3".toFloatOpt // None

"123".toDoubleOpt // Some(123.0)
"1-2-3".toDoubleOpt // None

"true".toBooleanOpt // Some(true)
"false".toBooleanOpt // Some(false)

"+".toBooleanOpt // Some(true)
"-".toBooleanOpt // Some(false)

"ON".toBooleanOpt // Some(true)
"OFF".toBooleanOpt // Some(false)

"yes".toBooleanOpt // Some(true)
"no".toBooleanOpt // Some(false)
```

### asInstanceOfOpt

```scala
import com.github.t3hnar.scalax.RichAny

(null: Any).asInstanceOfOpt[String] // None
("string": Any).asInstanceOfOpt[String] // Some
```

### RichEnum

Adds `withNameOpt` method additionally to default `withName`

```scala
  object Color extends Enumeration {
    val Green, Blue, Red = Value
  }

  import com.github.t3hnar.scalax.RichEnum

  Color.withNameOpt("Green") // Some(Green)
  Color.withNameOpt("Black") // None
```

### RichSet

Adds `collate` method to set. `collate` tries to compare sets of items in detail
and returns items which were added, updated (key relative) and deleted in `s2` regarding `s1`

```scala
  import com.github.t3hnar.scalax.RichSet

  case class Entity(id: Int, name: String)

  val s1 = Set(Entity(1, "1"), Entity(2, "2"), Entity(3, "3"))
  val s2 = Set(Entity(1, "1"), Entity(2, "u"), Entity(4, "4"))

  s1.collate(s2)(_.id) // Some(Set(Entity(4, "4")), Set(Entity(2, "u")), Set(3))
```

### RichDuration

```scala
  import com.github.t3hnar.scalax.RichDuration
  import concurrent.duration._

  60.seconds.toCoarsest // 1.minute
  60.minutes.toCoarsest // 1.hour
  180.minutes.toCoarsest // 3.hours
  24.hours.toCoarsest // 1.day
  Duration.Inf.toCoarsest // Duration.Inf
  Duration.MinusInf.toCoarsest // Duration.MinusInf
```

### RichPartialFunction
```scala
  import com.github.t3hnar.scalax.RichPartialFunction

  val pf1: PartialFunction[Int, String] = {
    case 0 => "0"
    case 1 => "1"
  }

  val pf2 = pf1.filter(_ == 0)

  pf2.isDefinedAt(0) // true
  pf2.isDefinedAt(1) // false
```

### RichTry

```scala
  import com.github.t3hnar.scalax.RichTry
  
  Try("10".toInt).fold("NumberFormatException for " + _)("Valid int: " + _)
```

### RichSetMap

```scala
  import com.github.t3hnar.scalax.RichSetMap

  val map = Map(1 -> Set(1))
  map.getOrEmpty(1) // Set()
  map.updatedSet(1, _ - 1) // Map()
  map.updatedSet(1, _ + 2) // Map(1 -> Set(1, 2))
  map.updatedSet(2, _ + 2) // Map(1 -> Set(1), 2 -> Set(2))
  map.updatedSet(1, _ => Set(1, 2, 3)) // Map(1 -> Set(1, 2, 3))
```

### Primitive

```scala
  import com.github.t3hnar.scalax.Primitive

  Primitive.unapply(()) // None
  Primitive.unapply(true) // Some(true)
  Primitive.unapply(' ') // Some( )
  Primitive.unapply(1) // Some(1)
  Primitive.unapply("") // None
```

### StandardAnyVal

```scala
  import com.github.t3hnar.scalax.StandardAnyVal

  StandardAnyVal.unapply(()) // Some(())
  StandardAnyVal.unapply(true) // Some(true)
  StandardAnyVal.unapply(' ') // Some( )
  StandardAnyVal.unapply(1) // Some(1)
  StandardAnyVal.unapply("") // None
```


### ExpiringCache

```scala
  import com.github.t3hnar.scalax.util.ExpiringCache

  val cache = new ExpiringCache[Int, Int](duration = 5,
                                        unit = TimeUnit.SECONDS,
                                        queryOverflow = 3)
  cache.put(0,0)
  cache.get(0) // Some(0)

  // after 5 seconds
  cache.get(0) // None, however it is not cleaned up yet, need one more query to go

  cache.get(0) // None, now it's cleaned up as we reached `queryOverflow` limit
```

## Setup

```scala
libraryDependencies += "com.github.t3hnar" %% "scalax" % "2.4"
```
