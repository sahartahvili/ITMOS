# ITMOS

## Project overview

This system gives recommendations for CI configuration and test automation based on input test specifications. The system features an integrated graphical user interface (GUI) that provides a user-friendly experience for inputting test specifications and  interacting with the system. The recommendations are visually presented in the GUI and are generated based on the models' confidence in their predictions.

![Figure 1: Overview of the implementation of AI system.](Figures/GUI.pdf/)


## Data Description
This synthetic dataset contains information related to tests conducted on test beds in a telecom industry. The dataset includes various attributes such as test setup, procedures, criteria, conditions, configurations, and more.

### Columns
1. **id**: Unique identifier for each test.
2. **Creation Date**: Date when the test was created.
3. **Status**: Current status of the test.
4. **Last Updated**: Date of the last update to the test.
5. **Test Setup**: Description of the test setup.
6. **Test Procedure**: Detailed procedure for conducting the test.
7. **Pass Criteria**: Criteria for determining the test pass.
8. **Pre Post Condition**: Conditions before and after the test.
9. **Test Instruction Text Default**: Default test instruction text.
10. **Test Configuration**: Configuration details for the test.
11. **SW Track**: Software track associated with the test.
12. **Tagged Microservice**: Microservice tagged with the test.
13. **Test Duration (weeks)**: Duration of the test in weeks.
14. **CHS Verification**: Verification status of CHS (Characteristics Specifications).
15. **EPIC**: EPIC associated with the test.
16. **Test Framework Text Default**: Default test framework text.
17. **Test Framework**: Framework used for conducting the test.
18. **Context_name**: Name of the test context.
19. **CI Configuration**: Continuous Integration Configuration value for each test (Target Value).
20. **Automation**: Value telling wheter the test needs to be automated or not (Target Value).
