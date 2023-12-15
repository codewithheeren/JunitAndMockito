## Mockito testing implementation 
### Project Structure 
![image](https://github.com/codewithheeren/JunitAndMockito/assets/87074236/06ba867e-da62-4a44-b9b2-fcbb0ac3955b)

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
### application.properties 
```properties 
spring.h2.console.enable=true
spring.datasource.url=jdbc:h2:mem:testdb
spring.datasource.driverClassName=org.h2.Driver
spring.datasource.username=sa
spring.datasource.password=
spring.jpa.hibernate.ddl-auto=create
spring.datasource.initialization-mode=always
spring.jpa.defer-datasource-initialization=true
spring.data.jpa.repositories.bootstrap-mode=default
```
---

### data.sql 

```sql
insert into item(id, name, price, quantity) values(10001,'Item1',10,20);
insert into item(id, name, price, quantity) values(10002,'Item2',5,10);
insert into item(id, name, price, quantity) values(10003,'Item3',15,2);
```
---
### ItemController.java

```java
package com.codewithheeren.contoller;

import java.util.List;
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

	@GetMapping("/all-items-from-database")
	public List<Item> retrieveAllItems() {
		return businessService.retrieveAllItems();
	}
}
```
---
### Item.java

```java
package com.codewithheeren.model;

import javax.persistence.Entity;
import javax.persistence.Id;
import javax.persistence.Table;
import javax.persistence.Transient;

@Entity
@Table
public class Item {
	@Id
	private int id;
	private String name;
	private int price;
	private int quantity;
	@Transient
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
### ItemRepository.java
```java
package com.codewithheeren.repository;

import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.stereotype.Repository;
import com.codewithheeren.model.Item;
@Repository
public interface ItemRepository extends JpaRepository<Item, Long> {
}

```
---
### ItemBusinessService.java
```java
package com.codewithheeren.service;

import java.util.List;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;
import org.springframework.stereotype.Service;
import com.codewithheeren.model.Item;
import com.codewithheeren.repository.ItemRepository;

@Service
public class ItemBusinessService {
	@Autowired
	private ItemRepository repository;

	public Item retreiveHardcodedItem() {
		return new Item(1, "Ball", 10, 100);
	}

	public List<Item> retrieveAllItems() {
		List<Item> items = repository.findAll();
		for (Item item : items) {
			item.setValue(item.getPrice() * item.getQuantity());
		}
		return items;
	}
}
```
---

### MockitoUnitTest05Application.java
```java
package com.codewithheeren.startapp;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.boot.autoconfigure.domain.EntityScan;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.data.jpa.repository.config.EnableJpaRepositories;
import org.springframework.transaction.annotation.EnableTransactionManagement;
import org.springframework.web.servlet.config.annotation.EnableWebMvc;
//Independently testing of service layer using repository mock 
@SpringBootApplication
@ComponentScan("com.codewithheeren")
@EntityScan("com.codewithheeren.model")
@EnableJpaRepositories("com.codewithheeren.repository")
@EnableWebMvc
@EnableTransactionManagement
public class MockitoUnitTest05Application {

	public static void main(String[] args) {
		SpringApplication.run(MockitoUnitTest05Application.class, args);
	}

}

```
---

### ItemBusinessServiceTest1.java
```java
package com.codewithheeren.startapp;

import static org.junit.Assert.assertEquals;
import static org.mockito.Mockito.when;
import java.util.Arrays;
import java.util.List;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.boot.test.mock.mockito.MockBean;
import org.springframework.test.context.junit4.SpringRunner;
import com.codewithheeren.model.Item;
import com.codewithheeren.repository.ItemRepository;
import com.codewithheeren.service.ItemBusinessService;

@RunWith(SpringRunner.class)
@SpringBootTest
public class ItemBusinessServiceTest1 {
	@Autowired
	private ItemBusinessService service;

	@MockBean
	private ItemRepository repository;

	@Test
	public void retrieveAllItems_basic() {
		when(repository.findAll())
				.thenReturn(Arrays.asList(new Item(2, "Item2", 10, 10), new Item(3, "Item3", 20, 20)));
		List<Item> items = service.retrieveAllItems();
		assertEquals(100, items.get(0).getValue());
		assertEquals(400, items.get(1).getValue());
	}
}

```
---
### ItemBusinessServiceTest2.java
```java
package com.codewithheeren.startapp;

import static org.junit.Assert.assertEquals;
import static org.mockito.Mockito.when;
import java.util.Arrays;
import java.util.List;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.junit.MockitoJUnitRunner;
import com.codewithheeren.model.Item;
import com.codewithheeren.repository.ItemRepository;
import com.codewithheeren.service.ItemBusinessService;

@RunWith(MockitoJUnitRunner.class)
public class ItemBusinessServiceTest2 {
	@InjectMocks
	private ItemBusinessService business;

	@Mock
	private ItemRepository repository;

	@Test
	public void retrieveAllItems_basic() {
		when(repository.findAll())
				.thenReturn(Arrays.asList(new Item(2, "Item2", 10, 10), new Item(3, "Item3", 20, 20)));
		List<Item> items = business.retrieveAllItems();
		Item item1 = items.get(0);
		item1.getValue();
		assertEquals(100, item1.getValue());
//      assertEquals(400, items.get(1).getValue());
	}
}

```
---
