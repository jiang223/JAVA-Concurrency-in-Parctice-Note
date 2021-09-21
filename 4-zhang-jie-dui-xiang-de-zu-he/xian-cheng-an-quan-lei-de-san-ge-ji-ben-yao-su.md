# 线程安全类的三个基本要素

```text
package net.jcip.examples;

import net.jcip.annotations.*;

/**
 * Counter
 * <p/>
 * Simple thread-safe counter using the Java monitor pattern
 *
 * @author Brian Goetz and Tim Peierls
 */
@ThreadSafe
public final class Counter {
    @GuardedBy("this") private long value = 0;

    public synchronized long getValue() {
        return value;
    }

    public synchronized long increment() {
        if (value == Long.MAX_VALUE)
            throw new IllegalStateException("counter overflow");
        return ++value;
    }
}
```

 1.不变性条件

         vlalue域其状态空间从LONG_MIN到LONG\_MAX的范围，确保值域在并发情况下不应该破坏该条件_

 2.后验条件

         如果counter当前状态是17，则它下一状态则应该是18

 3.先验条件

        不能从空队列中移除一个元素，在删除元素前，队列必须处于非空的状态。





