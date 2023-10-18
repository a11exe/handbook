# Spock and Groovy

[Introduction to Testing with Spock and Groovy](https://www.baeldung.com/groovy-spock)

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

mocking

```
given:
def paymentGateway = Mock(PaymentGateway)
def paymentService = new PaymentService(paymentGateway)

when:
paymentService.use(paymentGateway)

then:
1 * paymentGateway.isEnabled() >> true
```

verifying

```
1 * mockService.register(_) >> 'id1'		// checks that we call the register method on the service called exactly once (strict mocking) and passed any parameter
1 * mockService.register(_ as User) >> 'id1'	// verifies that the parameter is of the User class
1 * mockService.register(!null) >> 'id1'	// verifies that the parameter is not null
1 * mockService.register({it.name == 'Jimbo' && it.email == 'jb@test.com'}) >> 'id1'
```

combine

```
1 * mockService.subscribe({it.name == 'Jimbo'}, "computer studies")
1 * mockService.subscribe({it.name == 'Jimbo' >> it.email == 'jb@test.com'}, !null)
1 * mockService.subscribe(_, !null)
```

argument capture
```
 then: 'service is called with expected parameters'
        1 * mockService.register(_ as User) >> {arguments ->
            assert arguments[0].name == USERNAME && arguments[0].email == EMAIL
            // Note with an argument capture - as well as verifying input
            // you can also do other things - (if you need to), e.g.:
            arguments[0].name = 'I changed the name in the test!'
            test = arguments[0].name
        }
 
        assert test == 'I changed the name in the test!'
```
