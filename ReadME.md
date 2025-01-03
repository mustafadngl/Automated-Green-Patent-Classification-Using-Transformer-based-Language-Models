This is a model training project in order to specify green data. According, to detailed description test bulk data on granted patents BERT based LLM model has been trained in order to classify later patent data. Data gathered from: https://patentsview.org/download/detail_desc_text
"workspace.ipynb" folder may lead approach on the project, additionally.

### In order to process any python file in the project follow the process below accordingly:
### Create a virtual environment in order to make sure requirements has been imported from Command Line Interface (CLI or terminal).
     python -m venv .venv
### Install requirements. ('requirement.txt' provided for used stack). Also, torch library can be need to installed seperately if used script throws an error. 
     pip insrall -r requrements.txt     
*    pip3 install torch torchvision torchaudio
     pip install transformers[torch]
### Then from Command Line Interface (CLI or terminal)
     python <python_script_name>
*data_collection branch demonstrates an example output for a patent data which is extracted from USPTO database.m

# Below Steps have been performed while training model.
## Step 1:
Data collection has been performed from USPTO website. "g_cpc_title.tsv" and "g_detail_desc_text_2024" data gathered. Because of the storage and memory efficiency "g_detail_desc_text_2024" data (approximetely 14.7 gigabyte (GB)) has ben imported to SQLite database via "import_data_db.py". And "output.csv" has been generated in order to demonstrated an patent data as an example to raw dataset via "observe_db.py".
### Run CLI followings
     python import_data_db.py
     python observe_db.py
## Step 2:
Needed to extract cpc codes and keywords related "green" terms. "green_cpc_keywords.json" file has been created via script "pull_green_cpc_codes_and_keywords.py".
### Run CLI followings
     python pull_green_cpc_codes_and_keywords.py
## Step 3:
Granted data (from database g_detail_desc_text_2024 table) has been classified as "green" terms. Imported to database vias 'label_data.py' script.
### Run CLI followings
     python label_data.py
## Step 4:
Selected model has been trained via "train_model_first.py' in order to observe code reliability and process time in local machine. Later in order to train via mass data, 'train_model_all.py' has been used.
### Example a part of training instance in terminal:
***
Map: 100%|████████████████████████████████████████████████████████████████████████████████████████████████████████████████████| 100/100 [00:01<00:00, 88.74 examples/s]
Some weights of DistilBertForSequenceClassification were not initialized from the model checkpoint at distilbert-base-uncased and are newly initialized: ['classifier.bias', 'classifier.weight', 'pre_classifier.bias', 'pre_classifier.weight']
You should probably TRAIN this model on a down-stream task to be able to use it for predictions and inference.
{'eval_loss': 0.09320060908794403, 'eval_runtime': 3.277, 'eval_samples_per_second': 6.103, 'eval_steps_per_second': 0.915, 'epoch': 1.0}
{'eval_loss': 0.03281847760081291, 'eval_runtime': 3.4369, 'eval_samples_per_second': 5.819, 'eval_steps_per_second': 0.873, 'epoch': 2.0}
{'eval_loss': 0.023905927315354347, 'eval_runtime': 3.3778, 'eval_samples_per_second': 5.921, 'eval_steps_per_second': 0.888, 'epoch': 3.0}
{'train_runtime': 204.0818, 'train_samples_per_second': 1.176, 'train_steps_per_second': 0.147, 'train_loss': 0.12877427736918132, 'epoch': 3.0}
100%|██████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████| 30/30 [03:24<00:00,  6.80s/it]
100%|████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████| 3/3 [00:01<00:00,  1.55it/s]
{'eval_loss': 0.023905927315354347, 'eval_runtime': 3.2466, 'eval_samples_per_second': 6.16, 'eval_steps_per_second': 0.924, 'epoch': 3.0}
Model and tokenizer saved in 'green_patent_model_100' directory.
***
### Run CLI followings
     python train_model_all.py

## Step 5:
In order to test model result a script has been created ('test_layout_classify.py'). Random test data has been classified using by the model. In here, results have could be observed by manual approach. Further test implementation; randomly classified data has been tested in the manners of accuracy, precision, recall and F-1 score tests by "test_layout.py".
###
     example result of "test_layout.py" with minimal test data.
          Classification Report:

                        precision    recall  f1-score   support

             Non-Green       0.00      0.00      0.00         6
                 Green       0.40      1.00      0.57         4

              accuracy                           0.40        10
             macro avg       0.20      0.50      0.29        10
          weighted avg       0.16      0.40      0.23        10


          Summary of Results:
          - Total Samples: 10
          - Correctly Classified: 4
          - Incorrectly Classified: 6
     
     with mass test data:
               Classification Report:
          
                        precision    recall  f1-score   support
          
             Non-Green       0.00      0.00      0.00       501
                 Green       0.50      1.00      0.67       499
          
              accuracy                           0.50      1000
             macro avg       0.25      0.50      0.33      1000
          weighted avg       0.25      0.50      0.33      1000
          
          
          Summary of Results:
          - Total Samples: 1000
          - Correctly Classified: 499
          - Incorrectly Classified: 501
          
### Run CLI followings
     python test_layout_classify.py (P.S: you have to return to use this code with new test setup in order to apply via "import_data_db.py". Also, have to update script the table stated in the "import_data_db.py" at line 20(query ~ "SELECT * FROM <your_table_name> LIMIT 10").)
     python test_layout.py