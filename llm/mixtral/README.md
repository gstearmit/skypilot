#  tail -n100 -f /home/gstearmit/sky_logs/sky-2024-04-01-13-47-25-185514/provision.log
# sky show-gpus
# https://cloud.google.com/compute/docs/gpus/gpu-regions-zones 

https://skypilot.readthedocs.io/en/latest/cloud-setup/quota.html


tail -n100 -f /home/gstearmit/sky_logs/sky-2024-04-01-14-58-59-154274/provision.log

# 
#
# To activate this environment, use
#
#     $ conda activate mixtral
#
# To deactivate an active environment, use
#
#     $ conda deactivate

#-----------------------------
Shared connection to 34.170.28.113 closed.
I 04-01 15:16:37 cloud_vm_ray_backend.py:3214] Job ID: 1
I 04-01 15:16:37 cloud_vm_ray_backend.py:3214] To cancel the job:       sky cancel mixtral-02 1
I 04-01 15:16:37 cloud_vm_ray_backend.py:3214] To stream job logs:      sky logs mixtral-02 1
I 04-01 15:16:37 cloud_vm_ray_backend.py:3214] To view the job queue:   sky queue mixtral-02
I 04-01 15:16:37 cloud_vm_ray_backend.py:3310]
I 04-01 15:16:37 cloud_vm_ray_backend.py:3310] Cluster name: mixtral-02
I 04-01 15:16:37 cloud_vm_ray_backend.py:3310] To log into the head VM: ssh mixtral-02
I 04-01 15:16:37 cloud_vm_ray_backend.py:3310] To submit a job:         sky exec mixtral-02 yaml_file
I 04-01 15:16:37 cloud_vm_ray_backend.py:3310] To stop the cluster:     sky stop mixtral-02
I 04-01 15:16:37 cloud_vm_ray_backend.py:3310] To teardown the cluster: sky down mixtral-02

#------------list port all -------------------

netstat -tulpn | grep LISTEN

https://www.cyberciti.biz/faq/unix-linux-check-if-port-is-in-use-command/


OR filter out specific TCP port such as 443:
netstat -tulpn | grep ':443

netstat -tulpn | grep ':8080

# -----------------------
https://console.cloud.google.com/networking/networkinterfaces/zones/us-central1-a/instances/design2code-0cf3-head-b9ln0xk1-compute?networkInterface=nic0&project=llmsmixtral8x7b-417610&tab=APPLICABLE_FIREWALL_POLICIES&analysisTab=CONNECTIVITY_TEST

telnet 35.232.224.50 7001

# -----------How to open a specific port such as 9090 in Google Compute Engine-------------
https://stackoverflow.com/questions/21065922/how-to-open-a-specific-port-such-as-9090-in-google-compute-engine

gcutil addfirewall allow-7001 --allowed=tcp:7001

gcloud compute --project=<project_name> firewall-rules create firewall-rules --direction=INGRESS --priority=1000 --network=default --action=ALLOW --rules=tcp:<port number> --source-ranges=0.0.0.0/0

gcloud compute --project=llmsmixtral8x7b-417610 firewall-rules create firewall-rules --direction=INGRESS --priority=1000 --network=default --action=ALLOW --rules=tcp:7001 --source-ranges=0.0.0.0/0

gcloud compute --project=llmsmixtral8x7b-417610 firewall-rules create firewall-rules-5173 --direction=INGRESS --priority=1001 --network=default --action=ALLOW --rules=tcp:5173 --source-ranges=0.0.0.0/0

gcloud compute --project=llmsmixtral8x7b-417610 firewall-rules create default-allow-http-7001  --direction=INGRESS --priority=1004 --network=default --action=ALLOW  --rules=tcp:7001 --source-ranges=0.0.0.0/0 --target-tags=http-server

gcloud compute --project=llmsmixtral8x7b-417610 firewall-rules create default-allow-http-5173  --direction=INGRESS --priority=1005 --network=default --action=ALLOW  --rules=tcp:5173 --source-ranges=0.0.0.0/0 --target-tags=http-server

IP local : 171.246.8.61


#-------check firewall-rules -----
 
https://console.cloud.google.com/net-security/firewall-manager/firewall-policies/list?project=llmsmixtral8x7b-417610
#----------------------
Extra
$ find frontend/ -type f -print -exec chmod 644 {} \;
$ find frontend/ -type d -print -exec chmod 755 {} \;
$ chmod frontend/ 755

#---------Quick tour: LLM serving-----------------

https://skypilot.readthedocs.io/en/latest/serving/sky-serve.html


# Serving Mixtral from Mistral.ai

Mistral AI released Mixtral 8x7B, a high-quality sparse mixture of experts model (SMoE) with open weights. Mixtral outperforms Llama 2 70B on most benchmarks with 6x faster inference. Mistral.ai uses SkyPilot as [the default way](https://docs.mistral.ai/self-deployment/skypilot) to distribute their new model. This folder contains the code to serve Mixtral on any cloud with SkyPilot. 

There are three ways to serve the model:

## 1. Serve with a single instance

SkyPilot can help you serve Mixtral by automatically finding available resources on any cloud, provisioning the VM, opening the ports, and serving the model. To serve Mixtral with a single instance, run the following command:

```bash
sky launch -c mixtral ./serve.yaml
```

Note that we specify the following resources, so that SkyPilot will automatically find any of the available GPUs specified by automatically [failover](https://skypilot.readthedocs.io/en/latest/examples/auto-failover.html) through all the candidates (in the order of the prices):

```yaml
resources:
  accelerators: {A100:4, A100:8, A100-80GB:2, A100-80GB:4, A100-80GB:8}
```

The following is the example output of the optimizer:

```
Considered resources (1 node):
----------------------------------------------------------------------------------------------------------
 CLOUD   INSTANCE                    vCPUs   Mem(GB)   ACCELERATORS   REGION/ZONE     COST ($)   CHOSEN   
----------------------------------------------------------------------------------------------------------
 Azure   Standard_NC48ads_A100_v4    48      440       A100-80GB:2    eastus          7.35          ✔     
 GCP     g2-standard-96              96      384       L4:8           us-east4-a      7.98                
 GCP     a2-ultragpu-2g              24      340       A100-80GB:2    us-central1-a   10.06               
 Azure   Standard_NC96ads_A100_v4    96      880       A100-80GB:4    eastus          14.69               
 GCP     a2-highgpu-4g               48      340       A100:4         us-central1-a   14.69               
 AWS     g5.48xlarge                 192     768       A10G:8         us-east-1       16.29               
 GCP     a2-ultragpu-4g              48      680       A100-80GB:4    us-central1-a   20.11               
 Azure   Standard_ND96asr_v4         96      900       A100:8         eastus          27.20               
 GCP     a2-highgpu-8g               96      680       A100:8         us-central1-a   29.39               
 Azure   Standard_ND96amsr_A100_v4   96      1924      A100-80GB:8    eastus          32.77               
 AWS     p4d.24xlarge                96      1152      A100:8         us-east-1       32.77               
 GCP     a2-ultragpu-8g              96      1360      A100-80GB:8    us-central1-a   40.22               
 AWS     p4de.24xlarge               96      1152      A100-80GB:8    us-east-1       40.97               
----------------------------------------------------------------------------------------------------------
```


python -u -m vllm.entrypoints.openai.api_server \
                --host 0.0.0.0 \
                --model mistralai/Mixtral-8x7B-Instruct-v0.1 \
                --tensor-parallel-size 1 | tee ~/openai_api_server.log
				
sky status --ip mixtral-02				IP : 34.170.28.113


### Accessing the model

We can now access the model through the OpenAI API with the IP and port:

```bash
IP=$(sky status --ip mixtral-02)

curl -L http://$IP:8000/v1/completions \
  -H "Content-Type: application/json" \
  -d '{
      "model": "mistralai/Mixtral-8x7B-Instruct-v0.1",
      "prompt": "My favourite condiment is",
      "max_tokens": 25
  }'
```

Chat API is also supported:
```bash
IP=$(sky status --ip mixtral-02)

curl -L http://$IP:8000/v1/chat/completions \
  -H "Content-Type: application/json" \
  -d '{
      "model": "mistralai/Mixtral-8x7B-Instruct-v0.1",
      "messages": [
        {
          "role": "user",
          "content": "Hello! What is your name?"
        }
      ],
      "max_tokens": 25
  }'
```

## 2. Serve with multiple instances

When scaling up is required, [SkyServe](https://skypilot.readthedocs.io/en/latest/serving/sky-serve.html) is the library built on top of SkyPilot, which can help you scale up the serving with multiple instances, while still providing a single endpoint. To serve Mixtral with multiple instances, run the following command:

```bash
sky serve up -n mixtral ./serve.yaml
```

The additional arguments for serving specifies the way to check the healthiness of the service and manage the auto-restart of the service when unexpected failure happens:
```yaml
service:
  readiness_probe:
    path: /v1/chat/completions
    post_data:
      model: mistralai/Mixtral-8x7B-Instruct-v0.1
      messages:
        - role: user
          content: Hello! What is your name?
      max_tokens: 1
    initial_delay_seconds: 1200
  replica_policy:
    min_replicas: 1
```

Optional: To further save the cost by 3-4x, we can use the spot instances as the replicas, and SkyServe will automatically manage the spot instances, monitor the prices and preemptions, and restart the replica when needed.
To do so, we can add `use_spot: true` to the `resources` field, i.e.:
```yaml
resources:
  use_spot: true
  accelerators: {A100:4, A100:8, A100-80GB:2, A100-80GB:4, A100-80GB:8}
```

### Accessing the model

After the `sky serve up` command, there will be a single endpoint for the service. We can access the model through the OpenAI API with the IP and port:

```bash
ENDPOINT=$(sky serve status --endpoint mixtral)

curl -L http://$ENDPOINT/v1/completions \
  -H "Content-Type: application/json" \
  -d '{
      "model": "mistralai/Mixtral-8x7B-Instruct-v0.1",
      "prompt": "My favourite condiment is",
      "max_tokens": 25
  }'
```

Chat API is also supported:
```bash
ENDPOINT=$(sky serve status --endpoint mixtral)

curl -L http://$ENDPOINT/v1/chat/completions \
  -H "Content-Type: application/json" \
  -d '{
      "model": "mistralai/Mixtral-8x7B-Instruct-v0.1",
      "messages": [
        {
          "role": "user",
          "content": "Hello! What is your name?"
        }
      ],
      "max_tokens": 25
  }'
```

## 3. Official guide from Mistral AI

Mistral.ai also includes a guide for launching the Mixtral 8x7B model with SkyPilot in their official doc. Please refer to [this link](https://docs.mistral.ai/self-deployment/skypilot) for more details.

> Note: the docker image of the official doc may not be updated yet, which can cause a failure where vLLM is complaining about the missing support for the model. Please feel free to create a new docker image with the setup commands in our [serve.yaml](./serve.yaml) file instead.


#-----------------$ sky exec mixtral-02 serve.yaml---------------------
(task, pid=13917) INFO 04-01 08:46:02 llm_engine.py:79] Initializing an LLM engine with config: model='mistralai/Mixtral-8x7B-Instruct-v0.1', tokenizer='mistralai/Mixtral-8x7B-Instruct-v0.1', tokenizer_mode=auto, revision=None, tokenizer_revision=None, trust_remote_code=False, dtype=torch.bfloat16, max_seq_len=32768, download_dir=None, load_format=auto, tensor_parallel_size=1, disable_custom_all_reduce=False, quantization=None, enforce_eager=False, kv_cache_dtype=auto, device_config=cuda, seed=0)
(task, pid=13917) Traceback (most recent call last):
(task, pid=13917)   File "/opt/conda/envs/mixtral/lib/python3.10/runpy.py", line 196, in _run_module_as_main
(task, pid=13917)     return _run_code(code, main_globals, None,
(task, pid=13917)   File "/opt/conda/envs/mixtral/lib/python3.10/runpy.py", line 86, in _run_code
(task, pid=13917)     exec(code, run_globals)
(task, pid=13917)   File "/opt/conda/envs/mixtral/lib/python3.10/site-packages/vllm/entrypoints/openai/api_server.py", line 237, in <module>
(task, pid=13917)     engine = AsyncLLMEngine.from_engine_args(engine_args)
(task, pid=13917)   File "/opt/conda/envs/mixtral/lib/python3.10/site-packages/vllm/engine/async_llm_engine.py", line 625, in from_engine_args
(task, pid=13917)     engine = cls(parallel_config.worker_use_ray,
(task, pid=13917)   File "/opt/conda/envs/mixtral/lib/python3.10/site-packages/vllm/engine/async_llm_engine.py", line 321, in __init__
(task, pid=13917)     self.engine = self._init_engine(*args, **kwargs)
(task, pid=13917)   File "/opt/conda/envs/mixtral/lib/python3.10/site-packages/vllm/engine/async_llm_engine.py", line 366, in _init_engine
(task, pid=13917)     return engine_class(*args, **kwargs)
(task, pid=13917)   File "/opt/conda/envs/mixtral/lib/python3.10/site-packages/vllm/engine/llm_engine.py", line 120, in __init__
(task, pid=13917)     self._init_workers()
(task, pid=13917)   File "/opt/conda/envs/mixtral/lib/python3.10/site-packages/vllm/engine/llm_engine.py", line 163, in _init_workers
(task, pid=13917)     self._run_workers("init_model")
(task, pid=13917)   File "/opt/conda/envs/mixtral/lib/python3.10/site-packages/vllm/engine/llm_engine.py", line 1014, in _run_workers
(task, pid=13917)     driver_worker_output = getattr(self.driver_worker,
(task, pid=13917)   File "/opt/conda/envs/mixtral/lib/python3.10/site-packages/vllm/worker/worker.py", line 87, in init_model
(task, pid=13917)     _check_if_gpu_supports_dtype(self.model_config.dtype)
(task, pid=13917)   File "/opt/conda/envs/mixtral/lib/python3.10/site-packages/vllm/worker/worker.py", line 300, in _check_if_gpu_supports_dtype
(task, pid=13917)     raise ValueError(
(task, pid=13917) ValueError: Bfloat16 is only supported on GPUs with compute capability of at least 8.0. Your Tesla V100-SXM2-16GB GPU has compute capability 7.0. You can use float16 instead by explicitly setting the`dtype` flag in CLI, for example: --dtype=half.
INFO: Job finished (status: SUCCEEDED).