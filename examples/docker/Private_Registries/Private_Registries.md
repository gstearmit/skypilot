# https://skypilot.readthedocs.io/en/latest/examples/docker-containers.html
# Private Registries
Private Registries
When using this mode, to access Docker images hosted on private registries, simply add a setup section to your task YAML file to authenticate with the registry:

resources:
  accelerators: A100:1

setup: |
  # Authenticate with private registry
  docker login -u <username> -p <password> <registry>

run: |
  docker run <registry>/<image>:<tag>