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

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;
import com.codewithheeren.model.Item;
import com.codewithheeren.service.ItemBusinessService;

@RestController
public class ItemController {

	@Autowired
	private ItemBusinessService businessService;

	@GetMapping("/dummy-item")
	public Item dummyItem() {
		return new Item(1, "Mobile", 35000, 1);
	}

	@GetMapping("/item-from-business-service")
	public Item itemFromBusinessService() {
		Item item = businessService.retreiveHardcodedItem();
		return item;
	}
}
```
---
### Item.java

```java
ppackage com.codewithheeren.model;

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
### ItemBusinessService.java
```java
package com.codewithheeren.service;

import java.util.List;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;
import com.codewithheeren.model.Item;

@Component
public class ItemBusinessService {

    public Item retreiveHardcodedItem() {
        return new Item(1, "laptop", 24000, 2);
    }
}

```
---
### MockitoUnitTest03Application.java
```java
package com.codewithheeren.startapp;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.annotation.ComponentScan;

//Implementing integration test of service layer and controller layer.
@SpringBootApplication
@ComponentScan("com.codewithheeren")
public class MockitoUnitTest03Application {

	public static void main(String[] args) {
		SpringApplication.run(MockitoUnitTest03Application.class, args);
	}
}

```
---

### ItemControllerTest.java
```java
package com.codewithheeren.startapp;

import static org.mockito.Mockito.when;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.content;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.status;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.web.servlet.WebMvcTest;
import org.springframework.boot.test.mock.mockito.MockBean;
import org.springframework.http.MediaType;
import org.springframework.test.context.junit4.SpringRunner;
import org.springframework.test.web.servlet.MockMvc;
import org.springframework.test.web.servlet.MvcResult;
import org.springframework.test.web.servlet.RequestBuilder;
import org.springframework.test.web.servlet.request.MockMvcRequestBuilders;
import com.codewithheeren.contoller.ItemController;
import com.codewithheeren.model.Item;
import com.codewithheeren.service.ItemBusinessService;

@RunWith(SpringRunner.class)
@WebMvcTest(ItemController.class)
public class ItemControllerTest {
	@Autowired
	private MockMvc mockMvc;

	@MockBean
	private ItemBusinessService businessService;

	@Test
	public void itemFromBusinessService_basic() throws Exception {
		when(businessService.retreiveHardcodedItem()).thenReturn(new Item(2, "Item2", 10, 10));
		RequestBuilder request = MockMvcRequestBuilders.get("/item-from-business-service")
				                .accept(MediaType.APPLICATION_JSON);
		MvcResult result = mockMvc.perform(request).andExpect(status().isOk())
				           .andExpect(content().json("{id:2,name:Item2,price:10}")).andReturn();
		// JSONAssert.assertEquals(expected, result.getResponse().getContentAsString(),
		// false);
	}
}

```
---
### JsonAssertTest.java
```java
package com.codewithheeren.startapp;

import org.json.JSONException;
import org.junit.Test;
import org.skyscreamer.jsonassert.JSONAssert;
//Testing json data response using JSONAssert library.
public class JsonAssertTest {
	//This is hard coded as of now but later on this will come from rest api.
	String actualResponse = "{\"id\":1,\"name\":\"Mobile\",\"price\":35000,\"quantity\":1}";

	@Test
	public void jsonAssert_StrictTrue_ExactMatchExceptForSpaces() throws JSONException {
		String expectedResponse = "{\"id\":1,\"name\":\"Mobile\",\"price\":35000,\"quantity\":1}";
		JSONAssert.assertEquals(expectedResponse, actualResponse, true);
	}

	@Test
	public void jsonAssert_StrictFalse() throws JSONException {
		String expectedResponse = "{\"id\":1,\"name\":\"Mobile\",\"price\":35000}";
		JSONAssert.assertEquals(expectedResponse, actualResponse, false);
	}

	@Test
	public void jsonAssert_WithoutEscapeCharacters() throws JSONException {
		String expectedResponse = "{id:1,name:Mobile, price:35000}";
		JSONAssert.assertEquals(expectedResponse, actualResponse, false);
	}
}

```
---