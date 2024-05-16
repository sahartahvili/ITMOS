# Test Case Selection for different Pipelines using AI

This document serves as the comprehensive technical documentation for the "Intelligent Test 
Management System."

## Project overview

This system gives recommendations for CI pipelines and test automation based on input test specifications. The system features an integrated graphical user
interface (GUI) that provides a user-friendly experience for inputting test specifications and 
interacting with the system. The recommendations are visually presented in the GUI and are 
generated based on the models' confidence in their predictions.

![Figure 1: Overview of the implementation of AI system.](Figures/GUI.pdf/)

## Folder structure

The project is organized into the following folders:

**Data cleaning:** contains notebooks for data cleaning.

**Data exploration:** contains notebooks used for data exploration of the dataset from GTMS.

**Figures:** contains figures depicting the system.

**Input data:** contains Excel files used as input and pre-trained weights for the models using FastText.

**Obsolet code:** contains files with obsolete code

**Output data:** contains the data outputted from the different steps in the development process.

**Results:** contains Python scripts and notebooks that validate the developed models.

**Tuning:** contains notebooks that hyperparameter tune the developed models.

**Webportal:** contains the collection of files needed to launch and run the system.

   - **Webportal/Input data:** contains pre-trained weights for models using FastText. 

   - **Webportal/Outout data:** contains the data outputted from the different steps in the development process. These are used as input data to the models in Webportal.

   - **Webportal/Static:** contains static files related to the front-end, such as, CSS and JS files.

   - **Webportal/Templates:** contains HTML files related to the front-end.

   - **Webportal/App.py:** contains the launch configuration of the server and the API to the models.

   - **Webportal/AI-expert_CIPipeline:** contains the models for classifying CI pipeline.

   - **Webportal/AI-expert_Automation:** contains the models for classifying automation.

## Installation guide

**Install packages and dependencies** 

$pip install -r requirments.txt

**Dowload pretrained weights for the FastText model**
Dowload the pretrained weights from FastText at https://fasttext.cc/docs/en/crawl-vectors.html

Add the file to 'Webportal/Input data'

**How to setup models:**

The model for classifying CI pipeline and test automation and intialized and stored in pkl files by 
running the scripts CreateCIModel.py and CreateAutoModel.py. Go to the 'Webportal' in the CMD when initalizing the model. The initation files for the AI-experts can be found in 'Webportal/AI-expert_CIPipeline' and 'Webportal/AI-expert_Automation' 

**How to launch web portal:** 

1. $export FLASK_APP = app.py
2. $flask run

## System architecture

The implementation includes a GUI where the end user can input data and obtain a visualization of the result of the AI-expert. In Figure 1, an overview of the implemented system is illustrated. The user-interactive parts of the solution, seen in sections 4.1 and 4.4 of Figure 1, are implemented as a web portal written in Java-script, HTML, and CSS. The AI-experts, illustrated in sections 4.2 and 4.3 of Figure 1, are implemented in Python.

![Figure 1: Overview of the implementation of AI system.](Figures/GUI.png/)

**1.1. End user input**

A graphical user interface (GUI) has been implemented for the AI-system to be incorporated into the development-operation process of Ericsson. The GUI is integrated into the Test Management System (TMS) used by Ericsson. The GUI prompts the end user for required and optional data when entering test specifications, which is necessary for the TMS.

**a.	Test instruction (end user’s input):** In section (a) of the GUI shown in Figure 3, users can input data related to the test instruction for the feature to be tested. Although the user can enter this information as free-form text, the GUI guides them to provide the information in a structured manner. The inventors have identified four distinct subparts of the test instruction, and the GUI prompts users to enter these in separate text boxes: test setup, pre/post-conditions, test procedure, and pass criteria.

The combination of structured data input and free-form text collection in the GUI allows the AI-expert to learn more efficiently while also providing the flexibility needed to fully express test instructions.

**b.	Categorical end user input:** In section (b) of the GUI shown in Figure 3, end users are required to input categorical data such as quality area, software track, and tagged microservice. To ensure the user-friendliness of the GUI, some categories are selected by the end-user from a predetermined dropdown bar specified by the inventors.  However, some categories have a large set of possible options or are of a dynamic nature where the categories are subject to change over time. For such categories, the user is required to input data in free text without any limitations. To ensure compatibility of the free text input with the categorical nature, comprehensive data processing is implemented

**1.2. AI expert CI pipeline**

**1.2.1. Overview of architecture**

The architecture of the ensemble model, visualized in Figure 2, incorporates a rule-based component along with three sub models. Each sub model specializes in handling a different aspect of the input. The input to the ensemble model is in the form of test specifications created based on the end user's input, as described in section 1.1. Each test specification includes a test instruction in natural language and categorical data. The rule-based component of the ensemble considers the length of the test instructions in the test specification. Sub model 1 focuses on capturing the semantic meaning of the test instructions. Sub model 2 deals with the statistical distribution of words in the test instructions. Sub model 3 utilizes the categorical data in the test specification. The output from each sub model is used as input to the final classification model in the ensemble which outputs the final recommendation.

![Figure 2: Overview of system architecture for CI pipeline.](Figures/CIPipelineOverviewLatest.png/)

**1.2.2. Rule-based component**

The rule-based component of the AI-expert considers the number of characters of each subpart of the test instruction inputted in the test specifications. The rule assigns test specifications to the CI pipeline operation under certain conditions. Specifically, if the number of characters in the pre/post-condition section of the test instruction is greater than 1000 or if the number of characters in the pass criteria section is greater than 800, then the test specification should be assigned to the CI pipeline operation. The rules used in the rule-based component are derived from manually analysing the dataset. Note that the specific threshold for the rules is subject to changes when the solution is implemented for another domain.

**1.2.3. Sub-models**

Semantic classifier for CI pipeline: The input to the semantic classifier consists of the four subparts of the test instruction: test setup, pre/post-conditions, test procedure, and pass criteria, all of which are in natural language form. To capture the semantic and syntactic information of these texts, the subparts are converted into word embeddings using pre-trained FastText embeddings, which are 300-dimensional vectors. The pre-trained word embeddings were trained on the datasets Common Crawl and Wikipedia. The embeddings are fed into individual Random Forest classifiers, one for each subpart of the test instruction. Each specific Random Forest classifier employs the Gini impurity as cost function to make predictions for the corresponding subpart. As a result, the semantic model generates four three-element vectors, one for each subpart, where each element represents the confidence that a test specification belongs to a certain CI pipeline.

Word-distribution classifier: Similar to the semantic classifier, the input for the word-distribution classifier consists of the four subparts of the test instruction: test setup, pre/post-conditions, test procedure, and pass criteria. The word distribution model takes a test instruction as input and outputs four three-element vectors, each representing the probability of the test cases belonging to the three CI pipelines. Each three-element vector is the classification for each part of the test instruction. Below is an overview of the training and classification process of the word-distribution classifier.

Training: During the training of the model, statistics about the training data are collected. The model estimates the conditional probability that a specific word occurs given a CI pipeline. It also calculates the distribution of the CI pipelines in the training data. Both the conditional probability of words and the distribution of CI pipelines are calculated and stored separately for each of the four subparts of the test instruction. The distributions were calculated from the training data using the following equations:

![](Figures/Equation1ofWordDist.png/)

Classification: To classify test specifications, the model calculates the conditional probability of a word bag given a particular CI pipeline. It does this by taking the product of all the conditional probabilities for the words in the word bag given the CI pipeline, which were calculated and stored when the model was trained. Although this method assumes that the words are independent of each other, which is not always true, the impact of this assumption on the accuracy of the model is negligible. The conditional probability of the word bag given a CI pipeline is calculated for all three CI pipelines. The conditional probability of a CI pipelines given a word bag is then given by the quotient of the conditional probability of the word bag given a test level divided by the sum of the conditional probability of the word bag given a CI pipeline, where the sum loops through all possible CI pipelines. These three conditional probabilities are the probabilities that the model outputs. As a result, the word-distribution model generates four three-element vectors, one for each subpart of the test instruction, where each element represents the confidence that a test specification belongs to a certain CI pipeline. 

If a word that has not been seen during the training phase occurs during prediction, the model will assign all probabilities for that word to a very small number. To estimate the probabilities, the following equations are used:

![](Figures/Equation2ofWordDist.png/)

Categorical data classification for CI pipeline: The categorical data sub model leverages the categorical information provided by the end user in the test specifications (as detailed in section 4.1b) to generate recommendations. A one-hot encoder is utilized in this implementation to encode the categorical data. This encoder creates binary columns for each possible value of the categorical data in the test specification. The resulting one-hot encoded categorical data is then fed into a random forest model that uses the Gini impurity as the cost function for making recommendations. The categorical data sub-model output is a three-element array, where each element represents the probability that the test specification belongs to each CI pipeline.

Final classification: The final model in the ensemble takes a 27-element vector as input, which is created by concatenating the outputs from the sub models. This concatenated vector is then fed into a machine learning classifier. This implementation uses a random forest with Gini impurity as a cost function. The output of the final model is a three-element vector representing the model's confidence in assigning the test specification to different CI pipelines.

**1.3. AI-expert automation**

**1.3.1 Overview of architecture**

The AI-expert for automation’s ensemble model, depicted in Figure 3, comprises of two sub-models that specialize in handling different aspects of the input. The input to the ensemble model is in the form of test specifications created based on the information provided by the end user in section 4.1.b. Sub-model 1 captures the semantic meaning of the test specification description, while sub-model 2 utilizes the categorical data in the test specification. The final recommendation is obtained by using the output from each sub-model as input to the final classification model in the ensemble.

<div style="text-align: center;">
  <img src="Figures/AutomationOverviewLatest.png" alt="Figure 3: Overview of system architecture for CI pipeline." width="600">
</div>


**1.3.2 Sub-models**

**Semantic classifier for automation:** The semantic classifier for automation takes in a test specification described in natural language as its input. To capture the underlying meaning of the text, the description is first converted into word embeddings using pre-trained FastText embeddings. These embeddings are then fed into a random forest that utilizes the Gini impurity as the cost function to make predictions about whether the test specification should be automated or not. The output from the semantic classifier for automation is a list with two elements, indicating the level of confidence for automating or not automating the specific test specification.

Categorical data classification for automation: The AI-expert uses categorical data from the test specification, as provided by the end user in the GUI, along with the predicted CI pipeline given by the AI-expert in section 4.2, to generate recommendations for automation. The data is encoded using a one-hot encoder to transform it into one-hot encoded format. Subsequently, the encoded data is fed into a random forest classifier, which utilizes Gini impurity as the cost function. The output from the random forest classifier is a two-element array, where each element represents the AI-expert's confidence level regarding whether a test case should be automated.

**Final classification for automation:** The final model in the ensemble takes a 6-element vector as input, which is created by concatenating the outputs from the two sub models for automation. This concatenated vector is then fed into a support vector machine. This implementation uses a support vector machine with C-SVM as a cost function. The output of the final model is a two-element vector representing the model's confidence in automation the test specification or not.

**1.4. Visualization of recommendation**

In section 1.4 of Figure 2, the AI-based experts’ recommendations are displayed, which are determined based on the test specification inputted by the user. The recommendation of CI pipeline is visualized through a bar chart, where the confidence of the AI expert for each CI pipeline is indicated. The CI pipeline with the highest confidence is selected as the recommended pipeline. However, a test case might be recommended for two pipelines. This situation arises when the output from AI-based expert system gives that the two pipelines with the highest probability are close to equal.

The recommendation and the AI-expert’s confidence regarding automating the test are visualized using a doughnut chart. The final recommendation is given by the highest confidence. 

The ultimate decision of the CI pipeline and automation for a test specification is made by a domain expert. The final decision, as provided by the domain expert, is entered into the web portal and then propagated to the TMS.
