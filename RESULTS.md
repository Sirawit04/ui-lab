# UI lab results

Tool parts that I chose: Part 3 (Robot Framework) and Part 4 (Cypress)



## Part 0
- Step 0.1, Chrome version: Google Chrome 157.0.8103.78 (Official Build) (64-bit), Windows 11
- Step 0.3, ChromeDriver version: 157.0.8103.78
- Step 0.4, locator of Pizza, Please!: name:pizza-please
- Step 0.4, locator of Excluded tools: css:select
- Step 0.4, locator of Must be vegetarian: id:default-checkbox
- Step 0.4, locator of Custom Pizza Name: css:input#pizza-name
- Step 0.5, pizza name, tool and calories of the run by hand: Lab Pizza 20261124-185500, Pizza cutter, 575 calories per slice

| Step | Actual result | Pass or fail |
|------|---------------|--------------|
| 1 | Home page opened, heading Looking to break out of your pizza routine? shown | Pass |
| 2 | Advanced turned on, the Max Calories box, the Excluded tools list, Must be vegetarian and Custom Pizza Name appeared | Pass |
| 3 | Max Calories changed from 1000 to 600, Knife selected, Must be vegetarian ticked, name Lab Pizza 20261124-185500 typed | Pass |
| 4 | Answer showed name Lab Pizza 20261124-185500, dough Thin, 8 ingredients, tool Pizza cutter (not Knife), 575 calories per slice (not above 600) | Pass |
| 5 | Love it! without logging in showed Please log in first. | Pass |
| 6 | Admin page, user and password were admin, Sign in showed Latest pizza recommendations | Pass |
| 7 | The list had the line Lab Pizza 20261124-185500 (tool=Pizza cutter, ingredients_number=8), the same tool as in step 4. It was the second line, below a pizza of another user | Pass |

## Part 3: Robot Framework
- robot --version: Robot Framework 7.5.1 (Python 3.13.18 on linux), with robotframework-seleniumlibrary 6.9.0. The command ends with exit code 251, which is normal, so the workflow runs it with || true.
- Step 3.2, failure message for the wrong title: Title should have been 'Web Form' but was 'Web form'. (1 test, 0 passed, 1 failed)
- Step 3.8, run 1, result, elapsed time and pizza name: PASS (1 test, 1 passed, 0 failed), elapsed 3.29 s, Lab Pizza 20261124-065321 (tool Pizza cutter)
- Step 3.8, run 2, result, elapsed time and pizza name: PASS (1 test, 1 passed, 0 failed), elapsed 3.06 s, Lab Pizza 20261124-065325 (tool Scissors, 525 calories per slice)

The two runs gave different tools and calories, which is why the test checks that the answer keeps the restrictions and does not expect a fixed pizza.

## Part 4: Cypress
- node --version, npm --version and npx cypress version: v24.22.3, 11.19.1, Cypress 16.3.0 (package.json asks for ^16.2.1)
- Step 4.3, failure message for the wrong title: Timed out retrying after 10000ms, + expected - actual, -'Web form' +'Web Form', at failing.cy.js:6:15 (0 passing, 1 failing)
- Step 4.6, tool and calories in the answer of POST /api/pizza: Scissors and 575 calories per slice (the last run; the page shows the same, see qp01-step4-recommendation.png)
- Step 4.7, the line of the admin list that shows your pizza: Lab Pizza 20261124-065657 (tool=Scissors, ingredients_number=6)
- Step 4.8, run 1, result, duration and pizza name: All specs passed, 2 tests, 2 passing, duration 00:08. The pizza name of run 1 was not printed in the log, and the screenshots were replaced by run 2.
- Step 4.8, run 2, result, duration and pizza name: All specs passed, 2 tests, 2 passing, duration 00:08 (pizza-scenario.cy.js 00:05, web-form.cy.js 00:02), pizza Lab Pizza 20261124-065657
- Step 4.9 (optional), browser and result: not done; both runs used Chrome (npx cypress run --browser chrome)

## Part B
1. The three number boxes of the page share one id, floating_filled (the Console showed 3 elements for $$("#floating_filled").length), so an id cannot say which box is Max Calories per Slice. The tests find the label text and take the input next to it, xpath://label[contains(.,'Max Calories')]/../input in Robot and .siblings('input') in Cypress. I would give each box its own id, for example max-calories, min-toppings and max-toppings (or a data-testid). A tester then writes one short locator that does not depend on the label wording or on the layout of the page, and a failure points to one element.
2. The check compares the tool in the admin list with the tool in the recommendation, so it is a consistency check: two parts of the system must agree. It would catch a fault where the server stores a different tool than it showed, for example the admin list saves the tool of the previous pizza or a default tool. In run 2 the list said tool=Scissors and the answer said Scissors, so the check passed. It would also fail if the pizza never reached the list, because the line with the name would not exist.
3. I would check the field vegetarian of the answer of POST /api/pizza, which in Cypress is cy.wait('@pizza') followed by .its('response.body.vegetarian').should('eq', true). Robot would need the same value from another source, because SeleniumLibrary cannot see the network traffic. A stronger check would read the ingredient names of the answer and compare them with a list of ingredients that I know are not vegetarian. That list would have to come from outside the page, for example from the specification, because the page's own flag cannot find a fault in itself.
4. Robot waited with SeleniumLibrary keywords such as Wait Until Element Is Visible and Wait Until Element Contains, with the library timeout of 10 s, and a run took 3.29 s and 3.06 s. Cypress waited by retrying each command for up to 10 s and by cy.wait on the intercepted request, and the specs took 00:05 and 00:02. QP-01 in Robot needed a resource file with about ten keywords plus a scenario file, and in Cypress one spec plus one custom command, and Cypress needed no driver path while Robot needed the CHROMEDRIVER variable. The Robot log.html shows every keyword with its screenshot, and Cypress gave an mp4 video and three screenshots; I found the Robot log easier to read for a failed step and the video easier to see what the page did.
5. For Project 2 I would choose Cypress. First, cy.intercept with cy.wait waits for the real response of the server instead of a guess, and it also lets the test compare the answer of the API with the page, which gave the check in question 3. Second, one npm install and one npx cypress run command made the whole run, with video and screenshots saved without more setup, while Robot needed a driver path and its own environment variable. The cost is that Cypress runs the tests in a browser only, so it is not a fit for anything outside a web page.
6. If QuickPizza changed the locator of the calories box, I would change one line in robot/resources/quickpizza.resource, the variable MAX CALORIES in the Variables section, because the keywords Turn On Advanced and Fill In The Pizza Request both use that variable. The file pizza_scenario.robot only calls keywords such as Fill In The Pizza Request 600 Knife with a name, so it never contains a locator and stays unchanged. The scenario then still reads like the test case table, and the repair is one line instead of a search through every test.
7. Cypress clears the cookies and the page before each test, so each test starts on an empty page with no login and no values. In QP-01 steps 6 and 7 need what steps 1 to 5 made: the pizza name kept in the const pizzaName and the tool from the recommendation, and step 5 relies on being logged out. If steps 6 and 7 were a second test, the const would not be available, there would be no pizza made in that test to find in the admin list, and the test would start on a blank page and have to create the pizza again, which gives a different pizza and puts more entries on the shared site. Keeping QP-01 as a single test keeps the 7 steps of the test case as one flow.
