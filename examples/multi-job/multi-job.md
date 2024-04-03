in file yml how to set 2 task
In a YAML file, you can define a pipeline with multiple tasks. Each task has its own resources, setup, and run sections. The tasks are separated by a line with three dashes ---. Here is an example:

name: pipeline
resources:
  # resources for the first task
setup: |
  # setup commands for the first task
run: |
  # run commands for the first task
---
name: eval
resources:
  # resources for the second task
setup: |
  # setup commands for the second task
run: |
  # run commands for the second task
In this example, the first task is named pipeline and the second task is named eval. The setup and run sections are executed sequentially for each task.
To submit the pipeline, you can use the sky spot launch command with the name of your pipeline and the YAML file as arguments, like so:

$ sky spot launch -n pipeline pipeline.yaml
The pipeline will be automatically launched and monitored by SkyPilot. You can check the status of the pipeline with sky spot queue or sky spot dashboard.
For more details, you can refer to the SkyPilot documentation.
Answer based on the following sources:
skypilot.readthedocs.io/en
Spot Pipeline
Managed Spot Jobs
in file yml how to process 2 task parale
To run multiple tasks in parallel, you can use SkyPilot's job queue feature. Each task submitted by sky exec is automatically queued and scheduled for execution on an existing cluster. Here's an example of how you can launch the same job 5 times:

# Launch the job 5 times.
sky exec mycluster task.yaml -d
sky exec mycluster task.yaml -d
sky exec mycluster task.yaml -d
sky exec mycluster task.yaml -d
sky exec mycluster task.yaml -d
In this example, the -d / --detach flag detaches logging from the terminal, which is useful for launching many long-running jobs concurrently.
If you want to run different tasks in parallel, you can do so by executing different task YAML files:

sky exec mycluster task1.yaml -d
sky exec mycluster task2.yaml -d
In this case, task1.yaml and task2.yaml are different task YAML files.
For more details, you can refer to the SkyPilot documentation.
Answer based on the following sources:
skypilot.readthedocs.io/en
Job Queue
2 setup and 2 run How ?
In a YAML file, you can define multiple tasks each with its own setup and run sections. The tasks are separated by a line with three dashes ---. Here is an example:

name: task1
resources:
  # resources for the first task
setup: |
  # setup commands for the first task
run: |
  # run commands for the first task
---
name: task2
resources:
  # resources for the second task
setup: |
  # setup commands for the second task
run: |
  # run commands for the second task
In this example, the first task is named task1 and the second task is named task2. The setup and run sections are executed sequentially for each task.
To submit the tasks, you can use the sky spot launch command with the name of your tasks and the YAML file as arguments, like so:

$ sky spot launch -n task1 task1.yaml
$ sky spot launch -n task2 task2.yaml
The tasks will be automatically launched and monitored by SkyPilot. You can check the status of the tasks with sky spot queue or sky spot dashboard.
For more details, you can refer to the SkyPilot documentation.
Answer based on the following sources:
skypilot.readthedocs.io/en
Spot Pipeline
Managed Spot Jobs
no i want keep 1 resources ? write fixed update in file yml how?
If you want to use the same resources for multiple tasks but have different setup and run commands, you can define them in the same YAML file. However, you would need to run them as separate commands. Here's an example:

resources:
  # resources for both tasks

setup: |
  # setup commands for the first task
run: |
  # run commands for the first task
And another YAML file:

resources:
  # resources for both tasks

setup: |
  # setup commands for the second task
run: |
  # run commands for the second task
In this example, both tasks use the same resources but have different setup and run commands. You would need to run each task separately with the sky spot launch command:

$ sky spot launch -n task1 task1.yaml
$ sky spot launch -n task2 task2.yaml
Please note that SkyPilot currently does not support running multiple setup and run commands under the same resources in a single YAML file. Each setup and run pair needs to be in a separate YAML file.
For more details, you can refer to the SkyPilot documentation.