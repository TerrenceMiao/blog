---
title: Fibonacci function implementation in Java 8
date: 2017-02-18 22:09:56
tags:
---

### Implementation

``` Java
package org.paradise.function;

import java.util.HashMap;
import java.util.Map;
import java.util.function.Function;

/**
 * Created by terrence on 12/12/2016.
 */
public final class FibonacciFunction {

    public static final Map<Integer, Long> FIBONACCI_MAP = new HashMap<>();

    public static final Function<Integer, Long> FIBONACCI = (x) ->
            FIBONACCI_MAP.computeIfAbsent(x,
                    n -> FunctionCalculator.FIBONACCI.apply(n - 2) + FunctionCalculator.FIBONACCI.apply(n - 1));

    static {
        FIBONACCI_MAP.put(0, 0L); // FIBONACCI(0)
        FIBONACCI_MAP.put(1, 1L); // FIBONACCI(1)
    }

    private FibonacciFunction() {

    }

}

```

### Unit test

``` Java
package org.paradise.function;

import org.junit.Test;

import static org.junit.Assert.assertEquals;

/**
 * Created by terrence on 12/12/2016.
 */
public class FibonacciFunctionTest {

    @Test
    public void testFibonacciFunction() throws Exception {

        assertEquals("Incorrect result", Long.valueOf(0), FunctionCalculator.FIBONACCI.apply(0));
        assertEquals("Incorrect result", Long.valueOf(1), FunctionCalculator.FIBONACCI.apply(1));
        assertEquals("Incorrect result", Long.valueOf(1), FunctionCalculator.FIBONACCI.apply(2));
        assertEquals("Incorrect result", Long.valueOf(2), FunctionCalculator.FIBONACCI.apply(3));
        assertEquals("Incorrect result", Long.valueOf(3), FunctionCalculator.FIBONACCI.apply(4));
        assertEquals("Incorrect result", Long.valueOf(5), FunctionCalculator.FIBONACCI.apply(5));
        assertEquals("Incorrect result", Long.valueOf(8), FunctionCalculator.FIBONACCI.apply(6));

        assertEquals("Incorrect result", Long.valueOf(13), FunctionCalculator.FIBONACCI.apply(7));
        assertEquals("Incorrect result", Long.valueOf(21), FunctionCalculator.FIBONACCI.apply(8));
        assertEquals("Incorrect result", Long.valueOf(34), FunctionCalculator.FIBONACCI.apply(9));
        assertEquals("Incorrect result", Long.valueOf(55), FunctionCalculator.FIBONACCI.apply(10));

        assertEquals("Incorrect result", Long.valueOf(12586269025L), FunctionCalculator.FIBONACCI.apply(50));
    }

}
```
