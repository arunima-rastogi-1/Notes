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











