---
title: Factorial function implementation in Java 8
date: 2017-02-18 22:15:33
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
public final class FactorialFunction {

    public static final Map<Integer, Long> FACTORIAL_MAP = new HashMap<>();

    public static final Function<Integer, Long> FACTORIAL = (x) ->
            FACTORIAL_MAP.computeIfAbsent(x,
                    n -> n * FactorialFunction.FACTORIAL.apply(n - 1));

    static {
        FACTORIAL_MAP.put(1, 1L); // FACTORIAL(1)
    }

    private FactorialFunction() {

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
public class FactorialFunctionTest {

    @Test
    public void testFactorialFunction() throws Exception {

        assertEquals("Incorrect result", Long.valueOf(1), FactorialFunction.FACTORIAL.apply(1));
        assertEquals("Incorrect result", Long.valueOf(2), FactorialFunction.FACTORIAL.apply(2));

        assertEquals("Incorrect result", Long.valueOf(3628800), FactorialFunction.FACTORIAL.apply(10));
    }

}
```
