

# **Predicting Severity 1 (Reboot and Crash) Incidents Using Machine Learning on Severity 2 Telemetry from Cloud DataCenter Nodes**


### Author: Pablo Rivera


### Executive Summary:

The world’s largest cloud providers continuously roll out new hardware and software technologies—ranging from the latest CPUs and GPUs to advanced networking, storage enhancements, and updated software versions—to compete on efficiency and security. These innovations are integrated into high-capacity servers known as nodes. Typically, 20 nodes are housed within a rack, and 20 racks make up a cluster. Clusters serve as the basic modular units that are deployed into data centers worldwide.

![Nodes Rack Cluster in a Datacenter](NodeRackCluster.jpg)

Despite rigorous pre-deployment testing, these cutting-edge nodes frequently experience operational issues. Critical failures are categorized as Severity 1 (Sev1) incidents—such as system crashes or unexpected reboots—while Severity 2 (Sev2) events indicate degraded performance or functionality. Second-by-second telemetry data on Sev1 and Sev2 events is collected from every node, in every rack, across every data center globally, providing a rich dataset for monitoring, analysis, and predictive modeling.

The purpose of this project is to develop a machine learning model capable of predicting whether a Severity 2 (Sev2) incident is likely to escalate into a Severity 1 (Sev1) incident within a specific node and time window. By leveraging detailed telemetry data collected across global data centers,


### Disclaimer:

The dataset used in this project has been anonymized to protect sensitive infrastructure and organizational information. All identifiers, including Node IDs, Cluster names, and service references, have been altered or generalized to prevent traceability to real systems or entities. This analysis is for research purposes only and does not reflect any specific real-world infrastructure.


### Rationale

Why should anyone care about this question?

The model aims to identify early warning signals, this can allocate valuable resources to execute proactive mitigation strategies instead of reacting after the issue happened. This change of approach ultimately will save resources and improve the whole system reliability by reducing downtime.


#### Research Question

What are you trying to answer?

For each node in each cluster of new technology delivered into a Data Center I want to answer if the Sev2 being reported by the node could lead to a Sev1 System Crash or Reboot. I want to predict with a 70% of confidence if the node will suffer a Sev1


#### Data Sources

What data will you use to answer you question?

The data comes from one of the largest Cloud Provider internal telemetry systems that feed large databases with second to second status of every node in every cluster.  Initially I have 50 clusters that were deployed recently that have a blend of new CPU, new Storage, new Networking capabilities to offer more speed to run new AI workloads.


#### Methodology

What methods are you using to answer the question?

EDA to identify sources and do the heavy lift to find and prepare the data for training

Identifying the Right Data Sources

With over 50 new technology clusters in production across multiple global regions, one of the main challenges was selecting the appropriate data sources. The goal was to find clusters that offered a balanced variety of error types—avoiding extremes of either too much noise or too few issues. It was also critical to ensure:

Consistent configuration of firmware and core software components across clusters, to eliminate version-related variability.

High-quality telemetry data with easy access through internal databases.

At least three months of telemetry history, providing a stable baseline for analysis and making the findings generalizable to similar future deployments.

Feature Selection and Incident Correlation

Once data sources were secured, the next step involved identifying the right features that could contribute to Sev1 events. Starting from more than 200 telemetry signals, I consolidated a representative subset of 60 features based on technical intuition and domain expertise. This included a manual review of which Sev2 incidents consistently preceded Sev1 events, not only in terms of timing but also in terms of logical and technical causality. Establishing a meaningful time window was crucial to correctly associate Sev2 warnings with subsequent Sev1 incidents.

Ensuring Confidentiality

Given the sensitivity of internal infrastructure and organizational structures, all identifiers—including team names, cluster identifiers, and component labels—were anonymized or replaced with fictitious names. This step ensures full compliance with confidentiality policies while preserving the analytical integrity of the dataset.

Filtering for Relevant Training Data

The final step was to filter the dataset to focus only on relevant training samples. This included: Excluding nodes that did not experience any Sev2 incidents. Keeping only those NodeId–Severity combinations where at least one Sev2 event occurred before a Sev1. This filtering allowed the model to learn from meaningful patterns while reducing noise and irrelevant data.


#### Results

What did your research find?


### Class 0: No Sev1



* Precision: 0.74
* Recall 0.97
* F1-score: 0.84


### Class 1: Sev1



* Precision: 0.82
* Recall: 0.28
* F1-score: 0.42


### Combined



* Accuracy : 0.75
* Macro avg.  F1-score: 0.63
* Weighted avg. 0.70

**How the Model is good predicting the node will not escalate to Sev1**



* **Precision 74%** : Only about 26% of the time, when the model predicts noSev1, it's wrong. Another way to say is out of all the times the model predicted **"this node with Sev2 is not going to escalate to Sev1"**, **74% **were actually** correct**
* **Recall 97%** : The model has a very high recall (0.97): correctly identifies nearly all "safe" Sev2s, ie: Sev2 which never become Sev1

**How the Model is good predicting the node will escalate to Sev1**



* **Precision 82%**: High prediction: When predicting a Sev1 risk, it's usually right.
* **Recall: 28%** : Low recall the model is missing 72% of the real Sev1s.

This model has mixed results: For one side this model is very useful for the Support Engineers since it can predict when a Sev2 node will escalate to Sev1, 76% of the cases.  This is a huge improvement from today when we rely on a very constrained human expertise, which doesn’t have enough time to review thousands of nodes.

For the bad side: When this model says “ this node will not escalate to Sev1, it misses 72% of the true escalations.

**Basically this model is good saying: The node in Sev2 is going to escalate to Sev1 but bad saying this node is not going to escalate to Sev1**

**Why is this model missing 72% of the true Sev1s?**

The training was done with imbalanced data:



* Macro Av F1-score = 0.63 is the average F1 score of both classes. This means how fair this model treats each class:  this means the data could be imbalanced
* Weighted Average F1-Score = 0.70 takes the F1-score of each class and weights by their support giving a realistic performance score reflecting class imbalance


#### Next steps

What suggestions do you have for next steps?



1. Resampling using SMOTE, undersampling
2. Feature Engineering to add features that helps to predict the Sev1 the model is missing now


#### Outline of project

* [1test EDA](https://github.com/pabloriveraw/testAssignment/blob/main/1test%20EDA%20-%20Required%20Capstone%20Assignment%2020.1%20Pablo%20Rivera.ipynb)

* [2test Charts](https://github.com/pabloriveraw/testAssignment/blob/main/2test%20Charts%20%26%20Correlations%20Required%20Capstone%20Assignment%2020.1%20Pablo%20Rivera.ipynb)

* [3test TRAINING](https://github.com/pabloriveraw/testAssignment/blob/main/3test%20TRAINING%20%26%20Results%20-%20Required%20Capstone%20Assignment%2020.1%20Pablo%20Rivera.ipynb)

* [4test Bias Correction and Final Improvement](https://github.com/pabloriveraw/testAssignment/blob/main/4%20Bias%20Correction%20and%20Improvement.ipynb)


#### Files that are used in this project

* 1 EDA - Required Capstone Assignment 20.1 Pablo Rivera.ipynb
* 2 Charts & Correlations Required Capstone Assignment 20.1 Pablo Rivera.ipynb
* 3 TRAINING & Results - Required Capstone Assignment 20.1 Pablo Rivera.ipynb
* 4 requirements.txt
* 5 NodeRack.png
* 6 README.md
* 7 Sev1.csv
* 8 Sev1Telemetry.csv

Contact and Further Information: pablo rivera

