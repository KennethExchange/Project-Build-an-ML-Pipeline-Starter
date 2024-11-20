This documents changes made to the code of project 1 of WGU D501 / Udacity Machine Learning DevOps course:  Project-Build-an-ML-Pipeline-Starter 

############################################################
____________________________________________________________

need to remove $ echo.  Then need to run each command one at a time......

    command: >-
      python main.py {hydra_options}

____________________________________________________________

in eda.ipynb:  Errored out when running second step.  Stated sample.cav_latest\\sample1.csv could not be found.

Changed 

run = wandb.init(project="nyc_airbnb", group="eda", save_code=True)
local_path = wandb.use_artifact("sample.csv:latest").file()
df = pd.read_csv(local_path)

to:

run = wandb.init(project="nyc_airbnb", group="eda", save_code=True)
local_path = wandb.use_artifact("sample.csv:latest").file("sample1.csv")
df = pd.read_csv(local_path)

____________________________________________________________


Ran mlflow with code:  mlflow run . -P steps=basic_cleaning -P hydra_options="modeling.random_seed=42".  
Run errored out:  File "C:\Users\kenne\PycharmProjects\Project-Build-an-ML-Pipeline-Starter\Project-Build-an-ML-Pipeline-Starter\main.py", line 57
    iif "basic_cleaning" in active_steps:


Changed code in line 57 of main.py 
from:
        iif "basic_cleaning" in active_steps:
to:
        if "basic_cleaning" in active_steps:

____________________________________________________________

run errored out again:  

usage: run.py [-h] --input_artifact INPUT_ARTIFACT --output_artifact OUTPUT_ARTIFACT --output_type OUTPUT_TYPE --output_description OUTPUT_DESCRIPTION --min_price MIN_PRICE --max_price MAX_PRICE
run.py: error: unrecognized arguments: with outliers and null values removed"
Error executing job with overrides: ['main.steps=basic_cleaning', 'modeling.random_seed=42']

changed line 65 of main.py
from:
	"output_description": "Data with outliers and null values removed",
to:
	"output_description": "Data_with_outliers_and_null_values_removed",

____________________________________________________________

when running:   mlflow run . -P steps=data_check -P hydra_options="modeling.random_seed=42" 
got error:  

Could not solve for environment specs
The following packages are incompatible
├─ python 3.10.0**  is installable with the potential options
│  ├─ python 3.10.0, which can be installed;
│  └─ python 3.10.0 would require
│     └─ python_abi 3.10.* *_cp310, which can be installed;
└─ scipy 1.5.2**  is not installable because there are no viable options
   ├─ scipy 1.5.2 would require
   │  └─ python >=3.6,<3.7.0a0 , which conflicts with any installable versions previously reported;
   ├─ scipy 1.5.2 would require
   │  ├─ python >=3.6,<3.7.0a0 , which conflicts with any installable versions previously reported;
   │  └─ python_abi 3.6.* *_cp36m, which conflicts with any installable versions previously reported;
   ├─ scipy 1.5.2 would require
   │  ├─ python >=3.7,<3.8.0a0 , which conflicts with any installable versions previously reported;
   │  └─ python_abi 3.7.* *_cp37m, which conflicts with any installable versions previously reported;
   ├─ scipy 1.5.2 would require
   │  ├─ python >=3.8,<3.9.0a0 , which conflicts with any installable versions previously reported;
   │  └─ python_abi 3.8.* *_cp38, which conflicts with any installable versions previously reported;
   ├─ scipy 1.5.2 would require
   │  ├─ python >=3.9,<3.10.0a0 , which conflicts with any installable versions previously reported;
   │  └─ python_abi 3.9.* *_cp39, which conflicts with any installable versions previously reported;
   ├─ scipy 1.5.2 would require
   │  └─ python >=3.7,<3.8.0a0 , which conflicts with any installable versions previously reported;
   ├─ scipy 1.5.2 would require
   │  └─ python >=3.8,<3.9.0a0 , which conflicts with any installable versions previously reported;
   └─ scipy 1.5.2 would require
      └─ python >=3.9,<3.10.0a0 , which conflicts with any installable versions previously reported.

Error executing job with overrides: ['main.steps=data_check', 'modeling.random_seed=42']

Changed data_check.conda.yml dependency for the following items from
	- pytest=6.2.2
	- scipy=1.5.2
to
	- pytest
	- scipy 

____________________________________________________________

when running:  mlflow run . -P steps=data_split -P hydra_options="modeling.random_seed=42"
get error:  PermissionError: [Errno 13] Permission denied: 'C:\\Users\\kenne\\AppData\\Local\\Temp\\tmpjjfryj4l'

temp folder redirected to desktop.
code in components.train_val_test_split/run.py line 40 changed 
from
	with tempfile.NamedTemporaryFile("w") as fp:
to
	with tempfile.NamedTemporaryFile(mode='wb+',
                                         delete=False,
                                         dir='C:\\Users\\kenne\\Desktop\\MLFLOW_TEMP'
                                         ) as fp:

____________________________________________________________


when running command:  
	mlflow run . -P steps=train_random_forest -P hydra_options="modeling.random_forest.max_depth=10,50 modeling.random_forest.n_estimators=100,200 -m"

get error:  
	main.py: error: argument --multirun/-m: ignored explicit argument '"'
	2024/11/19 23:42:39 ERROR mlflow.cli: === Run (ID 'f09adbdbcf00478584b2541f61ab4889') failed ===


Udacity AI suggested using the following command:
	python main.py --multirun "modeling.random_forest.max_depth=10,50" "modeling.random_forest.n_estimators=100,200"

____________________________________________________________

Cannot get multirun to work with hydra_options or mlflow.  Udacity AI suggestions did not work.
When running command:  mlflow run . -P steps=train_random_forest  -P hydra_options= "modeling.random_forest.max_depth=10,50 modeling.random_forest.n_estimators=100,200 -m"                
Get error at -m.  Same when using --multirun.


main.py line 18: command changed from
	command: "python main.py main.steps={steps} {hydra_options}"
to
	command: "python main.py --multirun main.steps=train_random_forest modeling.random_forest.max_depth=10,50 modeling.random_forest.n_estimators=100,200"

terminal command run is 
	mlflow run .                

____________________________________________________________

when running code above get error:  
	usage: run.py [-h] [--trainval_artifact TRAINVAL_ARTIFACT] [--val_size VAL_SIZE] [--random_seed RANDOM_SEED] [--stratify_by STRATIFY_BY] [--rf_config RF_CONFIG]
        [--max_tfidf_features MAX_TFIDF_FEATURES] --output_artifact OUTPUT_ARTIFACT
	run.py: error: the following arguments are required: --output_artifact

Note:  My PyCharm will not read multi-line commands.

changed code in train_random_forest.MLproject from

	command: >-
      python run.py --trainval_artifact {trainval_artifact} \
                    --val_size {val_size} \
                    --random_seed {random_seed} \
                    --stratify_by {stratify_by} \
                    --rf_config {rf_config} \
                    --max_tfidf_features {max_tfidf_features} \
                    --output_artifact {output_artifact}

to
	python run.py --trainval_artifact {trainval_artifact} --val_size {val_size} --random_seed {random_seed} --stratify_by {stratify_by} --rf_config {rf_config} --max_tfidf_features {max_tfidf_features} --output_artifact {output_artifact}

____________________________________________________________


Code would not run with changes above.  
Error stating:  mlflow.exceptions.MlflowException: Unable to map 'object' type to MLflow DataType. object can be mapped iff all values have identical data type which is one of (string, (bytes or byterray),  int, float).

Changed conda.yml.  Removed version dependency from mlflow. 

Changed from:

name: basic_cleaning
channels:
  - conda-forge
  - defaults
dependencies:
  - python=3.10.0
  - hydra-core=1.3.2
  - matplotlib=3.8.2
  - pandas=2.1.3
  - pip=23.3.1
  - scikit-learn=1.3.2
  - pip:
      - mlflow==2.8.1
      - wandb==0.16.0

to:

name: basic_cleaning
channels:
  - conda-forge
  - defaults
dependencies:
  - python=3.10.0
  - hydra-core=1.3.2
  - matplotlib=3.8.2
  - pandas=2.1.3
  - pip=23.3.1
  - scikit-learn=1.3.2
  - pip:
      - mlflow
      - wandb==0.16.0

run successful
____________________________________________________________

after train_random_forest run above:  changed main.py line 18 back to:
	command: "python main.py main.steps={steps} {hydra_options}"

____________________________________________________________


Running command:  
	mlflow run . -P steps=test_regression_model -P hydra_options="modeling.random_seed=42"

Getting error:
	TypeError: ColSpec.__init__() got an unexpected keyword argument 'required'

Changed conda.yml.  Removed version dependency from mlflow. 

Changed from: 

name: test_regression_model
channels:
  - conda-forge
  - defaults
dependencies:
  - python=3.10.0
  - pip=23.3.1
  - requests=2.24.0
  - scikit-learn=1.3.2
  - pandas=2.1.3
  - pip:
      - mlflow==2.8.1
      - wandb==0.16.0
      - git+https://github.com/udacity/Project-Build-an-ML-Pipeline-Starter.git#egg=wandb-utils&subdirectory=components

to:

name: test_regression_model
channels:
  - conda-forge
  - defaults
dependencies:
  - python=3.10.0
  - pip=23.3.1
  - requests=2.24.0
  - scikit-learn=1.3.2
  - pandas=2.1.3
  - pip:
      - mlflow
      - wandb==0.16.0
      - git+https://github.com/udacity/Project-Build-an-ML-Pipeline-Starter.git#egg=wandb-utils&subdirectory=components


run successful.
____________________________________________________________







____________________________________________________________