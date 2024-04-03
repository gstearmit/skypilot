https://skypilot.readthedocs.io/en/latest/examples/ports.html

# Network interface  open port how?
To open a port on a SkyPilot cluster, you need to specify ports in the resources section of your task. Here is an example of a YAML configuration to expose a Jupyter Lab server:

# jupyter_lab.yaml
resources:
 ports: 8888

setup: pip install jupyter

run: jupyter lab --port 8888 --no-browser --ip=0.0.0.0
In this example, the run command will start the Jupyter Lab server on port 8888. By specifying ports: 8888, SkyPilot will expose port 8888 on the cluster, making the jupyter server publicly accessible. To launch and access the server, run:

$ sky launch -c jupyter jupyter_lab.yaml
To get the public IP address of the head node of the cluster, run sky status --ip jupyter:

$ sky status --ip jupyter
35.223.97.21
In the jupyter server URL, replace 127.0.0.1 with the public IP from sky status --ip jupyter and open the URL in your browser.
If you want to expose multiple ports, you can specify a list of ports or port ranges in the resources section:

resources:
 ports:
 - 8888
 - 10020-10040
 - 20000-20010
SkyPilot also supports opening ports through the CLI:

$ sky launch -c jupyter --ports 8888 jupyter_lab.yaml
Please note that the ports you open are exposed to the public internet. Anyone who knows your VM’s IP address and the opened port can access your service. Ensure you use security measures, like authentication mechanisms, to protect your services (source).