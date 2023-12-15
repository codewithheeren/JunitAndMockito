## Mockito testing implementation 

### pom.xml 
```xml
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
			<artifactId>spring-boot-starter-test</artifactId>
			<scope>test</scope>
		</dependency>
	</dependencies>

```
---
### ItemController.java

```java
package com.codewithheeren.contoller;

import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;
import com.codewithheeren.model.Item;

@RestController
public class ItemController {

	@GetMapping("/dummy-item")
	public Item dummyItem() {
		return new Item(1, "Mobile", 35000, 1);

	}

}
```
---
### Item.java

```java
package com.codewithheeren.model;

public class Item {
	private int id;
	private String name;
	private int price;
	private int quantity;
	private int value;

	protected Item() {
	}

	public Item(int id, String name, int price, int quantity) {
		this.id = id;
		this.name = name;
		this.price = price;
		this.quantity = quantity;
	}

	public int getId() {
		return id;
	}

	public String getName() {
		return name;
	}

	public int getPrice() {
		return price;
	}

	public int getQuantity() {
		return quantity;
	}

	public int getValue() {
		return value;
	}

	public void setValue(int value) {
		this.value = value;
	}

	@Override
	public String toString() {
		return "Item [id=" + id + ", name=" + name + ", price=" + price + ", quantity=" + quantity + ", value=" + value
				+ "]";
	}
}

```
---
### MockitoUnitTest02Application.java
```java
package com.codewithheeren.startapp;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.annotation.ComponentScan;

//Validating json response from rest controller.
@SpringBootApplication
@ComponentScan("com.codewithheeren")
public class MockitoUnitTest02Application {

	public static void main(String[] args) {
		SpringApplication.run(MockitoUnitTest02Application.class, args);
	}
}

```
---

### ItemControllerTest.java
```java
package com.codewithheeren.startapp;

import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.content;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.status;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.web.servlet.WebMvcTest;
import org.springframework.http.MediaType;
import org.springframework.test.context.junit4.SpringRunner;
import org.springframework.test.web.servlet.MockMvc;
import org.springframework.test.web.servlet.MvcResult;
import org.springframework.test.web.servlet.RequestBuilder;
import org.springframework.test.web.servlet.request.MockMvcRequestBuilders;
import com.codewithheeren.contoller.ItemController;

//you can also verify specific fields data of json using matcher.
@RunWith(SpringRunner.class)
@WebMvcTest(ItemController.class)
public class ItemControllerTest {
	@Autowired
	private MockMvc mockMvc;

	@Test
	public void testDummyItem1() throws Exception {
		System.out.println(mockMvc);
		RequestBuilder request = MockMvcRequestBuilders.get("/dummy-item").accept(MediaType.APPLICATION_JSON);
		MvcResult result = mockMvc.perform(request).andExpect(status().isOk())
				.andExpect(content().json("{\"id\":1,\"name\":\"Mobile\",\"price\":35000,\"quantity\":1}")).andReturn();
	}

	@Test
	public void testDummyItem2() throws Exception {
		RequestBuilder request = MockMvcRequestBuilders.get("/dummy-item").accept(MediaType.APPLICATION_JSON);
		MvcResult result = mockMvc.perform(request).andExpect(status().isOk())
				.andExpect(content().json("{\"id\":1,\"name\":\"Mobile\"}")).andReturn();
	}
}

```
---
