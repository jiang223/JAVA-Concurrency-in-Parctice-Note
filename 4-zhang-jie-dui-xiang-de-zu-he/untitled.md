# 实例封闭

## 1、JAVA监视器模式

遵守JAVA监视器模式的对象会将对象的所有可变变量都封装起来，并由对象自己的内置锁来保护。

例子: 

```java
package net.jcip.examples;

import net.jcip.annotations.*;

/**
 * PrivateLock
 * <p/>
 * Guarding state with a private lock
 *
 * @author Brian Goetz and Tim Peierls
 */
public class PrivateLock {
    private final Object myLock = new Object();
    @GuardedBy("myLock") Widget widget;

    void someMethod() {
        synchronized (myLock) {
            // Access or modify the state of widget
        }
    }
}
```

Vector与HashTable同样也使用监视器模式来实现线程安全的类

### PirveateLock是使用私有锁的。

私有锁与内置锁的区别，私有的锁对象可以将锁封装起来，客户端程序无法得到锁对象。



## 2、示例车辆追踪

```java
package net.jcip.examples;

import java.util.*;

import net.jcip.annotations.*;

/**
 * MonitorVehicleTracker
 * <p/>
 * Monitor-based vehicle tracker implementation
 *
 * @author Brian Goetz and Tim Peierls
 */
@ThreadSafe
 public class MonitorVehicleTracker {
    @GuardedBy("this") private final Map<String, MutablePoint> locations;

    public MonitorVehicleTracker(Map<String, MutablePoint> locations) {
        this.locations = deepCopy(locations);
    }

    public synchronized Map<String, MutablePoint> getLocations() {
        return deepCopy(locations);
    }

    public synchronized MutablePoint getLocation(String id) {
        MutablePoint loc = locations.get(id);
        return loc == null ? null : new MutablePoint(loc);
    }
    /**
    设置车辆的坐标
    **/
    public synchronized void setLocation(String id, int x, int y) {
        MutablePoint loc = locations.get(id);
        if (loc == null)
            throw new IllegalArgumentException("No such ID: " + id);
        loc.x = x;
        loc.y = y;
    }

    private static Map<String, MutablePoint> deepCopy(Map<String, MutablePoint> m) {
        Map<String, MutablePoint> result = new HashMap<String, MutablePoint>();

        for (String id : m.keySet())
            result.put(id, new MutablePoint(m.get(id)));

        return Collections.unmodifiableMap(result);
    }
}
```

4-5

```java
package net.jcip.examples;

import net.jcip.annotations.*;

/**
 * MutablePoint
 * <p/>
 * Mutable Point class similar to java.awt.Point
 *
 * @author Brian Goetz and Tim Peierls
 */
@NotThreadSafe
public class MutablePoint {
    public int x, y;

    public MutablePoint() {
        x = 0;
        y = 0;
    }

    public MutablePoint(MutablePoint p) {
        this.x = p.x;
        this.y = p.y;
    }
}
```

getLocaltions通过在返回客户代码之前复制可变的数据来维持线程安全性的。在车辆容器非常大的情况下将极大的降低性能。因为加了锁，即使车辆发生位置上的变化，但也不会实时更新，我觉得倒没问题就是了。

