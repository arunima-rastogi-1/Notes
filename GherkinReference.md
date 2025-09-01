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







