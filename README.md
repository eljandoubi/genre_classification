# genre_classification


## Table of contents

- [Introduction](#genre_classification)
- [Preliminary steps](#preliminary-steps)
  * [Clone repository](clone-repository)
  * [Create environment](#create-environment)
  * [Get API key for Weights and Biases](#get-api-key-for-weights-and-biases)
  * [The configuration](#the-configuration)
  * [Running the entire pipeline or just a selection of steps](#Running-the-entire-pipeline-or-just-a-selection-of-steps)
- [Licence](#license)
 
## Preliminary steps
### Clone repository

Clone the repository locally so you can start working on it:

```
git clone https://github.com/eljandoubi/genre_classification.git
```

and go into the repository:

```
cd genre_classification
```

### Create environment
Make sure to have conda installed and ready, then create a new environment using the ``environment.yml``
file provided in the root of the repository and activate it:

```bash
> conda env create -f environment.yml
> conda activate genre_classification
```

### Get API key for Weights and Biases
Let's make sure we are logged in to Weights & Biases. Get your API key from W&B by going to 
[https://wandb.ai/authorize](https://wandb.ai/authorize) and click on the + icon (copy to clipboard), 
then paste your key into this command:

```bash
> wandb login [your API key]
```

You should see a message similar to:
```
wandb: Appending key for api.wandb.ai to your netrc file: /home/[your username]/.netrc
```
### The configuration
As usual, the parameters controlling the pipeline are defined in the ``config.yaml`` file defined in
the root. We will use Hydra to manage this configuration file. 
Open this file and get familiar with its content. Remember: this file is only read by the ``main.py`` script 
(i.e., the pipeline) and its content is
available with the ``go`` function in ``main.py`` as the ``config`` dictionary. For example,
the name of the project is contained in the ``project_name`` key under the ``main`` section in
the configuration file. It can be accessed from the ``go`` function as 
``config["main"]["project_name"]``.

NOTE: do NOT hardcode any parameter when writing the pipeline. All the parameters should be 
accessed from the configuration file.

### Running the entire pipeline or just a selection of steps
In order to run the pipeline when you are developing, you need to be in the root of the starter kit, 
then you can execute as usual:

```bash
>  mlflow run .
```
This will run the entire pipeline.

When developing it is useful to be able to run one step at the time. Say you want to run only
the ``download`` step. The `main.py` is written so that the steps are defined at the top of the file, in the 
``_steps`` list, and can be selected by using the `steps` parameter on the command line:

```bash
> mlflow run . -P steps=download
```
If you want to run the ``download`` and the ``basic_cleaning`` steps, you can similarly do:
```bash
> mlflow run . -P steps=download,preprocess
```
You can override any other parameter in the configuration file using the Hydra syntax, by
providing it as a ``hydra_options`` parameter. For example, say that we want to set the parameter
random_forest_pipeline -> random_forest -> n_estimators to 10:

```bash
> mlflow run . \
  -P hydra_options="random_forest_pipeline.random_forest.n_estimators=10"
```

In order to realize parallel hyperparmeter optimization, you execute:

```bash
> mlflow run . \
  -P hydra_options="-m random_forest_pipeline.random_forest.max_depth=range(10,50,3) random_forest_pipeline.tfidf.max_features=range(50,200,50) hydra/launcher=joblib"
```
## License
Distributed under the [Apache License 2.0](https://www.apache.org/licenses/LICENSE-2.0.txt) License. See ```LICENSE``` for more information.
