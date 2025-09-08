#1. Gherkin elements:
=====================

Gherkin 	Element	Purpose
----------------------------------------------------------------------
Feature		 High-level description of functionality
Rule		 Groups scenarios by business logic
Scenario	 Single test case/example
Scenario Outline Template for scenarios with variable data
Examples	 Data table for Scenario Outline
Background	 Precondition steps for all scenarios
Given/When/Then	 Steps for scenario actions and assertions
And/But		 Additional/alternative steps
----------------------------------------------------------------------


#1. @Given
Used to describe the initial context of the system (the “preconditions”).

@Given("the user is on the login page")
public void userIsOnLoginPage() {
    // code to navigate to login page
}

# 2. @When
Used to describe an action or event.

@When("the user enters valid credentials")
public void userEntersValidCredentials() {
    // code to enter credentials
}

# 3. @Then
Used to describe an expected outcome or result.

@Then("the user should be redirected to the dashboard")
public void userRedirectedToDashboard() {
    // code to check dashboard
}

# 4. @And
Used to chain multiple steps together (can use in place of @Given, @When, or @Then for readability).

@And("the remember me option is selected")
public void rememberMeOptionSelected() {
    // code to select remember me
}

# 5. @But
Used for negative assertions.

@But("the error message is not displayed")
public void errorMessageNotDisplayed() {
    // code to check error message is not shown
}

# 6. Parameterized Step
With parameters for dynamic values:

@When("the user enters username {string} and password {string}")
public void userEntersCredentials(String username, String password) {
    // code to enter parameters
}


#2. Background
================

@smoke @login
Feature: User Login

  Background:
    Given the application is running
    And the user database is seeded

  @valid
  Scenario: User logs in with correct credentials
    Given the user is on the login page
    When the user enters valid credentials
    Then the user should be redirected to the dashboard

  @invalid
  Scenario: User logs in with incorrect credentials
    Given the user is on the login page
    When the user enters invalid credentials
    Then the user should see an error message

 -> @smoke @login tags apply to the entire Feature and all scenarios within it.
 -> Each scenario can have its own tags (e.g., @valid, @invalid), allowing you to filter which scenarios to run.



#3. Tags can be placed above the following Gherkin elements:
===============================================================
	Feature
	Rule
	Scenario (or Example)
	Scenario Outline (or Scenario Template)
	Examples (or Scenarios)
	Background
	Given, When, Then, And, But
	

   -> It is not possible to place tags above Background or steps (Given, When, Then, And and But).


The Rule keyword is used to group related scenarios that illustrate a business rule within a feature.

example:

  @login
  Feature: User Login

  @password
  Rule: Password requirements

  @short
  Scenario: User enters a password that is too short
    Given the user is on the login page
    When the user enters a password "123"
    Then the login should be rejected with the message "Password too short"

  @username
  Rule: Username requirements

  @invalid
  Scenario Outline: Steps will run conditionally if tagged
    Given user is logged in
    When user clicks <link>
    Then user will be logged out

    @mobile
    Examples:
      | link                  |
      | logout link on mobile |

    @desktop
    Examples:
      | link                   |
      | logout link on desktop |
 


Notes:
   -> Place the tag directly above the block you want to tag (Feature, Rule, Scenario, Scenario Outline).
   -> You can add multiple tags (e.g., @login @smoke).
   -> Tags are inherited—if you tag a Rule, all scenarios under it have that tag
   -> You can also tag individual scenarios/examples, or the whole feature, for more granular control
   Rule: Password must be at least 8 characters
    @short
    Example: Short password is rejected
      ...
   -> It is not possible to place tags above Background or steps (Given, When, Then, And and But).


#4. Datatable usage:
======================

Scenario: Adding items to the shopping cart
  Given the shopping cart contains:
    | item      | quantity |
    | Apple     | 2        |
    | Banana    | 3        |
    | Chocolate | 1        |
  When the user checks out
  Then the total price should be calculated correctly


    @Given("the shopping cart contains:")
    public void the_shopping_cart_contains(DataTable dataTable) {
        List<Map<String, String>> items = dataTable.asMaps(String.class, String.class);
        for (Map<String, String> item : items) {
            String name = item.get("item");
            int quantity = Integer.parseInt(item.get("quantity"));
            cart.addItem(name, quantity);
        }
    }

Following collections can be also used as the last parameter in a step definition.

List<List<String>> table
List<Map<String, String>> table
Map<String, String> table
Map<String, List<String>> table
Map<String, Map<String, String>> table


example definitions  
	from source 1: https://github.com/cucumber/cucumber-jvm/tree/main/datatable
	

Feature: Various DataTable usages  

  ##List<List<String>>  Use when you want a simple 2D grid (no headers required).
  Scenario: Table as List of Lists     
    Given the following grid:
      | A | B | C |
      | D | E | F |
      | G | H | I |

  ##List<Map<String, String>> Use when you have a table with headers; each row becomes a map of header to value
  Scenario: Table as List of Maps
    Given the following users exist:
      | name    | email           |
      | Alice   | alice@email.com |
      | Bob     | bob@email.com   |
      | Charlie | charlie@email.com |

  ##Map<String, String> Use for a two-column table where the first column is the key and the second is the value
  Scenario: Table as Map of String to String
    Given the following configuration:
      | url      | https://example.com  |
      | timeout  | 30                   |
      | retries  | 5                    |

  ##Map<String, List<String>> Use for a two-column table where the first column is the key, and the second column is a comma-separated list of values.
  Scenario: Table as Map of String to List of String
    Given the following roles and users:
      | role    | users               |
      | admin   | Alice, Bob          |
      | editor  | Charlie, Dave       |
      | viewer  | Eve, Frank, Grace   |

  ##Map<String, Map<String, String>>  Use for a three-column table; the first column is the top-level key, the second is the sub-key, the third is the value.
  Scenario: Table as Map of String to Map of String to String
    Given the following nested settings:
      | section   | key      | value         |
      | database  | host     | db.example.com|
      | database  | port     | 5432          |
      | server    | port     | 8080          |
      | server    | ssl      | true          |

#5. DataTableType:
========================

@DataTableType 

	Source:  https://github.com/cucumber/cucumber-jvm/tree/main/cucumber-java#data-table-type

@DataTableType annotation in Cucumber (Java) is used to define custom conversions for entries in Gherkin data tables, allowing you to map rows or cells in your data table directly to custom Java objects.


Below is a complete example, including a feature file and Java step definitions using @DataTableType

Featurefile:
---------------

Feature: User registration

  Scenario: Registering multiple users
    Given the following users are registered:
      | name    | email             | age |
      | Alice   | alice@email.com   | 30  |
      | Bob     | bob@email.com     | 25  |



StepDefinition:
------------------

import io.cucumber.java.DataTableType;
import io.cucumber.java.en.Given;
import java.util.List;
import java.util.Map;

public class UserStepDefs {

    static class User {
        String name;
        String email;
        int age;

        public User(String name, String email, int age) {
            this.name = name;
            this.email = email;
            this.age = age;
        }

        // Getters, setters, equals, hashCode, toString, etc.
    }

    @DataTableType
    public User userEntry(Map<String, String> entry) {
        return new User(
            entry.get("name"),
            entry.get("email"),
            Integer.parseInt(entry.get("age"))
        );
    }

    @Given("the following users are registered:")
    public void the_following_users_are_registered(List<User> users) {
        for (User user : users) {
            System.out.println("Registering user: " + user);
            // Registration logic here
        }
    }
}

How It Works
	-> The @DataTableType annotation defines a mapping from a table row (as a Map<String, String>) to your custom User object.
	-> In the step definition, the parameter can be a List<User>, and Cucumber will use the registered converter.
	-> Each row in the Gherkin table is converted to a User instance automatically.




#6. Localization in Gherkin:
============================

	localized name:  https://cucumber.io/docs/gherkin/languages/
	ISO Localized language codes:  https://en.wikipedia.org/wiki/List_of_ISO_639_language_codes


You can define the language of your Gherkin feature file by adding a language header at the very top of the file using the following syntax:

# language: [ISO code]

example:

# language: no

Egenskap: Brukerinnlogging

  Scenario: Brukeren logger inn med gyldig brukernavn og passord
    Gitt at brukeren er på innloggingssiden
    Når brukeren skriver inn gyldig brukernavn og passord
    Så skal brukeren bli logget inn


	-> Combine multiple langage Stepsdefs

	@Given("I have numbers {int} and {int}")
	@Soit("j'ai les nombres {int} et {int}")
	public void i_have_numbers(int a, int b) {
	    // Implementation
	}



#7. Step states:
=====================

Pending:   To define a "Pending" step definition implementation in Gherkin (Cucumber)

example:
import io.cucumber.java.en.Given;
import io.cucumber.java.PendingException;

@Given("I have a pending step")
public void i_have_a_pending_step() {
    throw new PendingException();
}



#8.  Scenario hooks:
===========================

| Hook         | Purpose                                            |
|--------------|----------------------------------------------------|
| `@Before`    | Run before each scenario                           |
| `@After`     | Run after each scenario                            |
| `@BeforeStep`| Run before each step in every scenario             |
| `@AfterStep` | Run after each step in every scenario              |
| `@BeforeAll` | Run once before all scenarios (static method)      |
| `@AfterAll`  | Run once after all scenarios (static method)       |


Example in Java


import io.cucumber.java.Before;
import io.cucumber.java.After;
import io.cucumber.java.BeforeStep;
import io.cucumber.java.AfterStep;
import io.cucumber.java.BeforeAll;
import io.cucumber.java.AfterAll;

public class Hooks {

    @Before
    public void beforeScenario() {
        // Code to run before each scenario
    }

    @After
    public void afterScenario() {
        // Code to run after each scenario
    }


    @BeforeAll
    public static void beforeAll() {
        // Code to run once before all scenarios
    }

    @AfterAll
    public static void afterAll() {
        // Code to run once after all scenarios
    }
}


# Tag-Specific Hooks
You can also make hooks conditional based on tags:
-----------------------------------------------------

@Before("@web")
public void beforeWebScenarios() {
    // Runs before scenarios tagged with @web
}


# Step hooks
----------------

Hook Name      | 	When It Runs
---------------|---------------------------
@BeforeStep    |	Before every step
@AfterStep     |	After every step
-------------------------------------------

Step hooks allow you to execute code before or after each step in a scenario.

In Gherkin, you do not define step hooks directly in the .feature file. Instead, step hooks are a feature of Cucumber (or similar frameworks), and are implemented in your step definition code, not in Gherkin itself.


# Conditional hooks
----------------------

Conditional hooks allow you to execute setup/teardown code only for scenarios (or features) with specific tags, making your test runs more efficient and modular.

example:

import io.cucumber.java.Before;
import io.cucumber.java.After;

public class Hooks {

    // Runs only before scenarios tagged with @web
    @Before("@web")
    public void beforeWebScenario() {
        System.out.println("This runs before @web scenarios");
    }

    // Runs only after scenarios tagged with @api
    @After("@api")
    public void afterApiScenario() {
        System.out.println("This runs after @api scenarios");
    }

    // Runs before scenarios tagged with either @smoke or @regression
    @Before("@smoke or @regression")
    public void beforeSmokeOrRegression() {
        System.out.println("This runs before @smoke or @regression scenarios");
    }
}


# Global hooks
------------------

@BeforeAll
public static void beforeAll() {
    // Runs before all scenarios
}

@AfterAll
public static void afterAll() {
    // Runs after all scenarios
}



# Notes:
===================

->  A feature or scenario can have as many tags as you like. Separate them with spaces:

	@billing @bicker @annoy
	Feature: Verify billing
	....

-> In Scenario Outline, you can use tags on different sets of examples like below:

	Scenario Outline: Steps will run conditionally if tagged
	  Given user is logged in
	  When user clicks <link>
	  Then user will be logged out

	  @mobile
	  Examples:
	    | link                  |
	    | logout link on mobile |

  	  @desktop
	  Examples:
	    | link                   |
	    | logout link on desktop |


->  Tags Hierarchy

Gherkin itself does not support true tag "hierarchies" (like parent/child relationships), you can simulate a hierarchy by strategically applying multiple tags at different levels (feature, scenario, scenario outline, and example row).

example simulating tag hierarchy in gherkin:
-----------------------------------------------

@productA @smoke
Feature: Login functionality

  @ui
  Scenario: Valid user logs in
    Given the user is on the login page
    When they enter correct credentials
    Then they see the dashboard

  @api @regression
  Scenario: API login endpoint returns success
    Given the API is available
    When a POST request is made with correct credentials
    Then the response is 200 OK

  @ui @regression
  Scenario Outline: Login error messages
    Given the user is on the login page
    When they enter invalid "<username>" and "<password>"
    Then the error message "<message>" is shown

    Examples:
      | username | password    | message                | @invalid |
      | user1    | wrongpass   | Invalid credentials!   | @critical |
      |          | password123 | Username required!     |           |


==> @productA and @smoke are applied to the feature, marking all scenarios as part of Product A smoke tests.
==> @ui, @api, @regression, @critical, @invalid provide finer granularity at the scenario and example level.
==> You can filter test execution using these tags, for example:
	@smoke and @ui to run only smoke UI tests.
	@regression and @api for regression API tests.

Visualization of tags hierarchy:

@productA (Feature)
 ├── @ui (Scenario 1)
 ├── @api, @regression (Scenario 2)
 └── @ui, @regression (Scenario Outline)
       └── @critical (Example row)


==>  Running a subset of scenarios

	mvn test -Dcucumber.filter.tags="@smoke and @fast"

	Or an environment variable:
	set CUCUMBER_FILTER_TAGS="@smoke and @fast"
	mvn test

==> Running a subset of scenarios with TestRunner
	
	@CucumberOptions(tags = "@smoke and @fast")

==> Ignoring a subset of scenarios

	@CucumberOptions(tags = "not @smoke")


==>  Tags expressions

@smoke @ui
Scenario: UI smoke test
  Given the user is on the homepage

@regression @api
Scenario: API regression test
  Given the API is up

@smoke @regression
Scenario: Smoke and regression
  Given everything is working

@wip
Scenario: Work in progress
  Given something is being developed


| **Expression**                     | **Description**                                                 | **Scenarios Run**         |
|------------------------------------|-----------------------------------------------------------------|---------------------------|
| `@smoke`                           | Scenarios with the `@smoke` tag                                 | 1 & 3                     |
| `@smoke and @regression`           | Scenarios with **both** `@smoke` **and** `@regression`          | 3                         |
| `@smoke or @regression`            | Scenarios with `@smoke` **or** `@regression`                    | 1, 2, 3                   |
| `not @wip`                         | Scenarios **without** the `@wip` tag                            | 1, 2, 3                   |
| `@smoke and not @ui`               | Scenarios with `@smoke` but **not** `@ui`                       | 3                         |
| `(@smoke or @regression) and not @wip` | Scenarios with `@smoke` **or** `@regression`, but **not** `@wip` | 1, 2, 3                   |
| `@ui or @api`                      | Scenarios with `@ui` **or** `@api`                              | 1, 2                      |



#  @wip tag
===============
The @wip tag in Gherkin stands for Work In Progress. It’s commonly used to mark scenarios or features that are still being developed, 

ex:  Marking an Entire Feature as Work In Progress

@wip
Feature: User Profile Management

  Scenario: Edit profile
    Given the user is logged in
    When the user edits their profile information
    Then the changes should be saved


ex: Marking a Single Scenario as WIP

Feature: Shopping Cart

  @wip
  Scenario: Remove item from cart
    Given I have items in my cart
    When I remove one item
    Then the cart total should update

ex: Marking a Scenario Outline as WIP

Feature: User Login

  @wip
  Scenario Outline: Invalid login attempts
    Given the user is on the login page
    When they enter username "<username>" and password "<password>"
    Then they see an error message

    Examples:
      | username | password  |
      | baduser  | 12345     |
      | admin    | wrongpass |


ex: Marking an Example Row as WIP (Cucumber-jvm only)

Feature: Payment Processing

  Scenario Outline: Card payment
    Given I enter card number "<card>"
    When I submit payment
    Then the payment is "<status>"

    Examples:
      | card         | status    | @wip    |
      | 123456789012 | declined  | @wip    |
      | 987654321098 | approved  |         |



# Summary of TestRunner:
----------------------------

cucumber.ansi-colors.disabled=  # true or false. default: false
cucumber.execution.dry-run=     # true or false. default: false
cucumber.execution.limit=       # number of scenarios to execute (CLI only).
cucumber.execution.order=       # lexical, reverse, random or random:[seed] (CLI only). default: lexical
cucumber.execution.wip=         # true or false. default: false.
cucumber.features=              # comma separated paths to feature files. example: path/to/example.feature, path/to/other.feature
cucumber.filter.name=           # regex. example: .*Hello.*
cucumber.filter.tags=           # tag expression. example: @smoke and not @slow
cucumber.glue=                  # comma separated package names. example: com.example.glue
cucumber.plugin=                # comma separated plugin strings. example: pretty, json:path/to/report.json
cucumber.object-factory=        # object factory class name. example: com.example.MyObjectFactory
cucumber.snippet-type=          # underscore or camelcase. default: underscore

example:  For example, if you are using Maven and want to run a subset of scenarios tagged with @smoke:

mvn test -Dcucumber.filter.tags="@smoke"

In Cucumber BDD step definitions, several variables and objects are accessible, either directly via method parameters, by injection, or through the Cucumber API. Here are the most commonly available variables inside a step definition (StepDef):

#  1. Scenario Object
- Type: `io.cucumber.java.Scenario`
- How: You can inject it as a parameter in any step definition or hook (`@Before`, `@After`).
- Provides:  
  - `scenario.getName()` → Scenario Name  
  - `scenario.getId()` → Scenario ID  
  - `scenario.getStatus()` → Execution status  
  - `scenario.write()` / `scenario.log()` → Write/log output  
  - `scenario.attach()` → Attach files or screenshots  
  - Tags assigned to the scenario

#  2. Tags
- How:  
  - `scenario.getSourceTagNames()` — returns tags for the current scenario

#  Example Step Definition
```
    @Given("user logs in with username {string} and password {string}")
    public void userLogsIn(String username, String password, Scenario scenario) {
        // Print the scenario name
        System.out.println("Scenario Name: " + scenario.getName());

        // Print the scenario tags
        System.out.println("Scenario Tags: " + scenario.getSourceTagNames());

        // Get all tags for this scenario
        Collection<String> tags = scenario.getSourceTagNames();

        // Print each tag
        System.out.println("Scenario Tags:");
        for (String tag : tags) {
            System.out.println(tag);
        }

        // Print the scenario status
        System.out.println("Scenario Status: " + scenario.getStatus());

        // Print step arguments
        System.out.println("Username: " + username);
        System.out.println("Password: " + password);

        // Example: Write a message to the scenario report (visible in Cucumber HTML report)
        scenario.log("Attempting login with username: " + username);

        // Here, add your login logic, e.g. interacting with UI or API:
        // loginPage.login(username, password);

        // Attach screenshot to Cucumber report
	byte[] screenshot = ((TakesScreenshot) driver).getScreenshotAs(OutputType.BYTES);
        scenario.attach(screenshot, "image/png", "Login Screenshot");
        scenario.log("Login attempted with username: " + username);
    }
```

Another Example:

```
    @Given("the user is on the login page")
    public void the_user_is_on_the_login_page(Scenario scenario) {
        // scenario.getId() typically returns: "features/login.feature:10"
        String scenarioId = scenario.getId();
        String[] parts = scenarioId.split(":");
        String lineNumber = parts[parts.length - 1]; // Get the last part as line number
        System.out.println("Scenario starts at line: " + lineNumber);
    }

```

#  3. DataTable
- Type: `io.cucumber.datatable.DataTable`
- How: Pass as a step method parameter if your scenario uses a data table.
- Provides:  
  - Tabular data from the feature file step  
  - Methods to convert to `List`, `Map`, etc.

#  4. Feature Name
- How: Not directly available through the standard API, but you can extract it:
  - From the scenario ID (`scenario.getId()`), which contains the feature file name.
  - Custom parsing or third-party libraries can give direct access.

### find and print the Feature Name using Java reflection (assuming you have the Scenario object injected into your step/hook):

```
import io.cucumber.java.Scenario;

public void printFeatureName(Scenario scenario) {
    try {
        // Get the class of the scenario object
        Class<?> scenarioClass = scenario.getClass();

        // Access the private field "scenario" inside cucumber.runtime.Scenario
        java.lang.reflect.Field scenarioField = scenarioClass.getDeclaredField("scenario");
        scenarioField.setAccessible(true);
        Object cucumberScenario = scenarioField.get(scenario);

        // Access the private field "uri" which has the feature file path
        java.lang.reflect.Field uriField = cucumberScenario.getClass().getDeclaredField("uri");
        uriField.setAccessible(true);
        Object uri = uriField.get(cucumberScenario);

        // The feature file name is the last segment of the URI
        String featureFilePath = uri.toString();
        String featureName = featureFilePath.substring(featureFilePath.lastIndexOf('/') + 1);

        System.out.println("Feature Name: " + featureName);
    } catch (Exception e) {
        e.printStackTrace();
    }
}
```

Sample usage for above:

```
@Given("some step")
public void someStep(Scenario scenario) {
    printFeatureName(scenario);
}
```

### Alternative (No Reflection)

```
String scenarioId = scenario.getId();
String featureName = scenarioId.split(":")[0]; // Typically the feature file path
System.out.println("Feature Name: " + featureName);
```

This is safer and works with Cucumber-JVM >= 4.


#  5. Step Name / Step Text
- How:  
  - You usually have access to the current step’s text via the step definition pattern (the string inside the `@Given`, `@When`, etc).
  - The Scenario API does not directly provide the step text.


```
import io.cucumber.java.en.Given;
import io.cucumber.java.en.When;
import io.cucumber.java.en.Then;
import io.cucumber.java.en.And;
import io.cucumber.java.en.But;

import java.lang.reflect.Method;
import java.lang.annotation.Annotation;

// Utility class to scan and print Cucumber step definitions
public class StepDefinitionScanner {

    // Pass your step definition class here
    public static void printStepNamesAndTexts(Class<?> stepDefClass) {
        for (Method method : stepDefClass.getDeclaredMethods()) {
            for (Annotation annotation : method.getAnnotations()) {
                String stepType = null;
                String stepText = null;

                if (annotation instanceof Given) {
                    stepType = "Given";
                    stepText = ((Given) annotation).value();
                } else if (annotation instanceof When) {
                    stepType = "When";
                    stepText = ((When) annotation).value();
                } else if (annotation instanceof Then) {
                    stepType = "Then";
                    stepText = ((Then) annotation).value();
                } else if (annotation instanceof And) {
                    stepType = "And";
                    stepText = ((And) annotation).value();
                } else if (annotation instanceof But) {
                    stepType = "But";
                    stepText = ((But) annotation).value();
                }

                if (stepType != null) {
                    System.out.println("Step Type: " + stepType);
                    System.out.println("Step Text: " + stepText);
                    System.out.println("Method: " + method.getName());
                    System.out.println("---------------------------");
                }
            }
        }
    }
}
```

```
// Sample usage of above:  
Suppose your stepdef class is UserLoginStepDef
StepDefinitionScanner.printStepNamesAndTexts(UserLoginStepDef.class);

```


#  6. Step Arguments
- How:  
  - As method parameters in your step definition (e.g., numbers, strings, etc. from regex capture groups).

```
    @Given("user logs in with username {string} and password {string}")
    public void userLogsIn(String username, String password, Scenario scenario) {

        try {
            Method method = this.getClass().getMethod("userLogsIn", String.class, String.class, Scenario.class);
            Parameter[] parameters = method.getParameters();

            for (int i = 0; i < parameters.length; i++) {
                System.out.println("Parameter Name: " + parameters[i].getName());
                System.out.println("Parameter Type: " + parameters[i].getType().getName());
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
```





#  Summary Table

| Variable               | How to Access                       | Available in StepDef | Notes                         |
|------------------------|-------------------------------------|----------------------|-------------------------------|
| Scenario Name          | `scenario.getName()`                | Yes                  | Inject Scenario object        |
| Scenario Tags          | `scenario.getSourceTagNames()`      | Yes                  | Inject Scenario object        |
| Scenario ID            | `scenario.getId()`                  | Yes                  | Includes feature file name    |
| Scenario Status        | `scenario.getStatus()`              | Yes                  | Inject Scenario object        |
| DataTable              | Step parameter                      | Yes (if used)        | Only for steps with DataTable |
| Feature Name           | Parse from Scenario ID              | Yes (indirect)       | No direct API                 |
| Step Arguments         | Step method parameters              | Yes                  | Regex capture groups          |


Note:  
- The `Scenario` object is the main gateway to scenario-level info.
- DataTable is only present if your step uses it.
- Feature name requires parsing or custom hooks.

-------------------------------------------------------------------------------------------------------------------------------------



# Scenario injection in StepDef

To inject the Scenario object into your step definition or hook in Cucumber BDD with Selenium Java, you simply add it as a method parameter. Cucumber will inject the current scenario automatically at runtime.

example:

```
import io.cucumber.java.en.Given;
import io.cucumber.java.Scenario;

public class LoginStepDef {
    @Given("user logs in")
    public void user_logs_in(Scenario scenario) {
        // You can use scenario.getName(), scenario.log(), etc.
        System.out.println("Scenario Name: " + scenario.getName());
    }
}
```

# Injecting Scenario in Hooks (@Before, @After)

```
import io.cucumber.java.Before;
import io.cucumber.java.After;
import io.cucumber.java.Scenario;

public class Hooks {

    @Before
    public void beforeScenario(Scenario scenario) {
        System.out.println("Starting Scenario: " + scenario.getName());
    }

    @After
    public void afterScenario(Scenario scenario) {
        System.out.println("Finished Scenario: " + scenario.getName());
    }
}
```


===

How to use the `@Transpose` annotation in **Cucumber BDD API (Java)** with a DataTable.  
The `@Transpose` annotation is used to **transpose rows and columns** in a DataTable so that you can easily map single-column or single-row tables to objects or lists.

---

### 1. **Gherkin Step with DataTable**

```gherkin
Given the user is
    | firstname   | Roberto     |
    | lastname    | Lo Giacco   |
    | nationality | Italian     |
```

- This step provides user information using a **vertical table** (keys in the first column, values in the second).
- Cucumber will pass this table to your Java step definition.

---

### 2. **@DataTableType Annotation**

```java
@DataTableType
public User convert(Map<String, String> entry){
    return new User(
        entry.get("firstname"),
        entry.get("lastname"),
        entry.get("nationality")
    );
}
```

- This method tells Cucumber how to convert a row from a DataTable into a `User` object.
- The `@DataTableType` annotation registers a custom mapping for DataTables.
- Each entry in the table (here, the whole table) is represented as a `Map<String, String>`.
- The method returns a new `User` object using values from the map.

---

### 3. **Step Definition Using @Transpose**

```java
@Given("^the user is$")
public void the_user_is(@Transpose User user) {
    this.user = user;
}
```

- The `@Transpose` annotation **flips the table** so that it can be mapped as a single row (even though the table is vertical).
- Cucumber uses the registered `@DataTableType` to convert the table into a `User` object and injects it into the step.
- You can now use the populated `user` object in your test.

---

### 4. **How It Works Together**

- When the scenario runs, Cucumber sees the vertical user table and the step.
- Because you have a `@DataTableType` method for `User`, and use `@Transpose` in your step, Cucumber:
  1. Transposes the table so it matches the expected mapping.
  2. Converts it to a `Map<String, String>`.
  3. Uses your converter to return a `User` object.
  4. Passes the `User` object to your step method.

---

### **Summary Table**

| Part              | Purpose                                                        |
|-------------------|---------------------------------------------------------------|
| Gherkin table     | Defines user details in vertical format                        |
| @DataTableType    | Converts table map to a User object                            |
| @Transpose        | Flips table orientation to match mapping needs                 |
| Step definition   | Receives `User` object, ready for use in test logic            |

---

**In short:**  
This pattern lets you write readable feature files, and have Cucumber automatically convert tabular data into rich Java objects for your tests, using custom mapping logic and the `@Transpose` annotation for orientation.


===

@DocString annotation
------------------------

Use `@DocStringType` to define a function that transforms a DocString into a custom Java object, making it easy to work with multi-line data in Cucumber steps.

The `@DocStringType` annotation in Cucumber BDD Java is used to define a custom transformation for [DocStrings](https://cucumber.io/docs/cucumber/cucumber-expressions/#doc-string) in Gherkin steps.  
A DocString is a multi-line string literal you can pass to steps.

---

## **1. Gherkin Feature Example**

```gherkin
Feature: DocString Example

  Scenario: Process JSON DocString
    Given the payload is:
      """
      {
        "name": "Roberto",
        "age": 32
      }
      """
```

---

## **2. Step Definition with `@DocStringType`**

```java name=DocStringStepDef.java
import io.cucumber.java.en.Given;
import io.cucumber.java.DocStringType;

public class DocStringStepDef {

    public static class Payload {
        public String name;
        public int age;
    }

    // Custom transformer for JSON DocStrings
    @DocStringType
    public Payload transformPayload(String docString) {
        // Very basic parsing for demonstration; use a proper JSON library in production!
        Payload payload = new Payload();
        docString = docString.replace("{", "").replace("}", "").replace("\"", "").trim();
        String[] lines = docString.split("\n");
        for (String line : lines) {
            String[] parts = line.trim().split(":");
            if (parts[0].trim().equals("name")) {
                payload.name = parts[1].trim();
            } else if (parts[0].trim().equals("age")) {
                payload.age = Integer.parseInt(parts[1].trim());
            }
        }
        return payload;
    }

    @Given("the payload is:")
    public void the_payload_is(Payload payload) {
        System.out.println("Name: " + payload.name);
        System.out.println("Age: " + payload.age);
    }
}
```

---

## **3. How It Works**

- The Gherkin step passes a DocString (multi-line string).
- Cucumber detects the `@DocStringType` transformation and applies it to the DocString before injection.
- The step definition receives a mapped `Payload` object.

---

## **References**
- [Cucumber Docs: DocStringType](https://cucumber.io/docs/cucumber/step-definitions/?lang=java#docstringtype)
- [Cucumber Docs: DocStrings](https://cucumber.io/docs/cucumber/cucumber-expressions/#doc-string)

---
===

Cucumber Expressions:
=============================


**Cucumber Expressions** are a more readable and flexible way to match steps to step definitions compared to regular expressions.

### **Summary Cucumber Expressions**

| Cucumber Expression                | Example Step                  | Parameter Type     |
|------------------------------------|-------------------------------|--------------------|
| `{int}`                            | I have 42 apples              | Integer            |
| `{string}`                         | I eat "banana" fruit          | String             |
| `{word}`                           | 5 apples should be left       | String (word)      |
| `{boolean}`                        | the light is true             | Boolean            |
| `{double}`                         | the price is 39.99            | Double             |
| Custom `{date}`                    | today is 2025-09-08           | LocalDate          |

---
#1. Example:

```java
@Given("the price is {double}")
public void the_price_is(double price) {
    // price: 39.99
}
```
**Matches:**  
`Given the price is 39.99`

---

# 2. **Matching Date Parameters aka ##Custom Parameter Types

Suppose you register a custom parameter type for dates:
```java
@ParameterType(".*")
public LocalDate date(String date) {
    return LocalDate.parse(date);
}

@Given("today is {date}")
public void today_is(LocalDate date) {
    // date: LocalDate object
}
```
**Matches:**  
`Given today is 2025-09-08`

---


# Cucumber Tags expressions
===

**Tag expressions** in Cucumber BDD are used to include or exclude scenarios or features based on their tags when running tests.  
They allow you to filter which scenarios to execute using logical operators.

---

## **Common Tag Expression Examples**

Assume you have the following feature file:

```gherkin
@smoke
Feature: Login

  @regression @important
  Scenario: Valid login
    Given the user enters valid credentials

  @regression
  Scenario: Invalid login
    Given the user enters invalid credentials

  @wip
  Scenario: Unfinished scenario
    Given the user tries something else
```

---

### 1. **Run Scenarios With a Single Tag**


```
@regression
```
**Effect:**  
Runs scenarios tagged with `@regression`.

---

### 2. **Run Scenarios With Multiple Tags (OR)**

```
@important or @wip
```
**Effect:**  
Runs scenarios tagged with either `@important` **or** `@wip`.

---

### 3. **Run Scenarios With All Tags (AND)**

```
@regression and @important
```
**Effect:**  
Runs scenarios tagged with **both** `@regression` **and** `@important`.

---

### 4. **Exclude Scenarios With Tag (NOT)**

```
not @wip
```
**Effect:**  
Runs scenarios **not** tagged with `@wip`.

---

### 5. **Complex Combinations**

```
@regression and not @important
```
**Effect:**  
Runs scenarios tagged with `@regression` **but not** `@important`.

---

## **How to Use Tag Expressions**

- **JUnit Example** (Cucumber-JVM 5+):

```java
import io.cucumber.junit.CucumberOptions;
import io.cucumber.junit.Cucumber;
import org.junit.runner.RunWith;

@RunWith(Cucumber.class)
@CucumberOptions(
    features = "classpath:features",
    tags = "@regression and not @wip"
)
public class RunCucumberTest {}
```

- **Command Line Example:**
```shell
cucumber --tags "@regression and not @wip"
```

---

## **Cucumber Tags Expressions**

| Expression                    | Meaning                                  |
|-------------------------------|------------------------------------------|
| `@regression`                 | Scenarios tagged with `@regression`      |
| `@a or @b`                    | `@a` OR `@b`                             |
| `@a and @b`                   | BOTH `@a` AND `@b`                       |
| `not @wip`                    | NOT tagged with `@wip`                   |
| `@a and not @b`               | `@a` AND NOT `@b`                        |


**References:**
- [Cucumber Tag Expressions Docs](https://cucumber.io/docs/cucumber/api/#tag-expressions)



================

# Running cucumber via Maven project:
 you can run the CLI using the Exec Maven plugin:

mvn exec:java                                  \
    -Dexec.classpathScope=test                 \
    -Dexec.mainClass=io.cucumber.core.cli.Main \
    -Dexec.args="/path/to/your/feature/files --glue hellocucumber --glue anotherpackage"



=====================


https://spring.io/guides


























