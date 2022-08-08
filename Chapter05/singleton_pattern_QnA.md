## Lazy initialization in Java

- Multi Thread 환경에서는 동시에 instance method에 접근하여 여러 개의 객체를 만듦으로써 Singleton의 속성이 사라질 수 있다.
- 따라서 Single Thread 환경에서 활용할 수 있다.

```java
public class GFG{
    // getInstance()로만 접근가능함
    private static GFG instance;
    private GFG(){
        // private 생성자
    }
    public static GFG getInstance(){
        if(instance == null){
            instance = new GFG();
        }
        return instance;
    }
}
```

- **장점**
    - 기존 Singleton Pattern의 장점과 동일하다.
    - 예외처리가 가능하다.
    - 매번 null 조건을 체크할 수 있다.
    - Multi Thread 환경에서는 Singleton의 속성이 사라질 수 있다.

---

## Lazy initialization in JavaScript

- Lazy-Initialized Singleton을 만드는 npm package가 있다.

```javascript
const lazySingleton = require('lazy-singleton');
const lazyDependency = lazySingleton(someExpensiveFunction)(...)
// later - the expensive function won't be called until here
lazyDependency().doSomething();
// calling wrapped function
const lazyInstance = new lazySingleton.Sync(SomeConstructor)(...argsToPass);
lazyInstance().memberFunction();
 
// it can be used as a lazy require wrapper - note we aren't chaining (...args) on the initial call.
const lazyRequire = require('lazy-singleton')(require);
const _ = lazyRequire('lodash');
const lazyFoo = lazyRequire('foo');
_().isNumber(3);
```

---

## References

- [Java Singleton](https://www.geeksforgeeks.org/java-singleton-design-pattern-practices-examples/)
- [npm - Lazy Singleton](https://www.npmjs.com/package/lazy-singleton)