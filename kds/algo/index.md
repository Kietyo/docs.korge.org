---
layout: default
title: "Algorithms"
title_prefix: KDS
description: "genericBinarySearch, genericSort, mapWhile, getCyclic"
fa-icon: fa-hat-wizard
priority: 10
---

{% include toc_include.md max_level=3 %}

## binarySearch: `genericBinarySearch`, `binarySearch`
{: #genericBinarySearch }

Kds provides binarySearch for its collections limiting the indices used. Also provides a `genericBinarySearch` to execute the algorithm in any possible kind of collection. It allows to get exact possitions or nearest positionss when no value is found:

```kotlin
val v = intArrayOf(10, 20, 30, 40, 50)
assertEquals(0, v.binarySearch(10).index)
assertEquals(1, v.binarySearch(20).index)
assertEquals(2, v.binarySearch(30).index)
assertEquals(3, v.binarySearch(40).index)
assertEquals(4, v.binarySearch(50).index)

assertEquals(true, v.binarySearch(10).found)
assertEquals(false, v.binarySearch(11).found)

assertEquals(2, v.binarySearch(21).nearIndex)
```

## genericSort

`genericSort` allows to sort any array-like structure fully or partially without allocating and without having to reimplementing any sort algorithm again.
You just have to implement a `compare` and `swap` methods that receive indices
in the array to compare and optionally a `shiftLeft` method (that fallbacks to use the `swap` one). The SortOps implementation is usually an `object` to prevent allocating.

```kotlin
fun <T> genericSort(subject: T, left: Int, right: Int, ops: SortOps<T>): T
abstract class SortOps<T> {
    abstract fun compare(subject: T, l: Int, r: Int): Int
    abstract fun swap(subject: T, indexL: Int, indexR: Int)
    open fun shiftLeft(subject: T, indexL: Int, indexR: Int)
}
```

So a simple implementation that would sort any `MutableList` could be:

```kotlin
val result = genericSort(arrayListOf(10, 30, 20, 10, 5, 3, 40, 7), 0, 7, ArrayListSortOps)
assertEquals(listOf(3, 5, 7, 10, 10, 20, 30, 40), result)

object ArrayListSortOps : SortOps<ArrayList<Int>>() {
    override fun compare(subject: ArrayList<Int>, l: Int, r: Int): Int {
        return subject[l].compareTo(subject[r])
    }

    override fun swap(subject: ArrayList<Int>, indexL: Int, indexR: Int) {
        val tmp = subject[indexA]
        subject[indexA] = subject[indexB]
        subject[indexB] = tmp
    }
}
```

## mapWhile: `mapWhile`, `mapWhileArray`, `mapWhileInt`, `mapWhileFloat`, `mapWhileDouble`
{: #mapWhile }

This method allows to generate a collection by providing a condition and a generator:

```kotlin
val iterator = listOf(1, 2, 3).iterator()
assertEquals(listOf(1, 2, 3), mapWhile({ iterator.hasNext() }) { iterator.next()})
```

## getCyclic: `List.getCyclic`, `Array.getCyclic`
{: #getCyclic }

For lists and arrays Kds defines a `getCyclic` extension method to get an element wrapping its bounds. So `list.getCylic(-1)` would return the last element of the List, and `list.getCyclic(size)` would return the element at 0:

```kotlin
assertEquals("a", arrayOf("a", "b").getCyclic(2))
assertEquals("b", arrayOf("a", "b").getCyclic(-1))
```
