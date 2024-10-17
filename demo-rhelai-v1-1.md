# Demo RHEL AI

From https://demo.redhat.com/catalog?search=rhel+ai&item=babylon-catalog-prod%2Frhdp.rhel-ai-vm.prod

InstructLab v0.18.4
RHEL AI (GA)
Nvidia L4 (24GB) GPU (g6.xlarge)
Optional 4 x L4 via dropdown (g6.12xlarge)
ssh access

# Chat with default LLMs
1. Initialize ilab environment
1. Review Configurations
1. Review/Download models
1. Serve and Chat with the model

```sh
# Verify iLAB version
ilab --version

# Activate the virtual environment
source instructlab/bin/activate

# Review the config.yaml
ilab config show

# You can edit the config.yaml
#ilab config edit

# Initialize local ilab working directory
ilab config init

# Found /var/home/instruct/.config/instructlab/config.yaml, do you still want to continue? [y/N]:
y

# Found /var/home/instruct/.local/share/instructlab/internal/train_configuration/profiles, do you also want to reset existing profiles? [y/N]: 
y

# Generating `/var/home/instruct/.config/instructlab/config.yaml` and `/var/home/instruct/.local/share/instructlab/internal/train_configuration/profiles`...
# Please choose a train profile to use:
6

# Review the github 
# https://github.com/RedHatOfficial/rhelai-sample-taxonomy/blob/main/knowledge/science/astronomy/constellations/phoenix/qna.yaml

# Review downloaded models
# Contains all downloaded large language models, including the saved output of ones you generate with RHEL AI.
ls -la ~/.cache/instructlab/models/
ilab model list

# Review generated data
# Contains data output from the SDG phase, built on modifications to the taxonomy repository.
ls -la ~/.local/share/instructlab/datasets/

# Review skills and knowledge data
ls -la ~/.local/share/instructlab/taxonomy/

# Login into registry.redhat.io
# https://access.redhat.com/RegistryAuthentication
podman login registry.redhat.io

# Review available models
podman search registry.redhat.io/rhelai1/

# Required models

# The granite-7b-redhat-lab default in the config.yaml
#ilab model download --repository docker://registry.redhat.io/rhelai1/granite-7b-redhat-lab --release latest

# The granite-7b-starter base LLM
#ilab model download --repository docker://registry.redhat.io/rhelai1/granite-7b-starter --release latest

# The mixtral-8x7b-instruct-v0-1 teacher model for SDG
#ilab model download --repository docker://registry.redhat.io/rhelai1/mixtral-8x7b-instruct-v0-1 --release latest

# The prometheus-8x7b-v2-0 judge model for training and evaluation
#ilab model download --repository docker://registry.redhat.io/rhelai1/prometheus-8x7b-v2-0 --release latest

# Required tools for customizing an LLM
# skills-adapter-v3 LoRA layered skills adapter for SDG.

# knowledge-adapter-v3 LoRA layered knowledge adapter for SDG.

# Serve the default model granite-7b-redhat-lab - you must download granite-7b-redhat-lab
ilab model serve

# Serve a different model
ilab model serve --model-path ~/.cache/instructlab/models/granite-7b-starter/

# You can set up a systemd service so that the ilab model serve command runs as a running service. The systemd service runs the ilab model serve command in the background and restarts if it crashes or fails. You can configure the service to start upon system boot.

# Once you serve your model, you can now chat with the model in a new terminal - default expects granite-7b-redhat-lab
ilab model chat

# Chat with a different model served
ilab model chat --model ~/.cache/instructlab/models/granite-7b-starter
```

## Optional: Running ilab model serve as a service 

The systemd service runs the ilab model serve command in the background and restarts if it crashes or fails. [source](https://docs.redhat.com/en/documentation/red_hat_enterprise_linux_ai/1.1/html/building_your_rhel_ai_environment/serving_and_chatting#creating_systemd_serving)

## Optional: Creating an API key for model chatting 

By default, the ilab CLI does not use authentication. If you want to expose your server to the internet, you can create a vLLM API key that connects to your server with the following procedures. [source](https://docs.redhat.com/en/documentation/red_hat_enterprise_linux_ai/1.1/html/building_your_rhel_ai_environment/serving_and_chatting#creating_api_key)

## Optional: Allowing chat access to a model from a secure endpoint 

You can serve an inference endpoint and allow others to interact with models provided with Red Hat Enterprise Linux AI on secure connections by creating a systemd service and setting up a nginx reverse proxy that exposes a secure endpoint. This allows you to share the secure endpoint with others so they can chat with the model over a network. [source](https://docs.redhat.com/en/documentation/red_hat_enterprise_linux_ai/1.1/html/building_your_rhel_ai_environment/serving_and_chatting#creating_secure_endpoint)
