# API-Automation
API Automation
RestAssuredAutomation/
├── features/
│   └── ApiTests.feature
├── stepdefinitions/
│ └── ApiStepDefinitions.java
├── utils/
│ └── RequestHelper.java
├── runners/
│ └── TestRunner.java

Feature: API Tests using REST Assured
 
  Scenario: Validate GET response fields
    When I send a GET request to the sample endpoint
    Then the response should include path, ip, and all headers
 
  Scenario: Validate POST response with order data
    When I send a POST request with order payload
    Then the response should contain order_id, customer, payment, and items
 
@When("I send a GET request to the sample endpoint")
public void sendGetRequest() {
    response = given().when()
        .get("https://echo.free.beeceptor.com/sample-request?author=beeceptor");
    response.then().statusCode(200);
}
 
@Then("the response should include path, ip, and all headers")
public void validateGetResponse() {
    response.then().body("path", notNullValue())
        .body("ip", notNullValue())
        .headers("Content-Type", containsString("application/json"));
}
 
@When("I send a POST request with order payload")
public void sendPostRequest() {
    String payload = new String(Files.readAllBytes(Paths.get("src/test/resources/payload.json")));
    response = given().header("Content-Type", "application/json")
        .body(payload)
        .post("http://echo.free.beeceptor.com/sample-request?author=beeceptor");
    response.then().statusCode(200);
}
 
@Then("the response should contain order_id, customer, payment, and items")
public void validatePostResponse() {
    response.then().body("order_id", equalTo("12345"))
        .body("customer.name", equalTo("Jane Smith"))
        .body("payment.amount", equalTo(111.97f));
}
 
// === REST Assured Tests in Java with Cucumber ===
// Directory: RestAssuredAutomation/
 
// Feature File - features/ApiTests.feature
Feature: API Tests using REST Assured
 
  Scenario: Validate GET response fields
    When I send a GET request to the sample endpoint
    Then the response should include path, ip, and all headers
 
  Scenario: Validate POST response with order data
    When I send a POST request with order payload
    Then the response should contain order_id, customer, payment, and items
 
// Step Definitions - stepdefinitions/ApiStepDefinitions.java
package stepdefinitions;
 
import io.cucumber.java.en.*;
import io.restassured.response.Response;
import io.restassured.RestAssured;
import java.nio.file.*;
import static io.restassured.RestAssured.*;
import static org.hamcrest.Matchers.*;
 
public class ApiStepDefinitions {
    private Response response;
 
    @When("I send a GET request to the sample endpoint")
    public void sendGetRequest() {
        response = given().when().get("https://echo.free.beeceptor.com/sample-request?author=beeceptor");
        response.then().statusCode(200);
    }
 
    @Then("the response should include path, ip, and all headers")
    public void validateGetResponse() {
        response.then()
                .body("path", notNullValue())
                .body("ip", notNullValue())
                .headers("Content-Type", containsString("application/json"));
    }
 
    @When("I send a POST request with order payload")
    public void sendPostRequest() throws Exception {
        String payload = Files.readString(Paths.get("src/test/resources/payload.json"));
        response = given().header("Content-Type", "application/json")
                .body(payload)
                .post("http://echo.free.beeceptor.com/sample-request?author=beeceptor");
        response.then().statusCode(200);
    }
 
    @Then("the response should contain order_id, customer, payment, and items")
    public void validatePostResponse() {
        response.then()
                .body("order_id", equalTo("12345"))
                .body("customer.name", equalTo("Jane Smith"))
                .body("payment.amount", equalTo(111.97f));
    }
}
 
// Runner - runners/TestRunner.java
package runners;
 
import io.cucumber.junit.Cucumber;
import io.cucumber.junit.CucumberOptions;
import org.junit.runner.RunWith;
 
@RunWith(Cucumber.class)
@CucumberOptions(
        features = "src/test/resources/features",
        glue = "stepdefinitions",
        plugin = {"pretty", "html:target/cucumber-report.html"},
        monochrome = true
)
public class TestRunner {
}
 
// Sample Payload - resources/payload.json
{
  "order_id": "12345",
  "customer": {
    "name": "Jane Smith",
    "email": "janesmith@example.com",
    "phone": "1-987-654-3210",
    "address": {
      "street": "456 Oak Street",
      "city": "Metropolis",
      "state": "NY",
      "zipcode": "10001",
      "country": "USA"
    }
  },
  "items": [
    {
      "product_id": "A101",
      "name": "Wireless Headphones",
      "quantity": 1,
      "price": 79.99
    },
    {
      "product_id": "B202",
      "name": "Smartphone Case",
      "quantity": 2,
      "price": 15.99
    }
  ],
  "payment": {
    "method": "credit_card",
    "transaction_id": "txn_67890",
    "amount": 111.97,
    "currency": "USD"
  },
  "shipping": {
    "method": "standard",
    "cost": 5.99,
    "estimated_delivery": "2024-11-15"
  },
  "order_status": "processing",
  "created_at": "2024-11-07T12:00:00Z"
}
 
