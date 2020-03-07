# Steps to follow to setup your chatbot


## Setup and installation

Firstly install miniconda from here (https://docs.conda.io/en/latest/miniconda.html)

Setup an environment in miniconda command prompt:
```
conda create -n rasa python=3.6
conda activate rasa
```
Then run the following commands in miniconda command prompt:
```
pip install -r requirements.txt
```

Install a spaCy English language model. You can install it by running:

```
python -m spacy download en
```

### Files for Rasa NLU model

- **data/nlu_data.md** file contents training data for the NLU model.
	
- **nlu_config.yml** file contains the configuration of the Rasa NLU pipeline:  
```yaml
language: "en"

pipeline: "spacy_sklearn"
```	

### Files for Rasa Core model

- **data/stories.md** file contains some training stories which represent the conversations between a user and the assistant. 
- **domain.yml** file describes the domain of the assistant which includes intents, entities, slots, templates and actions the assistant should be aware of.  
- **actions.py** file contains the code of a custom action which retrieves results of the latest IPL match by making an external API call.
- **endpoints.yml** file contains the webhook configuration for custom action.  
- **policies.yml** file contains the configuration of the training policies for Rasa Core model.

## How to run locally

**Note**: If running on Windows, you will either have to [install make](http://gnuwin32.sourceforge.net/packages/make.htm) or copy the following commands from the [Makefile](https://github.com/RasaHQ/starter-pack-rasa-stack/blob/master/Makefile)

1. You can train the Rasa NLU model by running:  
```make train-nlu```
[Windows] ```python -m rasa_nlu.train -c nlu_config.yml --data data/nlu_data.md -o models --fixed_model_name nlu --project current --verbose```  
This will train the Rasa NLU model and store it inside the `/models/current/nlu` folder of your project directory.

To generate a graph type following in terminal:

```python -m rasa_core.visualize -d domain.yml -s data/stories.md -o graph.html```

2. Train the Rasa Core model by running:  
```make train-core```  
[Windows] ```python -m rasa_core.train -d domain.yml -s data/stories.md -o models/current/dialogue -c policies.yml``` 
This will train the Rasa Core model and store it inside the `/models/current/dialogue` folder of your project directory.

3. Predicting the intent:
Run the following code in python:
```
>>> from rasa_nlu.model import Interpreter
>>> nlu_model = Interpreter.load('./models/current/nlu')
>>> nlu_model.parse('Hi')
>>> nlu_model.parse('what is happening in the cricket world these days?')
>>> nlu_model.parse('Bye')
```

4. Test the assistant by running:  
```make cmdline```  
[Windows] ```python -m rasa_core.run -d models/current/dialogue -u models/current/nlu --endpoints endpoints.yml```  
This will load the assistant in your terminal for you to chat.

