---
layout: default
---

# Mockito Argument Matcher - Fundamentals

### Example 1

In this example we are testing a method as usually with Mockito. Notice that we are using the value 10
in the **findUserNameById()** method

```java
public class MockitoArgumentMatcherTest {

	@Test
	public void shouldVerifyIfTheMethodWasCalledUsingASpecificValue() throws Exception {
		UserController userController = mock(UserController.class);

		when(userController.findUserNameById(10L)).thenReturn("Mockito");

		String userName = userController.findUserNameById(10L);

		assertThat(userName, equalTo("Mockito"));
	}

}    
```

### Example 2

Now let's imagine a challenging example. Sometimes you don't care about the value that is used to test your method

The method should receive any value and your test should pass. In this example we are using the **Mockito.anyLong()** method
that, as you may guess, say to Mockito:

- Hey, don't worry about the value! You should be called with any value. In this case, any Long value!

```java
public class MockitoArgumentMatcherTest {

	@Test
	public void shouldStubTheMethodUsingAnyValue() throws Exception {
		UserController userController = mock(UserController.class);

		when(userController.findUserNameById(anyLong())).thenReturn("Mockito");

		String userName = userController.findUserNameById(10L);

		assertThat(userName, equalTo("Mockito"));
	}

}    
```

Notice that we passed the value 10 to the method, and everything is working fine

Let's se a more challenging example in the next code

### Example 3

Now imagine that you will call a method that will call another one. In this example we are:

- Calling the UserController.generatedId

- This method is calling the Users.generate method

But this is the challenge: The second one get a Random value! Now how are you going to test your code?

Let's try!

```java
public class MockitoArgumentMatcherTest {

	@Test
	public void shouldVerifyIfTheMethodWasCalledUsingAnyValue() throws Exception {
		Users users = mock(Users.class);

		UserController userController = new UserController(users);

		userController.generateId();

        //Which number should be used here???
		verify(users).generate(???);
	}

}
```

As you can see, we don't know the number that will be generated randomly by the method! We can't just
put a number here because Mockito will say:

- Hey, I can verify the **generate** method but it is being called with different values

So, let's fix that in the next example

### Example 4

Now we will use the ArgumentMatcher again to test the code

```java
public class MockitoArgumentMatcherTest {

	@Test
	public void shouldVerifyIfTheMethodWasCalledUsingAnyValue() throws Exception {
		Users users = mock(Users.class);

		UserController userController = new UserController(users);

		userController.generateId();

		verify(users).generate(anyInt());
	}

}
```

Now, does not matter which number is generated by the method **generateId()**, the test will pass :)

**UserController utility class**

```java
class UserController {

	private Users users;

	public UserController(Users users) {
		this.users = users;
	}

	public void generateIdAndNickname() {
		int generatedValue = new Random().nextInt(10);
		users.saveIdWithNewNickname(generatedValue, "Nickname");
	}

	public String findUserByNameAndAge(String name, Long age) {
		return "Yes, I'm returning nothing :P";
	}

	public String findUserNameById(Long id) {
		if (id == 10L) {
			return "Alexandre Gama";
		}
		return "Craft Coder - Mockito";
	}

	public void generateId() {
		//we can't test it, since it is generated randomly
		int generatedValue = new Random().nextInt(10);
		users.generate(generatedValue);
	}

}
```

**Users utility class**


```java
class Users {

	public void generate(int value) {
		System.out.println("Generated value: " + value);
	}

	public void saveIdWithNewNickname(int id, String nickname) {
		System.out.println("Generated value: " + id);
		System.out.println("Generated nickname: " + nickname);
	}

}
```

[Back to Stubbing void methods](mockito-argument-matcher-main-page)

[<- Back to home](/)
