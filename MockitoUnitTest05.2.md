## Mockito testing implementation 
### Project Structure
![image](https://github.com/codewithheeren/JunitAndMockito/assets/87074236/e5665982-1437-4aad-bb5b-1b0dfdc541e1)

### Note
```text
Difference between @MockBean and @Mock
Both are use to create mock instance of any class.
@MockBean we use where application context is up.(In case when we use @SpringBootTest or @WebMvcTest)
@Mock we use when we dont have application context up like eg.- MockUnitTest04
```

### pom.xml 
```xml
    <dependencies>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-web</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-devtools</artifactId>
			<scope>runtime</scope>
			<optional>true</optional>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-data-mongodb</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-test</artifactId>
			<scope>test</scope>
		</dependency>
	</dependencies>

```
---
### application.properties 
```properties 
spring.data.mongodb.host=localhost
spring.data.mongodb.port=27017 
spring.data.mongodb.database=rough
```
---

### UserController.java

```java
package com.codewithheeren.spring.mockito.api.controller;

import java.util.List;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.DeleteMapping;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RestController;
import com.codewithheeren.spring.mockito.api.model.User;
import com.codewithheeren.spring.mockito.api.service.UserService;

@RestController
public class UserController {
	@Autowired
	private UserService service;

	@PostMapping(value = "/save")
	public User saveUser(@RequestBody User user) {
		return service.addUser(user);
	}

	@GetMapping("/getUsers")
	public List<User> findAllUsers() {
		return service.getUsers();
	}

	@GetMapping("/getUserByAddress/{address}")
	public List<User> findUserByAddress(@PathVariable String address) {
		return service.getUserbyAddress(address);
	}

	@DeleteMapping(value = "/remove")
	public User removeUser(@RequestBody User user) {
		service.deleteUser(user);
		return user;
	}
}

```
---
### User.java

```java
package com.codewithheeren.spring.mockito.api.model;

import org.springframework.data.annotation.Id;
import org.springframework.data.mongodb.core.mapping.Document;

@Document(collection = "users")

public class User {
	@Id
	private String id;
	private String name;
	private int age;
	private String address;

	public User() {
		super();
	}

	public User(String name, int age, String address) {
		super();
		this.name = name;
		this.age = age;
		this.address = address;
	}

	public String getId() {
		return id;
	}

	public void setId(String id) {
		this.id = id;
	}

	public String getName() {
		return name;
	}

	public void setName(String name) {
		this.name = name;
	}

	public int getAge() {
		return age;
	}

	public void setAge(int age) {
		this.age = age;
	}

	public String getAddress() {
		return address;
	}

	public void setAddress(String address) {
		this.address = address;
	}

	@Override
	public String toString() {
		return "User [id=" + id + ", name=" + name + ", age=" + age + ", address=" + address + "]";
	}
}
```
---
### UserRepository.java
```java
package com.codewithheeren.spring.mockito.api.dao;

import java.util.List;
import org.springframework.data.mongodb.repository.MongoRepository;
import com.codewithheeren.spring.mockito.api.model.User;

public interface UserRepository extends MongoRepository<User, String> {

	List<User> findByAddress(String address);

}

```
---
### UserService.java
```java
package com.codewithheeren.spring.mockito.api.service;

import java.util.List;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;
import com.codewithheeren.spring.mockito.api.dao.UserRepository;
import com.codewithheeren.spring.mockito.api.model.User;

@Service
public class UserService {

	@Autowired
	private UserRepository repository;

	public User addUser(User user) {
		return repository.save(user);
	}

	// when this method invoke from mockito test, it will not render data from db
	// instead it will render mock data.
	public List<User> getUsers() {
		List<User> users = repository.findAll();
		System.out.println("Getting data from DB : " + users);
		return users;
	}

	public List<User> getUserbyAddress(String address) {
		return repository.findByAddress(address);
	}

	public void deleteUser(User user) {
		repository.delete(user);
	}
}

```
---

### Application.java
```java
package com.codewithheeren.startapp;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.data.mongodb.repository.config.EnableMongoRepositories;

//Independently testing of service layer using repository mock using mongo repository
@SpringBootApplication
@ComponentScan("com.codewithheeren")
@EnableMongoRepositories(basePackages = "com.codewithheeren.spring.mockito.api.dao")
public class Application {

	public static void main(String[] args) {
		SpringApplication.run(Application.class, args);
	}
}
```
---

### ApplicationTests.java
```java
package com.codewithheeren.startapp;
import static org.junit.Assert.assertEquals;
import static org.mockito.Mockito.times;
import static org.mockito.Mockito.verify;
import static org.mockito.Mockito.when;
import java.util.stream.Collectors;
import java.util.stream.Stream;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.boot.test.mock.mockito.MockBean;
import org.springframework.test.context.junit4.SpringRunner;
import com.codewithheeren.spring.mockito.api.dao.UserRepository;
import com.codewithheeren.spring.mockito.api.model.User;
import com.codewithheeren.spring.mockito.api.service.UserService;

@RunWith(SpringRunner.class)
@SpringBootTest
public class ApplicationTests {

	@Autowired
	private UserService service;

	@MockBean
	private UserRepository repository;

	@Test
	public void getUsersTest() throws Exception{
		when(repository.findAll()).thenReturn(Stream
				.of(new User( "Danile", 31, "USA"), new User( "Huy", 35, "UK")).collect(Collectors.toList()));
		assertEquals(2, service.getUsers().size());
	}

	@Test
	public void getUserbyAddressTest() {
		String address = "Bangalore";
		when(repository.findByAddress(address))
				.thenReturn(Stream.of(new User("Danile", 31, "USA")).collect(Collectors.toList()));
		assertEquals(1, service.getUserbyAddress(address).size());
	}

	@Test
	public void saveUserTest() {
		User user = new User("Pranya", 33, "Pune");
		when(repository.save(user)).thenReturn(user);
		assertEquals(user, service.addUser(user));
	}

	@Test
	public void deleteUserTest() {
		User user = new User("Pranya", 33, "Pune");
		service.deleteUser(user);
		//varify will varify wheather delete method called once or not. as delete is not returning any value.
		verify(repository, times(1)).delete(user);
	}

}
```
---
