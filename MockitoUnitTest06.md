## Mockito testing implementation 
### Project Structure 
![image](https://github.com/codewithheeren/JunitAndMockito/assets/87074236/9ccab43f-2d29-4b95-bb93-61e5d845b34d)

### Note-
```text
why we use @AutoConfigureTestDatabase ?

The @AutoConfigureTestDatabase annotation is used to configure the behavior of the test database when running integration tests in a Spring Boot application. By default, the @SpringBootTest annotation will start an in-memory database for testing purposes, but you can use the @AutoConfigureTestDatabase annotation to override this behavior.

The replace attribute of the @AutoConfigureTestDatabase annotation allows you to specify how the test database should be replaced. There are three possible values for this attribute:

AutoConfigureTestDatabase.Replace.NONE: This value indicates that the test database should not be replaced. This is the default behavior.

(If you are using JUnit 5) You do NOT need to add

@RunWith(SpringRunner.class) on top of

@SpringBootTest(webEnvironment=WebEnvironment.RANDOM_PORT)

JUnit 4 Code

@RunWith(SpringRunner.class)


@SpringBootTest(webEnvironment=WebEnvironment.RANDOM_PORT)

JUnit 5 Code

@SpringBootTest(webEnvironment=WebEnvironment.RANDOM_PORT)

```
---
### pom.xml 
```xml
   <dependencies>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-data-jpa</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-web</artifactId>
		</dependency>

		<dependency>
			<groupId>mysql</groupId>
			<artifactId>mysql-connector-java</artifactId>
			<scope>runtime</scope>
		</dependency>
		<dependency>
			<groupId>org.projectlombok</groupId>
			<artifactId>lombok</artifactId>
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
spring.datasource.driverClassName=com.mysql.cj.jdbc.Driver
spring.datasource.url=jdbc:mysql://localhost:3306/emanage?createDatabaseIfNotExist=true
spring.datasource.username=root
spring.datasource.password=root
spring.jpa.show-sql=true
 spring.jpa.database-platform=org.hibernate.dialect.MySQL5InnoDBDialect
spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.MySQL8Dialect
spring.jpa.generate-ddl=true
spring.jpa.hibernate.ddl-auto = create
#enaable data.sql file injection
spring.jpa.defer-datasource-initialization=true
spring.sql.init.mode=always

```
---
### data.sql
```sql
INSERT INTO emanage.contact values(1001,'heeren@gmail.com','heeren','singh','23423423');
INSERT INTO emanage.contact values(1002,'test@gmail.com','testfirstname','testlastname','12345667');
```

### ContactController.java

```java
package com.codewithheeren.controller;

import com.codewithheeren.entity.Contact;
import com.codewithheeren.service.ContactService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;
import java.util.List;

@RestController
public class ContactController {
	@Autowired
	ContactService contactService;

	@PostMapping("/contact")
	public ResponseEntity<Contact> saveContact(@RequestBody Contact contact) {
		return ResponseEntity.ok(contactService.saveContact(contact));
	}

	@GetMapping("/contact/{id}")
	public ResponseEntity<Contact> getContactById(@PathVariable Long id) {
		return ResponseEntity.ok(contactService.getContactById(id));
	}

	@GetMapping("/contacts")
	public ResponseEntity<List<Contact>> getContactList() {
		return ResponseEntity.ok(contactService.getContactList());
	}

	@DeleteMapping("/contact")
	public void deleteContact(@RequestBody Contact contact) {
		contactService.deleteContact(contact);
	}
}

```
---
### Contact.java

```java
package com.codewithheeren.entity;

import javax.persistence.*;

@Entity
@Table(name = "contact")
public class Contact {
	@Id
	@GeneratedValue(strategy = GenerationType.AUTO)
	@Column(name = "id")
	private Long id;

	@Column(name = "first_name")
	private String firstName;

	@Column(name = "last_name")
	private String lastName;

	@Column(name = "email")
	private String email;

	@Column(name = "phone_no")
	private String phoneNo;

	public Long getId() {
		return id;
	}

	public Contact(Long id, String firstName, String lastName, String email, String phoneNo) {
		super();
		this.id = id;
		this.firstName = firstName;
		this.lastName = lastName;
		this.email = email;
		this.phoneNo = phoneNo;
	}

	public Contact() {
		super();
	}

	public void setId(Long id) {
		this.id = id;
	}

	public String getFirstName() {
		return firstName;
	}

	public void setFirstName(String firstName) {
		this.firstName = firstName;
	}

	public String getLastName() {
		return lastName;
	}

	public void setLastName(String lastName) {
		this.lastName = lastName;
	}

	public String getEmail() {
		return email;
	}

	public void setEmail(String email) {
		this.email = email;
	}

	public String getPhoneNo() {
		return phoneNo;
	}

	public void setPhoneNo(String phoneNo) {
		this.phoneNo = phoneNo;
	}

	@Override
	public String toString() {
		return "Contact [id=" + id + ", firstName=" + firstName + ", lastName=" + lastName + ", email=" + email
				+ ", phoneNo=" + phoneNo + "]";
	}
}

```
---
### UserRepository.java
```java
package com.codewithheeren.repository;

import com.codewithheeren.entity.Contact;
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.stereotype.Repository;

@Repository
public interface ContactRepository extends JpaRepository<Contact, Long> {
}

```
---
### ContactService.java
```java
package com.codewithheeren.service;

import com.codewithheeren.entity.Contact;
import com.codewithheeren.exception.ContactNotFoundException;
import com.codewithheeren.repository.ContactRepository;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.data.repository.support.Repositories;
import org.springframework.stereotype.Service;

import java.util.List;

@Service
public class ContactService {
	@Autowired
	ContactRepository contactRepository;

	public Contact saveContact(Contact contact) {
		return contactRepository.save(contact);
	}

	public List<Contact> getContactList() {
		return contactRepository.findAll();
	}

	public Contact getContactById(Long id) {
		return contactRepository.findById(id).orElseThrow(() -> new ContactNotFoundException("Contact Not Found" + id));

	}

	public void deleteContact(Contact contact) {
		contactRepository.delete(contact);
	}
}

```
---
### ContactNotFoundException.java
```java
package com.codewithheeren.exception;

public class ContactNotFoundException extends RuntimeException {
    public ContactNotFoundException(String message) {
        super(message);
    }
}

```
---
### Application.java
```java
package com.codewithheeren;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
//Implementing controller layer testing with service class mock.
//Implementing service layer testing with repository mock.
//implementing repository layer testing.
@SpringBootApplication
public class Application {

	public static void main(String[] args) {
		SpringApplication.run(Application.class, args);
	}
}

```
---

### ApplicationTests.java
```java
package com.codewithheeren;

import org.junit.jupiter.api.Test;
import org.springframework.boot.test.context.SpringBootTest;

@SpringBootTest
class ApplicationTests {

	@Test
	void contextLoads() {
	}

}

```
---
### ContactControllerTest.java
```java
package com.codewithheeren.controller;

import com.codewithheeren.entity.Contact;
import com.codewithheeren.service.ContactService;
import com.fasterxml.jackson.core.JsonProcessingException;
import com.fasterxml.jackson.databind.ObjectMapper;
import org.junit.jupiter.api.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.web.servlet.WebMvcTest;
import org.springframework.boot.test.mock.mockito.MockBean;
import org.springframework.http.MediaType;
import org.springframework.test.context.junit4.SpringRunner;
import org.springframework.test.web.servlet.MockMvc;
import java.util.Arrays;
import java.util.List;
import static org.mockito.Mockito.doNothing;
import static org.mockito.Mockito.when;
import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.*;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.*;

@RunWith(SpringRunner.class)
@WebMvcTest(ContactController.class)
public class ContactControllerTest {

	@MockBean
	ContactService contactService;

	@Autowired
	MockMvc mockMvc;

	@Test
	public void test_saveContact() throws Exception {
		Contact contact = new Contact(100L, "Lipsa", "Patra", "lipsa@gmail.com", "123456");
		when(contactService.saveContact(contact)).thenReturn(contact);
		mockMvc.perform(post("/contact").contentType(MediaType.APPLICATION_JSON)
				.content(new ObjectMapper().writeValueAsString(contact))).andExpect(status().isOk());

	}

	@Test
	public void test_getContactById() throws Exception {
		Contact contact = new Contact(100L, "Lipsa", "Patra", "lipsa@gmail.com", "123456");
		when(contactService.getContactById(100L)).thenReturn(contact);
		mockMvc.perform(get("/contact/{id}", 100L).contentType(MediaType.APPLICATION_JSON)).andExpect(status().isOk())
				.andExpect(jsonPath("$.firstName").value("Lipsa"));
	}

	@Test
	public void test_getContactList() throws Exception {
		List<Contact> contactList = Arrays.asList(new Contact(100L, "Lipsa", "Patra", "lipsa@gmail.com", "123456"),
				new Contact(101L, "Robert", "Frost", "robert@gmail.com", "56789"));

		when(contactService.getContactList()).thenReturn(contactList);
		mockMvc.perform(get("/contacts").contentType(MediaType.APPLICATION_JSON)).andExpect(status().isOk())
				.andExpect(jsonPath("$[0].firstName").value("Lipsa"));

	}

	@Test
	public void test_deleteContact() throws Exception {
		Contact contact = new Contact(100L, "Lipsa", "Patra", "lipsa@gmail.com", "123456");
		doNothing().when(contactService).deleteContact(contact);
		mockMvc.perform(delete("/contact").contentType(MediaType.APPLICATION_JSON)
				.content(new ObjectMapper().writeValueAsString(contact))).andExpect(status().isOk());

	}

}

```
---
### ContactServiceTest.java
```java
package com.codewithheeren.service;

import com.codewithheeren.entity.Contact;
import com.codewithheeren.repository.ContactRepository;
import org.junit.jupiter.api.Test;
import org.junit.runner.RunWith;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.boot.test.mock.mockito.MockBean;
import org.springframework.test.context.junit4.SpringRunner;
import java.util.Arrays;
import java.util.List;
import java.util.Optional;
import static org.junit.jupiter.api.Assertions.assertEquals;
import static org.mockito.Mockito.*;

@RunWith(SpringRunner.class)
@SpringBootTest
public class ContactServiceTest {

  @MockBean
  ContactRepository contactRepository;

  @Autowired
  ContactService contactService;


    @Test
    public void test_saveContact(){
        Contact contact = new Contact(100L,"Lipsa","Patra","lipsa@gmail.com","123456");
        when(contactRepository.save(contact)).thenReturn(contact);
        Contact actualReturnContact = contactService.saveContact(contact);
        assertEquals(contact,actualReturnContact);
    }
    @Test
    public void test_getContactById(){
        Contact contact = new Contact(100L,"Lipsa","Patra","lipsa@gmail.com","123456");
        when(contactRepository.findById(100L)).thenReturn(Optional.of(contact));
        assertEquals("lipsa@gmail.com",contactService.getContactById(100L).getEmail());

    }
    @Test
    public  void test_getContactList(){
        List<Contact> contactList = Arrays.asList(new Contact(100L,"Lipsa","Patra","lipsa@gmail.com","123456"),
                new Contact(101L,"Robert","Frost","robert@gmail.com","56789"));
        when(contactRepository.findAll()).thenReturn(contactList);
        assertEquals(2,contactService.getContactList().size());
    }


    @Test
    public void test_deleteContact(){
        Contact contact = new Contact(100L,"Lipsa","Patra","lipsa@gmail.com","123456");
        contactService.deleteContact(contact);
        verify(contactRepository,times(1)).delete(contact);
    }
}


```
---
### ContactRepositoryTest.java
```java
package com.codewithheeren.repository;

import static org.junit.Assert.assertEquals;
import java.util.List;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.jdbc.AutoConfigureTestDatabase;
import org.springframework.boot.test.autoconfigure.orm.jpa.DataJpaTest;
import org.springframework.test.context.junit4.SpringRunner;
import com.codewithheeren.entity.Contact;

@RunWith(SpringRunner.class)
@DataJpaTest
@AutoConfigureTestDatabase(replace = AutoConfigureTestDatabase.Replace.NONE)
public class ContactRepositoryTest {
	@Autowired
	private ContactRepository repository;

	@Test
	public void testFindAll() {
		List<com.codewithheeren.entity.Contact> Contacts = repository.findAll();
		assertEquals(2, Contacts.size());
	}

	@Test
	public void testFindOne() {
		Contact Contact = repository.findById(1001L).get();
		assertEquals("heeren", Contact.getFirstName());
	}
}
```
---

