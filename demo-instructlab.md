# Demo RHEL AI

From https://demo.redhat.com/catalog?search=rhel+ai&item=babylon-catalog-prod%2Frhdp.rhel-ai-vm.prod

InstructLab v0.17.1
Apple M1 Pro
32GB Memory
Sequoia 15.0.1
ssh access

## Chat with the default LLM
1. Initialize ilab environment
1. Review Configurations
1. Review/Download models
1. Serve and Chat with the model

```sh
# Verify iLAB version
ilab --version

# Activate the virtual environment
source instructlab/bin/activate

# Initialize local ilab working directory
ilab config init

# Found config.yaml in the current directory, do you still want to continue? [y/N]:
y

# Path to taxonomy repo [taxonomy]:
<ENTER>

# Path to your model [models/merlinite-7b-lab-Q4_K_M.gguf]:
<ENTER>

# Review downloaded models
# Contains all downloaded large language models, including the saved output of ones you generate with RHEL AI.
tree ./models

# Review skills and knowledge data
tree taxonomy

# Review example of knowledge
cat taxonomy/knowledge/instructlab/overview/qna.yaml

# Review example of compositional skill
cat taxonomy/compositional_skills/linguistics/synonyms/qna.yaml

# Serve the default model
ilab serve --model-family merlinite --model-path models/merlinite-7b-lab-Q4_K_M.gguf

# Open a new terminal tab and activate the virtual environment
source instructlab/bin/activate

# Launch a chat session
ilab model chat --model-family merlinite -m models/merlinite-7b-lab-Q4_K_M.gguf

# Commands:
# /p  - remove ASCII art
# /n  - start a new chat
# /h  - view help commands

# what is openshift in 20 words or less? 
# openshift is red hat's kubernetes-based platform for building, deploying, and scaling containerized 
# applications across multiple clouds and on-premises 
# environments. it simplifies infrastructure management, automates application deployment, and supports a wide 
# range of technologies and services.  

# what is the InstructLab project in 20 words or less?     
# InstructLab is a research initiative that focuses on understanding human learning and teaching processes and designing technology-enhanced learning 
# environments. It involves experts from various fields, such as education, computer science, psychology, neuroscience, and human-computer interaction, to 
# study these issues systematically.

# Exit chat
exit
Ctrl+c
```

## Customizing the LLM
[source](https://developers.redhat.com/learning/learn:rhel:rhel-ai-try-llms-easy-way/resource/resources:optimizing-llms-accuracy)

1. Add new knowledge in a `qna.yaml` - [source](https://github.com/instructlab/taxonomy/blob/main/docs/contributing_via_GH_UI.md)
1. Validate the new knowledge with `ilab diff`
1. Generate synthetic data with `ilab data generate`
1. Train the model `ilab train`
1. Serve the new model
1. Interact with the model

```sh
# create folder in area of knowledge you're contributing
mkdir -p taxonomy/knowledge/instructlab/overview

# Create the qna.yaml file
touch taxonomy/knowledge/instructlab/overview/qna.yaml

# Follow these guidelines https://github.com/instructlab/taxonomy/blob/main/docs/knowledge-contribution-guide.md#verification

# Validate the qna.yaml so synthetic data can be generated from it
ilab diff

# teacher model serves multiple roles: 
# it generates questions, produces responses, and evaluates the quality of both guiding the synthetic data generation process
# we are using Merlinite as the teacher model - Users can use any LLM
ilab serve --model-path models/merlinite-7b-lab-Q4_K_M.gguf

# generate 5 new data points
ilab data generate --num-instructions 5

# INFO 2024-10-15 17:46:22,730 generate_data.py:609: generate_data 6 instructions generated, 9 discarded due to format (see generated/discarded_merlinite-7b-lab-Q4_K_M_2024-10-15T17_43_37.log), 1 discarded due to rouge score
# INFO 2024-10-15 17:46:22,730 generate_data.py:613: generate_data Generation took 165.89s#

# Train the model
ilab train
```
