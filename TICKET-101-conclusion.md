# Code Review: TICKET-101


## Task Overview
Please design a decision engine which takes in personal code, loan amount, loan period in months and returns a decision (negative or positive) and the amount. The idea of the decision engine is to determine what would be the maximum sum, regardless of the
person requested loan amount. For example if a person applies for €4000,-, but we determine that we would approve a larger sum then the result should be the maximum sum which we would approve.

Also, in reverse, if a person applies for €4000,- and we would not approve it then we want to return the largest sum which we would approve, for example €2500,-,. If a suitable loan amount is not found within the selected period, the decision engine should also try to find a new suitable period. In real life the solution should connect to external registries and compose a comprehensive user
profile, but for the sake of simplicity this part can be mocked as a hard coded result for certain personal codes.


## Reviewer
- **Name:** Cer-Lyn Luhasaar
- **Date:** 15-04-2024


## Functionality
- **Completeness:**
  - The requirements were partially implemented as the solution does not take the credit score into account, along with the requirement to return the MAXIMUM possible loan sum for the given period.
- **Correctness:**
  - The implemented code was done well with very good readability and structure. The coding style of the intern has stayed consistent throughout the code, making it organized and understandable.
- **Scalability**
  - Although it was easy to use the existing code for modifications, the DecisionEngine class could better adhere to the open-close principle, as calculating credit modifiers and validating inputs is hardcoded. If new rules or conditions need to be added, the class would need to be modified again. To better work with OCP, we might refactor this to use strategies that can be injected at runtime, allowing for easier extensions without modifying existing code.


## Testing
  - Both manual and integrated testing has been completed:
    - Integrated tests covered the base cases of the task well and all tests for DecisionEngine passed successfully.
    - Manual testing was done to ensure the user interface was intuitive and nice to use. Also, to eliminate any miscommunications between the front-end and the back-end, if any. The application was easy to use and displayed all data as expected. 


## Documentation
- **Code Comments:**
  - The code has profound commenting - all the methods have enough explanation to understand their purpose with ease.


## Suggestions for Improvement

FRONT-END
----------

1. State management - as the UI uses sliders to get new values from the customer, it is inefficient to use simple state management like setState() each time a new value comes from the interface. If the user is sliding the slider back and forth it could result in an enormous amount of unnecessary server requests. Instead, a debounce strategy could be implemented. Instead of consistently updating the state, it would only need updating once a certain amount of time has passed without new inputs.
2. API service testing is very basic, could differentiate HTTP status code errors, timeouts, and add a retry mechanism for milder network errors to ensure better UX for the customer.
3. The loan form widget is quite heavy and takes time to go through. It could be made easier to comprehend. E.g., it could be made into two widgets instead of one - loanAmountSlider and loanPeriodSlider, as is done with the national ID field.

BACK-END
----------

1. The tests for DecisionEngineController failed.
Cause: the error message indicates a problem with deserialization when trying to convert the request body to an instance of DecisionRequest.
The root cause of the issue seems to be that the JSON processor used by Spring cannot construct an instance of DecisionRequest because it lacks a default constructor or any other suitable creator method.

Fix: use Lombok's @NoArgsConstructor annotation to generate a no-argument constructor for DecisionRequest class.

2. Console displays an "Incorrect use of ParentDataWidget." exception - should be looked into to see where the issue arises. It may not cause problems now, but could in the long run.
3. One requirement is for the decision engine to return the maximum possible loan sum for the requested period, even if the used input is less. Currently, the engine does not calculate that, it only calculates the sum if it's equal to or less than requested.
4. For people in debt, the engine could also state the reason why no valid loan can be found.
5. BIGGEST SHORTCOMING: the ticket requires implementing a scoring algorithm to calculate the client's credit score. In this case, this solution has not been implemented (to be used for point no. 3).

To fix the shortcoming: 

* First, I created a method calculateCreditScore(), that uses the given formula to calculate the credit score.
* Second, the method was used in calculating the highest valid loan amount. When the credit score == 1, the loan will be approved as requested. If it is > 1, a new highest possible loan amount is calculated. If the score is < 1, the amount will be brought down until it reaches a point of approval.

## Conclusion
The DecisionEngine demonstrates commendable strengths, particularly in its clear and organized code structure, which speaks to the intern's good coding practices. Additionally, the intuitive and user-friendly interface enhances the user experience, showing that the front-end design was thoughtfully crafted. These positive aspects provide a solid base upon which the necessary enhancements can be built. While the DecisionEngine has a strong foundation in terms of coding practices and user interface, it falls short in few critical functional areas. The application does not fully meet the requirement to calculate the maximum possible loan sum and lacks a method for incorporating credit scores, which are essential for lending decisions. Some improvements are needed in both front-end state management and back-end serialization, along with the implementation of a credit scoring algorithm.