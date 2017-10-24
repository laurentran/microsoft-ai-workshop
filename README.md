# Azure Machine Learning

## Titanic Survival

Today, we’re going to create a model to predict who would have lived (and died) during the sinking of the titanic. 

### Before we start

1. What is the dependent variable?  (The variable we are trying to predict)

2. What are some independent variables?  (Predictive characteristics or "features")

3. What type of algorithm do we want to use?

### Get the data

1. Navigate to [Kaggle.com](https://www.kaggle.com/c/titanic/data) to download the Titanic data set.  

2. Next to "train.csv", click the Download button. Save the file, and open it in Excel.

    ![Download data](/images/download.JPG)
    ![Excel](/images/excel.jpg)

### Feature Engineering

1. Look at the rows in your CSV file.  Each row is a **training example**, representing a single passenger from the Titanic.  We have 890 rows, which means we have data about 890 passengers on the Titanic.

2. Now take a look at the columns.  Each column represents a **feature**, and our features capture characteristics about the passengers in our data set.  Features are critical because they hold predictive power that will help our algorithm **learn** from the data. 

3. Let's add a column to create another feature.  We'll try **Age*Class**.  

    a.  Enter "Age*Class" in cell M1.
    <br>
        ![Age*Class](/images/age-class.png)

    b.  Enter the following formula into cell M2 to multiply the **age** feature by the **class** feature:  **=F2*C2**
    <br>
        ![Age*Class Formula](/images/age-class-formula.JPG)

    c.  Hover over the bottom right corner of the cell and double click.  This will populate the formula down the entire column.
    <br>
        ![Populate Formula](/images/populate.JPG)

    d.  Adding the **Age*Class** feature is just one example of creating a new feature.  This is a great time to add other features that you think might help your model predict survivors better!  Maybe the deck where their cabin was located.  Or maybe the size of their family.  Or perhaps their title.  Etc.

    A couple of hints:
    - **=LEFT(\<cell\>,1)**  will return the first character in a cell
    - Read on the [Kaggle site](https://www.kaggle.com/c/titanic/data) for tips (e.g. [Feature Engineering](https://triangleinequality.wordpress.com/2013/09/08/basic-feature-engineering-with-the-titanic-data/)) and to better understand the data!
    
4.  When you think you have enough features, save changes to your CSV file.

### Modeling

1.  We're now going to upload your saved CSV file as a dataset in Azure Machine Learning.

2.  Navigate to the [Azure Machine Learning Studio](http://studio.azureml.net) and log in.  (If you run into any issues, try opening an inprivate browser session.)

3.  On the bottom left, click the plus sign, and choose **DATASET** -> **FROM LOCAL FILE**.  
<br>
    ![New Item](/images/new.png)
    ![New Dataset](/images/new-dataset.jpg)

4.  Upload your new dataset, naming it appropriately, for example: "Titanic Dataset".  Hit the check mark when you've filled out the form to upload the data.
<br>
    ![Dataset Upload](/images/dataset-upload.JPG)

5.  Next we'll create a new experiment from the same + symbol in the bottom left. Choose **Blank experiment** and rename it appropriately. 
<br>
    ![New Experiment](/images/new-experiment.png)

6.  Drag your newly added dataset to the Azure ML experiment canvas.  Your dataset is on the left pane under **Saved Datasets**
<br>
    ![My Datasets](/images/my-datasets.png)

7.  We need to choose the features that we want to include in our model.  Add the **Select Columns in Dataset** module.
<br>
    ![Select Columns](/images/select-columns.JPG)

    With the module highlighted, click **Launch Column Selector** on the right pane
<br>
    ![Launch Column Selector](/images/select-columns-options.JPG)

    There are certain columns we want to exclude.  Choose the **WITH RULES** pane on the left, choose Begin With **ALL COLUMNS** and select **Exclude** from the first drop down.  Exclude the columns shown below.  Feel free to exclude others as well, leaving the columns that you think will be predictive of survival.

    Why would we want to exclude these columns?
<br>
    ![Select Columns](/images/exclude-columns.JPG)

8.  Next, we need to clean out pieces of the dataset where we have missing data.  Drag in the **Clean Missing Data** module, and configure the options as shown below.
<br>
    ![Clean Missing Data](/images/clean-data.JPG)

9.  Click the **Run** button at the bottom pane to ensure you get green check marks at each module and don't have any errors.
<br>
    ![Run](/images/run.JPG)

10.  The **Edit Metadata** module allows you to modify the data type of your features.  In our dataset, for example, **Pclass** is numerical, but we actually want Azure ML to treat the data a categorical.  Any ideas why?

     Drag in the **Edit Metadata** module, and specify the options below.
<br>
    ![Edit Metadata](/images/edit-metadata.JPG)

11.  We've finished preprocessing the data and are now ready to split our data into a training set and a test set.  The **training set** is the data that our algorithm will learn from.  The **test set** is the data we withhold so that we can test how well our model performs on new data is has not seen yet.

     Drag in the **Split Data** module, and configure it with 70% of the data for training (leaving 30% for testing).
<br>
    ![Split Data](/images/split.JPG)

     Turning on **Stratified Split** will tell Azure ML to evenly split data according to our column of choice.  We want to evenly split the data according to the **label** that we want to predict: **Survived**.  

12.  Next use the **Train Model** module, so drag that out as well, connecting the training dataset (left output of **Split Data**) into **Train Model**. Don't forget to select the column that we are trying to predict (the dependent variable).
<br>
    ![Train](/images/train.JPG)

13.  We know we're trying to solve a ____________________ problem, so we will add an algorithm from the _______________ drop down. Any will do to start!  Add it into your experiment.
<br>
    ![Binary Classifier](/images/binary-classification.JPG)

14.  Add a **Score Model** module and connect the **Train Model** output to the top left node. Connect your test data, the 30% split, to the top right node of the **Score Model** module.
<br>
    ![Score Model](/images/score.JPG)

15.  Now recreate this structure (steps 12-14), reusing the data from the **Split Module** to train and score using another classification algorithm.

16.  Finally, let's see how our two models perform!  Take the output of both **Score Model** modules and connect them to the two input nodes in the **Evaluate Model** module. 

17.  At this point your canvas should look similar to the one below.
<br>
    ![Experiment](/images/experiment.JPG)

18.  Press the **Run** button, and wait for the model to finish running. Once it is finished, click the output node of the **Evaluate Model** module, and click visualize to see how accurate your model is! Keep trying other algorithms until you are happy with your results.  

### Additional Resources

- To learn how to deploy your model to an API endpoint, see [Deploy Web Service](https://docs.microsoft.com/en-us/azure/machine-learning/studio/walkthrough-5-publish-web-service)
- [Azure Machine Learning Documentation](https://docs.microsoft.com/en-us/azure/machine-learning/studio/)