# Spock and Groovy

class name FirstSpecification.groovy
```
class FirstSpecification extends Specification {
    def "one plus one should equal two"() {
        expect:
        1 + 1 == 2
    }
}
```

common test format
```
def "two plus two should equal four"() {
    given:
        int left = 2
        int right = 2

    when:
        int result = left + right

    then:
        result == 4
}
```

list
```
def list = [1, 2, 3, 4]
```

exception
```
then:
        thrown(IndexOutOfBoundsException)
```
```
then:
        noExceptionThrown()
```

parametrized

```
def "numbers to the power of two"(int a, int b, int c) {
  expect:
      Math.pow(a, b) == c

  where:
      a | b | c
      1 | 2 | 1
      2 | 2 | 4
      3 | 2 | 9
  }
```

```
def "numbers to the power of two"(int a, int b, int c) {
  expect:
      Math.pow(a, b) == c

  where:
      a | b | c
      1 | 2 | 1
      2 | 2 | 4
      3 | 2 | 9
  }
```


[based on](https://www.baeldung.com/groovy-spock)