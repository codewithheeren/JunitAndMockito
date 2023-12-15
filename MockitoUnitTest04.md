## Mockito testing implementation 
```text
@InjectMocks And @Mock annotation

@Mock and @MockInject both are use to create the mock object of any class.
additionally @MockInject also allows us to inject mocked dependencies(@Mock annotated) in the  mocked object annotated with @MockInject annotation.

@Mock can applicable on interface variable or class variable , in both cases you will 
get mock object.

@MockInject can only give you mock object of class variable, for interface variable you need to 
create implementation object externally (take the reference of usecase2)

@MockInject is very useful when we have an external dependency in the class want to mock. 
We can specify the mock objects to be injected using @Mock annotation.
```
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
### WordDictionary.java

```java
package com.codewithheeren.entity.usecase1;
 
import java.util.HashMap;
import java.util.Map;
 
public class WordDictionary {
	
    private Map<String, String> wordsMap;
 
    public WordDictionary() {
        wordsMap = new HashMap<String, String>();
    }
    public void add(String word, String meaning) {
        wordsMap.put(word, meaning);
    }
    public String getMeaning(String word) {
        return wordsMap.get(word);
    }
}
```
---
### Dictionary.java

```java
package com.codewithheeren.entity.usecase2;

public interface Dictionary {

	public abstract void add(String word, String meaning);
	public abstract String getMeaning(String word);
}

```
---
### DictionaryImpl.java

```java
package com.codewithheeren.entity.usecase2;

import java.util.HashMap;
import java.util.Map;

public class DictionaryImpl implements Dictionary {
	
    private Map<String, String> wordsMap;
 
    public DictionaryImpl() {
        wordsMap = new HashMap<String, String>();
    }
    public void add(String word, String meaning) {
        wordsMap.put(word, meaning);
    }
    public String getMeaning(String word) {
        return wordsMap.get(word);
    }
}

```
---
### MockitoUnitTest04Application.java
```java
package com.codewithheeren.startapp;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.annotation.ComponentScan;
//This implements @Mock and @MockInject annotation.

@SpringBootApplication
@ComponentScan("com.codewithheeren")
public class MockitoUnitTest04Application {

	public static void main(String[] args) {
		SpringApplication.run(MockitoUnitTest04Application.class, args);
	}
}

```
---

### WordDictionaryUsecase1Test.java
```java
package com.codewithheeren.startapp;

import static org.junit.Assert.assertEquals;
import static org.mockito.Mockito.when;
import java.util.Map;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.springframework.test.context.junit4.SpringRunner;
import com.codewithheeren.entity.usecase1.WordDictionary;

@RunWith(SpringRunner.class)
public class WordDictionaryUsecase1Test {
 
	@Mock
	private Map<String, String> wordMap;
 
	@InjectMocks
	private WordDictionary wordDictionary;
 
	@Test
	public void injectMocksTest() {
		
		when(wordMap.get("awesome")).thenReturn("Very Good");
		assertEquals("Very Good", wordDictionary.getMeaning("awesome"));
	}
}

```
---
### WordDictionaryUsecase2Test.java
```java
package com.codewithheeren.startapp;

import static org.junit.Assert.assertEquals;
import static org.mockito.Mockito.when;
import java.util.Map;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.springframework.test.context.junit4.SpringRunner;
import com.codewithheeren.entity.usecase2.Dictionary;
import com.codewithheeren.entity.usecase2.DictionaryImpl;

@RunWith(SpringRunner.class)
public class WordDictionaryUsecase2Test {
 
	@Mock
	private Map<String, String> wordMap;

	@InjectMocks
	private Dictionary dictionary = new DictionaryImpl();

	@Test
	public void injectMocksTest() {
		
		when(wordMap.get("awesome")).thenReturn("Very Good");
		assertEquals("Very Good", dictionary.getMeaning("awesome"));
	}
}

```
---
