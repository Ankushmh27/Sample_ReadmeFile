# Project Title: Basic Template

## Getting started: 
 This project created using Selenium with Java using Cucumber and testNG framework.

## Pre-requisites

Before you can start performing Java automation testing with Selenium, you need to install the latest Java development environment i.e. JDK 1.8 or higher. We recommend using the latest version.
1. Java jdk-1.8 or higher 
   -> Link: https://www.oracle.com/in/java/technologies/downloads/

2. Integrated development environment(IDE)
    You can download either IntelliJ Idea IDE or Eclipse IDE.
     -> Link for IntelliJ: https://www.jetbrains.com/idea/
     -> Link for Eclipse: https://www.eclipse.org/downloads/
    If Eclipse is chosen, 
Open the IDE and download Cucumber and TestNG Plugins from Help -> Eclipse Marketplace

3. Different browsers need to be installed (if a cross-browser is performed).
    ->On chrome, firefox, and edge can be installed. 

 To open the automation framework, following two ways can be followed:
  - Clone the project repository and import it in the IDE. Or
  - Download the Zip file and set it up in your local workspace.

## Introduction to Framework

This Test Automation Framework is created using Selenium Web Driver with java, TestNG and Cucumber. Which can be used across different web based applications. 
Additionally, it offers the pretty reporting capabilities to generate the different reports like html, json, and also you can generate the pretty report using aventstack extent reports plugins.

In this repository, we encourage the use of Behavior-Driven Development (BDD) with Cucumber and Java to develop automation scripts and testNG annotation to prioritization, parallel testing. And setup the browser parameters and perform cross browser testing by running testNG.xml file.

We provide Step Definitions packaged under /{Project_name}/src/test/java/Stepdef/LoginSteps.java .

These Step Definitions can be customized according to your needs.

Tests are written in the Cucumber framework using the Gherkin syntax.
The features are written in the .feature files in plain english text.
you can find more information at https://cucumber.io/docs/cucumber/

A typical test will have a structure similar to this:
<ins>**Feature File:**<ins>
Feature: Login Feature

  Scenario: User Login with Valid Credentials
    Given user launch application
    When user enter username
    And user enter password
    And user click on Login button
    Then user verify the page title
    Then user close application

Automation test scripts are implemented with page object model (POM) design pattern with pagefactory.
In order to better organize the test code and make it more maintainable, it is recommended to use the Page Object Design Pattern. In the pom you can create a separate page class and test class. This approach reduces code duplication and allows easy updates if the UI changes and reuses the existing code. The Page Object pattern provides a solution by centralizing selectors (css’s, IDs, classes, Xpaths etc) in separate Page.java files, where we can manage them along with the associated methods.

Framework Structure
The automation framework structure is as follows:

Under /{Project_name}/src/main/java, there are some important java classes page object classes and Util classes(such as utils class with common reusable code)

Under /{Project_name}/src/test/java, there are feature files, test runner class, and step definitions.

All the property files like configuration files are present under /{Project_name}/src/test/resources path(configuration.properties, Extent.properties, Extent-config.xml).


<ins>**Reporting:**<ins>

TestNg Reports are generated at /{Project_name}/test-output/ html, emailable report)
Cucumber Reports are generated at /{Project_name}/target/ html, json report)
Extend Reports are generated at /{Project_name}/Extent_reports/All_Reports/(html,pdf,spark html report)

There is a POM.xml file in which all dependencies and plugins are present.
There is also a testng.xml file located at /{Project_name}/testng.xml for cross browser testing such as Chrome, Firefox, Microsoft Edge, etc.

Feature file description:

The feature file is the essential segment of the cucumber tool, which is used to write acceptance steps for automation testing. Acceptance steps generally follow the application specification. A feature file is usually a common file which stores feature, scenarios, and feature description to be tested.

Step Definition description:

Steps definition file stores the mapping between each step of the scenario defined in the feature file with a code of function to be executed. So, now when Cucumber executes a step of the scenario mentioned in the feature file, it scans the step definition file and figures out which function is to be called.

<ins>**Step Definition Class:**<ins>

public class LoginStep extends testNgRunner {
	static LoginPage login ;

	@Given("user launch application")
	public void launchApplication() throws IOException, InterruptedException 
	{
		System.out.println("Launch App");
	}
	
	@Test(priority = 1)
	@When("user enter username")
	public void enterUsername() throws IOException, InterruptedException 
	{
		login = new LoginPage(driver);
		login.inputUsername(utils.configReader("username"));
		Thread.sleep(2000);
	}

	@Test(priority = 2)
	@When("user enter password")
	public void enterPassword() throws IOException, InterruptedException
	{
		login.inputPassword(utils.configReader("password"));
		Thread.sleep(2000);
	}
	@Test(priority = 3)
	@When("user click on Login button")
	public void clickLoginBtn() throws IOException, InterruptedException 
	{
		login.clickLoginButton();
		Thread.sleep(2000);
	}
	
	@Test(priority = 4)
	@Then("user verify the page title") 
	public void verifyPageTitle() throws IOException, InterruptedException 
	{
		String actualTitle = driver.getTitle();
		System.out.println("Actual Page Title :"+ actualTitle);
		String expectedTitle= "OrangeHRMHome";
		SoftAssert soft=new SoftAssert();
		soft.assertEquals(actualTitle, expectedTitle);
		soft.assertAll();
		Thread.sleep(2000);
	}
}




<ins>**Test Runner Class:**<ins>

In Cucumber, the test runner file executes the Cucumber feature files and coordinates the steps defined in those feature files with the corresponding step definitions. In short, runner class maps the feature file steps to its corresponding step definition code.

Glimpse of runner class:

	// The CucumberOptions annotation is used to configure Cucumber test execution
	@CucumberOptions(

			// Specify the location of your feature files and package where your step definitions are located
			features = {"src/test/java/features"},
			glue = {"Stepdef"},

			// Configure the Cucumber plugin options
			plugin = {"pretty", "json:target/json-report/cucumber.json",
				"com.aventstack.extentreports.cucumber.adapter.ExtentCucumberAdapter:",
			},

			// Display the Cucumber output in monochrome (removes color codes)
			monochrome = true,
			publish = true
	)

	public class testNgRunner extends AbstractTestNGCucumberTests {

	// Here you can setup the browser by TestNG annotations and add browser parameter in      testng.xml file to perform the cross browser testing

	public static WebDriver driver;
		@BeforeClass     //Run only once before @Tests methods
		@Parameters("browser")   // by setting parameters select or run on multiple browser
		public void setUp(String browser) throws IOException {
			if (browser.equalsIgnoreCase("chrome")) {
				System.setProperty("webdriver.chrome.silentOutput", "true");
				WebDriverManager.chromedriver().setup();
				ChromeOptions options = new ChromeOptions();
				options.addArguments("--disable-extensions");
				options.addArguments("--deleteAll-Cookies");
				driver = new ChromeDriver(options);         
			} else if (browser.equalsIgnoreCase("edge")) {
				WebDriverManager.edgedriver().setup();
				EdgeOptions options= new EdgeOptions();
				options.addArguments("headless");
				driver = new EdgeDriver(options);
			} else if (browser.equalsIgnoreCase("firefox")) {
				WebDriverManager.firefoxdriver().setup();
				driver = new FirefoxDriver();

			} else if (browser.equalsIgnoreCase("opera")) {
				WebDriverManager.operadriver().setup();
				driver = new OperaDriver();
			} else {
				System.out.println("This Browser is not found in your system.");
			}
			driver.get(utils.configReader("url"));
			driver.manage().window().maximize();
			driver.manage().timeouts().implicitlyWait(Duration.ofSeconds(12));
		}
		@AfterClass     // After all @Test method executed then this annotation close browser
		public void tearDown() {
			driver.close();
			driver.quit();}
	}

You can run classes, tests parallel by adding the location of the class or test. following ways can be followed:

Run the testng.xml file using TestNG. Single and cross browser testing can be performed using this file.
Run through the command line. Open the cmd terminal where the pom.xml file is located. Write and run the TestRunner class using TestNG.

<ins>**Test Data:**<ins>
[Describe how to manage test data, if applicable]
[Include any sample test data files or templates or just write here]

<ins>**POM File:**<ins> 

-> POM is basically a Project Object Model. It is a fundamental unit of work in Maven. It is an XML file that resides in the base directory of the project as pom.xml. The POM contains information about the project and various configuration details used by Maven to build the project(s).

-> Some of the configuration that can be specified in the POM are:

Project Version
Build profiles
Plugins
Maven dependencies

-> POM Example:
	<?xml version="1.0" encoding="UTF-8"?>
	<project xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
		 xmlns="http://maven.apache.org/POM/4.0.0"
		 xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>

	<groupId>org.example</groupId>
	<artifactId>Basic_Template</artifactId>
	<version>1.0-SNAPSHOT</version>
	</project>

-> Note:

There should be a single POM file for each project.
Maven dependencies and plugins can vary as per the project use and requirements.
All POM files require the project element and three mandatory fields: groupId, artifactId and version.
-> Important plugins which are included are maven-cucumber-reporting , maven-compiler-plugin and maven-surefire-plugin.

-> Some important maven dependencies which are included in the POM file are as follows:

- webdrivermanager
- testng
- cucumber-java
- cucumber-testng
- selenium-java
- selenium-support
- extentreports
- Extentreports-cucumber7-adapter

<ins>**Contact:**<ins>

QA Manager: Nishit Sheth
QA Engineer: Ankush Mhala

