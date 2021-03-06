# KubeCon 2020 Demo (renewable-energy)

This repository is built as an example of a typical data science project. Its intended use is to demonstrate the data scientist's workflow when transforming a machine learning project into a [Kubeflow pipeline](https://www.kubeflow.org/docs/pipelines/overview/pipelines-overview/). The project is open-sourced and demoed publicly as part of the following presentation at KubeCon 2020: [https://sched.co/ekBt](https://sched.co/ekBt)

## Problem Statement
The state of California has a [goal](https://www.ca.gov/archive/gov39/wp-content/uploads/2018/09/9.10.18-Executive-Order.pdf) to generate 60% of its electrical energy from renewable sources by 2030 and become carbon-neutral by 2045. The California Independent System Operator (CAISO) provides daily [reports](http://www.caiso.com/market/Pages/ReportsBulletins/RenewablesReporting.aspx) where it publicly discloses energy generation data for the state's grid broken down by source and recorded on an hourly basis.

With this project we model the growth of renewable energy in the state of California using the available historical data, then project future growth and compare the model predictions with the state's renewable energy goals. <big>Are these goals achievable?</big>



```
   Disclaimer: 
      The CAISO reports use raw data and are not intended
      to be used as the basis for operational or financial decisions.
```

## Solution Approach
The solution approach is to break down the problem and solve the following subproblems, or steps. The steps can then be executed sequentially or in parallel depending on their dependencies.

### Data wrangling
Download all available data from the [bulletins](http://content.caiso.com/green/renewrpt/files.html) published on CAISO's website. Combine all data into a single table and save it locally as [data.csv](data/data.csv).

### Data preprocessing
Assess data quality and perform clensing, formatting, imputation, and feature engineering as needed. Save the resulting preprocessed data set as [preprocessed_data.csv](preprocessed_data.csv). Perform some initial analysis and [visualization](images/history.png) of the data to better understand its content.
<img src="images/history.png">

### Dataset splitting
Split the data into a training and testing set, producing files [train_data.csv](data/train_data.csv) and [test_data.csv](data/test_data.csv) respectively.

### Modeling technique selection
Build models using different modeling techniques and the training dataset, validate the models and compute model quality metrics based on the testing dataset. Select the technique which produces the most accurate model. In this example we use the [Facebook Prophet](https://facebook.github.io/prophet/) time series forecasting procedure as well as the [Scikit-learn Random Forest Regressor](https://scikit-learn.org/stable/modules/generated/sklearn.ensemble.RandomForestRegressor.html) to model the percentage of renewable compared to total energy over time. The final selection of modeling technique is done automatically based on the root mean square error (RMSE) calculated against the testing dataset. For comparison, we also build a linear model by drawing a trend line through the training set data and calculate its RMSE by using the trend line and the actual values from the testing set. Other modeling techniques have also been implemented in the code and may be used to enhance this project in the future. 

### Model building and prediction
Train a model using the selected best modeling technique and the full dataset, then [predict](data/prediction-prophet.csv) renewable energy generation for the next 30 years. 
<img src="images/prediction-prophet.png">
For comparison, we also use the full dataset to [forecast](data/prediction-linear.csv) future values using the Linear model.
<img src="images/prediction-linear.png"> 

### Visualizing results
Visualize [predictions](data/prediction-best.csv). Compare forecasted values for years 2030 and 2045 to California's goals of 60% and 100% renewable energy production respectively. 
<img src="images/prediction-best.png">


The model's predictions show that California's carbon neutrality goal is ambitious, and there is a large margin of error, especially when we look further into the future. One way to interpret this result is that whether the goal is achieved or not will depend on the advances made every day while working towards carbon neutrality.

## Data Science Workflow

Initially, the data sicentist develops the project using Python locally and pushes it to a Git [repository](https://github.com/iankoulski/renewable-energy). Then the data scienist logs on to a Kubeflow instance on the cloud, spins up a shared JupyterLab server and clones the code. As instructed by an MLOps expert, the data scientist makes a quick modification in the main file of the project, adding an import of the Kale SDK and decorating its functions using @pipeline and @step. 
<image src="images/decorators.png">

This converts the code from a regular python project to a Kubeflow pipeline. <br/>

The pipeline can still run locally like before by executing:
```
   python3 main.py
```
and it can also run in Kubeflow by executing:
```
   python3 main.py --kfp
```

<image src="images/kale-kfp.png">

## Conclusion
Following a simple worklow, the data scientist is able to transform a regular machine learning project into an automated Kubeflow pipeline. The pipeline can be run manually or scheduled to run periodically as needed. Through the use of tools like Kale and Kubeflow, running on Kubernetes, this demonstration uses a real-world example to show the power of democratizing and accelerating pipeline orchestration. Even though the focus of this example is on machine learning, the same approach applies for many other domains.

<image src="images/pipeline.png">


## References

* California Executive Order B-55-18 to achieve carbon neutrality: [https://www.ca.gov/archive/gov39/wp-content/uploads/2018/09/9.10.18-Executive-Order.pdf](https://www.ca.gov/archive/gov39/wp-content/uploads/2018/09/9.10.18-Executive-Order.pdf)
* Article: [https://www.power-technology.com/features/california-renewables-on-the-frontline](https://www.power-technology.com/features/california-renewables-on-the-frontline)
* CAISO renewables reports: [http://www.caiso.com/market/Pages/ReportsBulletins/RenewablesReporting.aspx](http://www.caiso.com/market/Pages/ReportsBulletins/RenewablesReporting.aspx)
* CAISO daily bulletin: [http://content.caiso.com/green/renewrpt/files.html](http://content.caiso.com/green/renewrpt/files.html)
* Facebook Prophet: [https://facebook.github.io/prophet/](https://facebook.github.io/prophet/)
* Scikit-learn Random Forest Regressor: [https://scikit-learn.org/stable/modules/generated/sklearn.ensemble.RandomForestRegressor.html](https://scikit-learn.org/stable/modules/generated/sklearn.ensemble.RandomForestRegressor.html) 
* Kubeflow:  [https://kubeflow.org](https://kubeflow.org)
* Kubeflow Pipelines: [https://www.kubeflow.org/docs/pipelines/overview/pipelines-overview/](https://www.kubeflow.org/docs/pipelines/overview/pipelines-overview/)
* Kale: [https://github.com/kubeflow-kale/kale](https://github.com/kubeflow-kale/kale)
* Kale SDK: [https://docs.arrikto.com/user_guides/kale.html](https://docs.arrikto.com/user_guides/kale.html)
* Arrikto: [https://www.arrikto.com/](https://www.arrikto.com/)